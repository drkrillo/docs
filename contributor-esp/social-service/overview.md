Detalles sobre el uso de la API del Social Service para recuperar amistades, solicitudes y amigos mutuos en la plataforma Decentraland. La API permite a los desarrolladores interactuar con el Social Service para recuperar información relacionada con amistades.

### REST API

Para recuperar amistades, el Social Service expone una [REST API](https://social-service-api-specs.pages.dev/):

- `GET /friendships/me` - Recuperar las amistades del usuario actual. Requiere el token de acceso en el encabezado Authorization.
- `GET /friendships/{other-address}/mutuals` - Recuperar amigos mutuos con otro usuario. Requiere el token de acceso en el encabezado Authorization.

### WebSocket RPC Server

La API WebSocket del Social Service proporciona dos tipos de mensaje para recuperar amistades y amigos mutuos. Primero necesitas establecer una conexión WebSocket y luego usar esos métodos para transmitir información relacionada con usuarios.

- `GetFriends` - Recuperar los amigos de un usuario. Devuelve un stream de respuestas de usuario.
- `GetMutualFriends` - Recuperar amigos mutuos con otro usuario. Devuelve un stream de respuestas de usuario.

Consulta el [archivo friendships.proto](https://github.com/decentraland/protocol/blob/main/proto/decentraland/social/friendships/friendships.proto#L7) para más detalles sobre estos tipos de mensaje.

## JavaScript Code Examples

Para interactuar con la API WebSocket del Social Service usando JavaScript, puedes usar el cliente RPC proporcionado como se explicó antes. Pero si necesitas implementarlo tú mismo, abajo hay ejemplos de código demostrando cómo recuperar amigos y amigos mutuos:

### Getting All Friends

```javascript
import { createRpcClient, createWebSocketsTransport } from '@dcl/rpc/dist/client';
import { FriendshipsServiceDefinition } from './protobuff-types/decentraland/social/friendships/friendships.gen';

const socialClientRpcUrl = 'wss://social.decentraland.org'; // Reemplazar con la URL real
const webSocketsTransport = createWebSocketsTransport(socialClientRpcUrl);
const service = loadService(FriendshipsServiceDefinition, webSocketsTransport);

const response = service.getFriends(Payload.create({ synapseToken }));
for await (const friends of response) {
  processErrors(friends);
  const userList = friends.users?.users ?? [];
  // Procesar la lista de amigos
}
```

### Getting Mutual Friends

```javascript
const response = service.getMutualFriends(
  MutualFriendsPayload.create({
    user: { address }, 
    authToken: { synapseToken }
  })
);
for await (const mutualFriend of response) {
  processErrors(mutualFriend);
  const mutualFriendList = mutualFriend.users?.users ?? [];
  // Procesar la lista de amigos mutuos
}
```

Para más ejemplos de código y detalles, visita el [repositorio de GitHub social-rpc-client-js](https://github.com/decentraland/social-rpc-client-js)
