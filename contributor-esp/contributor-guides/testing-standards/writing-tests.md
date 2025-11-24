# Escribir Pruebas

Las pruebas pueden escribirse de varias maneras. Esta sección intenta dar un estándar y una razón detrás de la forma en que escribimos pruebas.

Las pruebas DEBEN escribirse usando los métodos **describe** e **it** proporcionados por jest

## Describir y construir contexto

El método **describe** DEBERÍA usarse para describir, siempre que sea posible, el contexto en el cual la pieza de código a probar será ejecutada y DEBERÍA construir ese contexto dentro de su alcance.

```tsx
describe('when the flag is red', () => {
	...
})

describe('when the flag is blue', () => {
	...
})
```

Para describir el contexto, DEBERÍAMOS usar una de las siguientes palabras: **when** para describes de nivel superior y **having** o **and** para indicar que hay un contexto que depende de otro contexto que fue definido antes. La razón detrás de esto es tener contextos auto-descritos que pueden ser fácilmente entendidos y verificados combinando las oraciones describe. Como la idea es concatenar las descripciones de los contextos, los **describes** DEBEN escribirse en minúsculas.

```tsx
describe('when the flag is red', () => {
	...
	describe('and the wind is strong', () => {
     // El contexto aquí se describe por la conjunción de los describes:
     // "when the red flag is red and the wind is strong"
     ...
  })
})
```

Múltiples describes pueden anidarse en el mismo nivel y en un nivel inferior. Describes al mismo nivel denotan diferentes contextos y describes en niveles inferiores (describes anidados) denotan un contexto más específico.

```tsx
describe('when the flag is red', () => {
	...
	describe('and the wind is strong', () => {
     ...
  })

	describe('and the wind is weak', () => {
     ...
  })
})
```

Como se mencionó antes, cada describe DEBERÍA estar acompañado por un método que construya el contexto o un método que destruya o cambie el contexto. Los métodos que DEBEN usarse son **beforeEach** y **afterEach**.

{% hint style="warning" %}
El framework Jest tiene dos métodos, **beforeEach** y **beforeAll**, pero combinar ambos genera confusión y problemas de ejecución cuando hay contextos anidados presentes, ya que [el orden en el que se ejecutan](https://jestjs.io/docs/setup-teardown#scoping) no es el que esperaríamos.
{% endhint %}

Los contextos creados con el método **beforeEach** estarán más y más definidos a medida que vayamos más profundo en el árbol de describe. Cada **beforeEach** especificará el contexto más de acuerdo con lo que su texto dice.

### Alcance de Variables

Las **variables** establecidas en los contextos DEBERÍAN definirse de acuerdo con el alcance en el que van a usarse. Es decir, si una variable se usa solo para un contexto específico en un nivel anidado de describes, la variable DEBERÍA definirse solo en ese contexto específico. La razón detrás de esto es mantener las variables cerca del código en el que se están usando, haciendo el código más fácil de entender.

```tsx
let flag = 'blue'
describe('when the flag is red', () => {
	let wind = 'mild'
	beforeEach(() => {
    flag = 'red'
  })
  ...

	describe('and the wind is strong', () => {
		beforeEach(() => {
	    wind = 'strong'
	  })
    ...
  })

	describe('and the wind is weak', () => {
		beforeEach(() => {
	    wind = 'weak'
	  })
    ...
  })
})
```

Las variables de **tipos primitivos**, que **no serán cambiadas** en el contexto en el que están definidas o en contextos subsecuentes DEBERÍAN definirse como **const** una vez, sobre la definición de **beforeEach** en el alcance.

Las variables de **tipos primitivos** que serán cambiadas en el contexto en el que están definidas o en contextos subsecuentes DEBEN definirse como **let**, ya que la naturaleza de Typescript lo obliga.

Las variables de **tipos no primitivos** (objetos, arrays, etc) DEBEN definirse como **let** y su valor DEBE establecerse en un **beforeEach.** La razón detrás de esto es que los objetos en JS son mutables, eso significa que cualquier ejecución de código que use ese objeto está sujeto a cambios y afectar otras pruebas de manera involuntaria.

```tsx
let flag
describe('when the flag is red', () => {
  // Un valor primitivo que va a ser modificado en múltiples alcances.
	let wind
  // Un valor objeto que va a ser usado en el código de la prueba.
  let someObject
	beforeEach(() => {
    flag = 'red'
		someObject = { id: 1 }
  })
  ...

	describe('and the wind is strong', () => {
		// Un valor primitivo constante que va a ser usado solo en este alcance.
		const aConstantPrimitiveValue = 'something'
		beforeEach(() => {
	    wind = 'strong'
	  })
    ...
  })

	describe('and the wind is weak', () => {
		beforeEach(() => {
	    wind = 'weak'
	  })
    ...
  })
})
...
```

Las variables DEBERÍAN estar correctamente tipadas siempre que sea posible. Los tipos repetidos DEBERÍAN abstraerse en un type.

```tsx
let flag: string
describe('when the flag is red', () => {
  // Un valor primitivo que va a ser modificado en múltiples alcances.
	let wind: string
  // Un valor objeto que va a ser usado en el código de la prueba.
  let someObject: { id: string }
	beforeEach(() => {
    flag = 'red'
		someObject = { id: 1 }
  })
  ...

	describe('and the wind is strong', () => {
		// Un valor primitivo constante que va a ser usado solo en este alcance.
		const aConstantPrimitiveValue = 'something'
		beforeEach(() => {
	    wind = 'strong'
	  })
    ...
  })

	describe('and the wind is weak', () => {
		beforeEach(() => {
	    wind = 'weak'
	  })
    ...
  })
})
...
```

## Describir expectativas y ejecutar código

El método **it** DEBE colocarse siempre dentro del alcance de un **describe** y DEBE usarse para describir lo que esperamos del código que vamos a probar y DEBERÍA contener, si es posible, solo **una** afirmación. Múltiples afirmaciones por **it** pueden existir si hay un problema de rendimiento, ya que las pruebas se ejecutarán una vez por **it** (debido al **beforeEach**), o si lo que se espera puede describirse con claridad en la descripción de la expectativa.

```tsx
let flag: string
describe('when the flag is red', () => {
	let wind: string
  let someObject: { id: string }
	beforeEach(() => {
    flag = 'red'
		someObject = { id: 1, swim: jest.fn() }
  })
  ...

  // Expectativa única de la ejecución de una prueba en un contexto (un it)
	describe('and the wind is strong', () => {
		const aConstantPrimitiveValue = 'something'
		beforeEach(() => {
	    wind = 'strong'
	  })
    
		it('should not go swimming', () => {
      expect(goSwimming(flag, wind, someObject)).toBe(false)
    })
  })

  // Múltiples expectativas de la ejecución de una prueba en un contexto (dos its)
	describe('and the wind is weak', () => {
    let result: boolean
		beforeEach(() => {
	    wind = 'weak'
      result = goSwimming(flag, wind, someObject)
	  })

		it('should go swimming', () => {
      expect(goSwimming(flag, wind, someObject)).toBe(true)
    })

    it('should have called the swim method', () => {
      expect(someObject.swim).toHaveBeenCalled()
    })
  })
})
...
```

La razón detrás de esta estructura es proporcionar claridad al revisor de las pruebas y a los desarrolladores que mantendrán y harán cambios al código, ya que cada contexto y expectativa está claramente enumerado, haciendo más fácil entender qué se está probando, cómo se está probando y qué falta probar.

Siguiendo las descripciones de **describe** hasta los **it**s los desarrolladores pueden entender fácilmente qué se está probando concatenando las oraciones.

```tsx
// Esto se lee como: "when the flag is red and the wind is strong, 
// it should go swimming"
describe('when the flag is red', () => {
  ...
  describe('and the wind is strong', () => {
		...
		it('should go swimming', () => {
			...
		})
  })
})
```

### Escribir Expectativas Claras

La descripción de las expectativas escritas en los **it**s DEBE ser lo más descriptiva posible sobre lo que se espera de la ejecución de las pruebas. Los desarrolladores NO DEBEN usar fraseado abstracto o general para definir expectativas ya que elimina claridad sobre lo que se espera del código.

{% hint style="danger" %}
**El desarrollador NO DEBE usar frases como:**

* "should work as expected" ⇒ ¿Qué es trabajar como se espera?
* "should return the correct value" ⇒ ¿Cuál es el valor correcto?
* "should resolve/return/work correctly" ⇒ ¿Cómo funciona algo correctamente?
* "should fail" ⇒ ¿Cómo debería fallar? ¿Cuál es el mensaje que debería proporcionar?
{% endhint %}

Debe tomarse en consideración que al especificar lo que se espera en los **it**s o cuál será el contexto en los **describe**s los desarrolladores PUEDEN usar nombres de funciones o mensajes de error exactos si es necesario para entender mejor la intención, pero DEBERÍAN usar una representación textual cuando sea posible para hacer las pruebas más fáciles de mantener.

```tsx
// math.ts
export function div(a: number, b: number): number {
	if(b === 0)	{
		throw new Error('The divisor b equals 0, the division can\'t be performed')
	}
}

// test.spec.ts
import { div } from './math.ts'

describe('when dividing by zero', () => {
  // La descripción del it describe la intención del mensaje de la excepción
	it('should throw an exception signaling that a division by 0 is not possible', () => {
		expect(() => div(12, 0)).toThrowError('The divisor b equals 0, the division can\'t be performed')
	})
})
```

## Qué probar

Elegir qué probar puede variar dependiendo del código que se está ejecutando. Diferentes factores, desde rendimiento o un gran dominio de entradas, pueden definitivamente cambiar qué debería o no debería probarse en las pruebas. Aquí expondremos un conjunto único de casos que el desarrollador DEBERÍA probar si es posible.

```tsx
function run(kilometers: number): number {
	if(kilometers > 1000) {
    throw new Error('The runner can\'t run more than 1000 kilometers')
  }

  if(kilometers <= 10) {
		return kilometers
  } else if(kilometers > 10) {
		doSomething(kilometers)
    return beLazy(kilometers)
  }
}
```

La función run aquí tiene un par de flujos de ejecución diferentes. Las funciones, o secciones de código, NO DEBERÍAN probarse basándose solo en los diferentes flujos de ejecución posibles, sino en lo que se espera de la función, ya que la función podría no estar haciendo lo que fue escrita para hacer y las pruebas podrían estar fuertemente acopladas con el código y no detectar diferentes problemas con él. Esto implica que el desarrollador DEBERÍA siempre probar todos los caminos de ejecución posibles y DEBERÍA probar otros caminos posibles también de acuerdo con la semántica de la función.

Para este caso específico, el desarrollador DEBERÍA probar **al menos** los siguientes casos:

1. Ejecutar la función run con una cantidad de kilómetros mayor que 1000
2. Ejecutar la función run con una cantidad de kilómetros igual a 10
3. Ejecutar la función run con una cantidad de kilómetros mayor que 10 pero menor que 1000

Para el primer caso, el desarrollador necesita probar que la función run lanza una excepción. **Las excepciones DEBEN verificarse por su mensaje de error**, ya que cualquier otra excepción también podría ser lanzada, invalidando el propósito de la prueba. Si la excepción es una excepción personalizada, el desarrollador PUEDE verificar la instancia de la excepción.

Para el segundo caso, el desarrollador necesita probar que la función retornó el mismo número de kilómetros que se le dio.

Para el tercer caso, el desarrollador necesita probar que la función retornó lo que una función externa, **beLazy** retornó y, como **doSomething** (también ejecutado en el método) no puede verificarse a través del valor de retorno de la función, el desarrollador DEBERÍA **probar que fue llamado con los argumentos correctos**.

```tsx
import { doSomething, beLazy } from '../runningUtils'
jest.mock('../runningUtils')

// Note que no hay describe principal
// El único contexto global son funciones que queremos mockear con jest

const mockDoSomething = doSomething as jest.MockedFunction<typeof doSomething>
const mockBeLazy = beLazy as jest.MockedFunction<typeof beLazy>

describe('when running more than 1000 kilometers', () => {
	it('should throw an error signaling that the runner can\'t run more than 1000 kilometers', () => {
		expect(() => run(1001)).toThrowError("The runner can't run more than 1000 kilometers")
	})
})

describe('when running less or equal than 10', () => {
	it('should return the same amount of kilometers as the ones given', () => {
		expect(run(2)).toEqual(2)
	})
})

describe('when running more than 10 kilometers but less than 1000', () => {
	const kilometers = 50
	let result: number
	beforeEach(() => {
		mockDoSomething.mockReturnValueOnce(undefined)
		mockBeLazy.mockImplementationOnce(value => value)
		result = run(kilometers)
	})
	
	it('should return the kilometers after being lazy', () => {
		expect(result).toEqual(kilometers)
	})

	it('should call the doSomething function with the given kilometers', () => {
		expect(mockDoSomething).toHaveBeenCalledWith(kilometers)
	})
})
```

## Cuándo mockear

Mockear dependerá principalmente del tipo de pruebas que el desarrollador está escribiendo.

* Las pruebas unitarias DEBERÍAN tener cada función externa mockeada, con la excepción de funciones que hacen cosas simples, como formatear strings, etc.
* Las pruebas de API DEBERÍAN solo tener mocks para comunicarse con servicios externos, es decir, DBs o diferentes APIs. Si una de las operaciones que se hace en la prueba afecta el rendimiento de la suite de pruebas, un mock podría implementarse para mitigar este problema.

Todos los mocks DEBEN hacerse usando las herramientas proporcionadas por Jest, con la excepción de los mocks de `redux-saga-test-plan`.

### Mocks y funciones de utilidad

* Cualquier mock de Jest DEBERÍA mockearse usando su método **once** cuando sea posible, es decir, `mockReturnValueOnce` o `mockResolvedValueOnce` se recomienda en lugar de `mockReturnValue` o `mockResolvedValue`. La razón detrás de esto es prevenir mocks no deseados de ejecutarse, cambiando la ejecución de nuestras pruebas.
* Jest ofrece un montón de Types que puedes usar para diferentes tipos de mocks. Por ejemplo, al mockear un objeto DEBERÍAS usar `jest.Mocked<typeof someObject>`, para clases DEBERÍAS usar `jest.MockedClass<typeof SomeClass>`, y para funciones, `jest.MockedFunction<typeof someFunction>`.
* Un `afterEach` DEBERÍA escribirse para ejecutarse después de cada prueba con un `jest.resetAllMocks` para limpiar cualquier implementación mockeada de módulos globalmente mockeados que podrían filtrarse a otras pruebas. Puedes ignorar este reset si estás usando `mockReturnValueOnce` o `mockResolvedValueOnce` porque se hace automáticamente por ti.
* Un directorio llamado `mocks` PUEDE crearse para almacenar mocks, que deberían colocarse en el directorio `test` o `spec`. Los mocks grandes DEBEN almacenarse en archivos diferentes de las pruebas para evitar tener archivos de prueba extendidos. Los mocks pequeños, si no hay muchos, DEBERÍAN mantenerse en las pruebas.
  * Los archivos de mock DEBERÍAN nombrarse como la entidad que están mockeando, por ejemplo, si estamos mockeando un perfil, el mock debería colocarse bajo el archivo `/test/mocks/profile.ts`.
  * En caso de que se requieran varios mocks grandes, DEBERÍAN colocarse en archivos diferentes dentro de un directorio llamado como la entidad a mockear, por ejemplo, si tenemos dos mocks de perfil, los almacenaremos como: `/test/mocks/profile/profile-with-wearables.ts` y `/test/mocks/profile/profile-without-wearables.ts`. Para acceder a ellos fácilmente, DEBERÍAS crear un archivo `index.ts` dentro del directorio `/test/mocks/profile/` y exportarlos.
  * Para evitar mutación de objetos mockeados, los mocks **DEBEN exportarse como funciones** que los retornen.
