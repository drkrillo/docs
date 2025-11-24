

Esta documentación está extraída de [social-rpc-client-js](https://github.com/decentraland/social-rpc-client-js#using-the-client)

### Basic setup and usage

Para usar el cliente, instala el paquete en tu proyecto NPM:

```bash
npm install -S @dcl/social-rpc-client
```

Importa la función creadora de cliente desde el paquete instalado:

```typescript
import { createSocialClient } from "@dcl/social-client";
```

Crea una nueva instancia de cliente proporcionando al cliente:
1. Una URL a la API REST del Social Service
2. Una URL al endpoint Websocket del Social Service
3. La dirección del usuario (la misma usada para firmar la identidad)
4. Una identidad, firmada con la wallet del usuario.

```typescript
import { createSocialClient } from "@dcl/social-client";
import { Wallet } from 'ethers'

// Generar una wallet aleatoria para propósitos de prueba o usar la del usuario en entornos de producción.
const wallet = Wallet.createRandom()
const identity = await createIdentity(wallet, expiration)

const socialClient = await createSocialClient(
  "https://social.decentraland.org",
  "wss://social-service.decentraland.org",
  wallet.address,
  identity
);
```

El `createSocialClient` realizará las operaciones requeridas para conectarse al Social Service y devolverá el cliente conectado.


Usa el cliente para interactuar con el Social Service:

```typescript
import { createSocialClient } from "@dcl/social-client";

const socialClient = await createSocialClient(
  "https://social.decentraland.org",
  "wss://social-service.decentraland.org",
  wallet.address,
  identity
);

const friends = socialClient.getFriends()
for await (const friend of friends) {
  console.log(friend)
}
```

El cliente expone los métodos disponibles a través del [social protobuff](https://github.com/decentraland/protocol/blob/main/public/social.proto) y un método disconnect que desconecta el cliente del Social Service.


### Generating an identity

Para autenticar usuarios con el Social Service, necesitarás generar una identidad para ellos. Para hacerlo, la biblioteca `@dcl/crypto` proporciona el método `Authenticator.initializeAuthChain`. Úsalo para generar una identidad para tus usuarios:

```typescript
  import { Wallet } from 'ethers'
  import { Authenticator } from '@dcl/crypto'
  // Generar una wallet aleatoria para propósitos de prueba o usar la del usuario en entornos de producción.
  const userWallet = Wallet.createRandom()

  // Generar una identidad para el usuario.
  const address = await userWallet.getAddress()
  const ephemeralWallet = Wallet.createRandom()
  const payload = {
    address: ephemeralWallet.address,
    privateKey: ephemeralWallet.privateKey,
    publicKey: ephemeralWallet.publicKey
  }
  const identity = await Authenticator.initializeAuthChain(address, payload, expiration, (message: string) => signer.signMessage(message))
```
