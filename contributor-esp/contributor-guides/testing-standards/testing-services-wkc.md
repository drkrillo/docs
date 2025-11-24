# Probar Servicios (Well-Known-Component)

Los servicios well known component son servidores construidos con la arquitectura [well known component](https://well-known-components.github.io/documentation/), donde cada parte del servicio está modularizada y encapsulada en un componente. Estos componentes, junto con la capacidad de ser fácilmente intercambiados, tienen la capacidad de ser fácilmente probados unitariamente, ya que todas sus dependencias son inyectadas cuando se crean.

Probar estos servicios DEBERÍA hacerse usando dos tipos diferentes de prueba, **unitaria** e **de integración**. Los componentes que contienen lógica DEBEN ser probados usando pruebas unitarias y los componentes que tienen principalmente interacciones con servicios externos DEBERÍAN ser probados usando pruebas de integración. Es decir, un componente adaptador de base de datos que no contiene ninguna lógica y contiene principalmente consultas de base de datos DEBE ser probado integrativamente.

## Pruebas unitarias de componentes well known component

Todos los componentes DEBEN ser probados unitariamente y DEBEN escribirse siguiendo la sección definida aquí sobre cómo escribir pruebas.

Para mostrar cómo estos componentes son probados, aquí hay un componente simple con solo un método y dos dependencias, **settings** y **friends**:

```tsx
// Componente Voice

export function createVoiceComponent(dependencies: Pick<AppComponents, 'settings' | 'friends'>) {
	const { settings, friends } = dependencies

  async function canCallEachOther(caller: string, callee: string): Promise<boolean> {
	  const [callerSettings, calleeSetting] = Promise.all([settings.getPrivacySetting(caller), settings.getPrivacySetting(callee)])
	  const areFriends = await friends.areUsersFriends(caller, callee)
		return areFriends || (callerSettings !== Settings.ONLY_FRIENDS && calleeSettings !== Settings.ONLY_FRIENDS)
  }
  
  return {
	  createVoiceChat
  }
}
```

Para probar este componente, se deben construir mocks apropiados, es decir, mocks que tengan los tipos correctos para interactuar con el componente a probar y que sean flexibles para permitirnos crear todos los tipos de pruebas. Estos mocks también deberían ser inmutables entre pruebas, preservando la idea de que cada prueba se ejecuta en su propio contexto, aislada de las demás.

Estos mocks DEBERÍAN crearse usando funciones creadoras de componentes mock y DEBERÍAN nombrarse como create**ComponentName**MockedComponent. Tomando como ejemplo el componente creado arriba, aquí hay un ejemplo de cómo DEBERÍAN verse:

```tsx
// Colocado en el archivo /test/mocks/settings.ts
export function createSettingsMockedComponent(overrides?: Partial<jest.Mocked<ISettingsComponent>>): jest.Mocked<ISettingsComponent> {
  return {
    getPrivacySettings: overrides?.getPrivacySettings ?? jest.fn()
  }
}

// Colocado en el archivo test/mocks/friends.ts
export function createFriendsMockedComponent(overrides?: Partial<jest.Mocked<IFriendsComponent>>): jest.Mocked<IFriendsComponent> {
	return {
	  // Tenemos la flexibilidad de definir comportamiento sobre cualquiera de los métodos exportados por el componente.
	  areUsersFriends: overrides?.areUsersFriends ?? jest.fn(),
	  // Los métodos que no se usan pueden evitarse en el parámetro overrides, convirtiéndolos en un simple mock de función.
	  hasBlockedUser: overrides?.hasBlockedUser ?? jest.fn()
	}
}
```

Al usar estos mocks cuidadosamente colocados dentro de un `beforeEach`, podemos estar seguros de que los mocks están definidos correctamente aislados de diferentes ejecuciones. Aquí hay un ejemplo de cómo estos mocks pueden usarse:

```tsx
// Define las funciones mockeadas en el contexto para que podamos cambiar su mock.
let getPrivacySettingsMock: jest.MockedFn<ISettingsComponent['getPrivacySettings']>
let areUsersFriendsMock: jest.MockedFn<IFriendsComponent['areUsersFriends']>
let voice: IVoiceComponent

beforeEach({
  getPrivacySettingsMock = jest.fn()
  areUsersFriendsMock = jest.fn()
  // Construye el mock
  const settings = createSettingsMockedComponent({ getPrivacySettings: getPrivacySettingsMock })
  // Inicializa el mock del componente solo con los métodos que se van a usar en las pruebas
  const friends = createFriendsMockedComponent({ areUsersFriends: areUsersFriendsMock })
  // Crea el componente a probar usando los mocks
  voice = createVoiceComponent({ settings, friends })
})
```

Esta forma de definir los mocks no solo nos permite aislar correctamente los contextos de las pruebas entre ejecuciones, sino que también construye las bases para una inicialización de contexto apropiada, ya que al tener las funciones mockeadas ya definidas, es bastante fácil construir cada contexto:

```tsx
// Definido después del bloque de código anterior.

describe('when checking if two users can call each other', () => {
	const calleeAddress = '0xd7D746d39D142b6bE752efd7626cE28F245a25D1'
	const callerAddress = '0x2e8b4De1230f827082202aa53d489A26163aace0'

  describe('and the callee only accepts calls from friends', () => {
    beforeEach(() => {
      // Define el mock para el contexto, haciendo que el callee 
      getPrivacySettingsMock.mockImplementation((address: string) => {
	      switch(address) {
	        case caleeAddress:
		        return Settings.ONLY_FRIENDS
		      case callerAddress:
			      return Settings.ALL
			    default:
				    throw new Error("Wrong mock")
	      }
      })
    })
    
    describe('and the calle and the caller are friends', () => {
	    beforeEach(() => {
		    areUsersFriendsMock.mockResolvedValueOnce(true)
	    })
	    
	    it('should resolve to true', () => {
		    return expect(voice.canCallEachOther(callerAddress, calleeAddress)).resolves.toBe(true)
	    })
    })
    
    describe('and the callee and the caller are not friends', () =>{
		  beforeEach(() => {
		    areUsersFriendsMock.mockResolvedValueOnce(false)
	    })
	    
	    it('should resolve to false', () => {
		    return expect(voice.canCallEachOther(callerAddress, calleeAddress)).resolves.toBe(false)
	    })
    })
  })
  
  //... otros contextos
})
```

## Pruebas de integración

Todos los endpoints, llamadas WS RPC, jobs o tareas DEBEN ser probados integrativamente y DEBEN escribirse siguiendo la sección definida aquí sobre cómo escribir pruebas. Las pruebas de integración deben colocarse bajo el directorio `test/integration` y deben nombrarse de acuerdo con el punto de entrada que queremos probar.

Las pruebas de integración deberían limitarse a probar condiciones de integración específicas que no pueden probarse con pruebas unitarias (consultas SQL, operaciones Redis, etc) y la integración entre nuestros componentes lógicos y el protocolo (HTTP, WS).

Para mostrar un ejemplo de cómo deben hacerse las pruebas de integración, estaremos probando una solicitud HTTP simple que recupera los amigos de un usuario.

```tsx
export async function getFriendHandler(
  context: Pick<
    HandlerContextWithPath<'logs' | 'communities', '/v1/users/:user/friends'>,
    'url' | 'components' | 'params' | 'verification'
  >
): Promise<HTTPResponse<AggregatedCommunityWithMemberData>> {
  const {
    components: { friends },
    params: { id },
    verification
  } = context

  try {
    const userAddress = verification!.auth.toLowerCase()

    return {
      status: 200,
      body: {
        data: await friends.getFriends(userAddress)
      }
    }
  } catch (error) {
  
	  if (error instanceof InvalidFriendshipRequest) {
			throw InvalidRequest(isErrorWithMessage(error) ? error.message : 'Uknown error')
	  }

    return {
      status: 500,
      body: {
        message
      }
    }
  }
}
```

Y un componente friends que tiene lo siguiente:

```tsx
async function getFriends(addres: string) {
	friendsDb.getFriends()
}
```
