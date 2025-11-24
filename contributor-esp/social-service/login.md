

El proceso de autenticación para el Social Service involucra interactuar con el [servidor Matrix](https://matrix.org/), usado internamente para el subsistema de chat privado. Los usuarios deben iniciar sesión en Matrix usando su dirección, firmando una operación fetch para obtener un token de autenticación. Este token se usa luego para autorizar interacciones con el Social Service.

## HTTP Request

Para adquirir el token de autenticación, como [la documentación de Matrix se refiere](https://spec.matrix.org/v1.3/client-server-api/#login), una solicitud POST debe hacerse a la siguiente URL: `https://social.decentraland.org/_matrix/client/r0/login` como en el ejemplo abajo.

Para iniciar sesión en Matrix, necesitas crear y firmar un mensaje con AuthChain.

### Request Body

```json
{
  "auth_chain": authChain,
  "identifier": {
    "type": "m.id.user",
    "user": address
  },
  "timestamp": timestamp.toString(),
  "type": "m.login.decentraland"
}
```

### Response Body

```json
{
  "user_id": "@0x123abC:decentraland.org",
  "social_user_id": "0x123abC",
  "access_token": "syt_SomETokEN",
  "device_id": "FRFREGRG",
  "home_server": "decentraland.org",
  "well_known": {
      "m.homeserver": {
          "base_url": "https://synapse.decentraland.org/"
      }
  }
}
```

El token de autenticación, presente en el campo `access_token`, es requerido para interacciones subsecuentes con el Social Service.

## JS Code Example

```javascript
import fetch from 'cross-fetch'
import { AuthIdentity, Authenticator } from '@dcl/crypto'

export async function getMatrixToken(matrixUrl: string, address: string, identity: AuthIdentity): Promise<string> {
  const timestamp = Date.now()
  const authChain = Authenticator.signPayload(identity, timestamp.toString())

  try {
    const response = await fetch(`${matrixUrl}/_matrix/client/r0/login`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        auth_chain: authChain,
        identifier: {
          type: 'm.id.user',
          user: address
        },
        timestamp: timestamp.toString(),
        type: 'm.login.decentraland'
      })
    })

    if (response.ok) {
      const responseBody = await response.json()
      return responseBody.access_token
    } else {
      throw new Error(`Matrix server responded with a ${response.status} status code`)
    }
  } catch (error) {
    throw new MatrixLoginError(isErrorWithMessage(error) ? error.message : 'Unknown error')
  }
}
```

Código de [social-rpc-client-js](https://github.com/decentraland/social-rpc-client-js/blob/main/src/client.ts#L14)
