

Interactuar con la API del Social Service para realizar acciones relacionadas con eventos y usuarios. Estas acciones incluyen crear nuevas solicitudes de amistad, aceptar o rechazar solicitudes entrantes, y eliminar amistades. El Social Service permite a los usuarios gestionar sus relaciones e interacciones dentro de la plataforma.

## Friendships and Actions

Las amistades dentro del Social Service son relaciones bidireccionales entre usuarios. Se pueden tomar acciones para actualizar estas amistades, como enviar solicitudes de amistad, aceptar o rechazar solicitudes, cancelar solicitudes, y remover amistades. Cada una de estas acciones tiene implicaciones específicas para la relación entre usuarios.


## Implementing Friendship Updates

Para realizar estas acciones relacionadas con amistades, el mensaje `UpdateFriendshipEvent` necesita ser utilizado. Este mensaje permite la ejecución de varias acciones relacionadas con amistades, como solicitar, aceptar, rechazar, cancelar y remover.

La estructura exacta del payload y los parámetros requeridos para cada acción se especifican en el mensaje `UpdateFriendshipEvent`.

**Example Usage**

Aquí hay un ejemplo de cómo podrías usar el `UpdateFriendshipEvent` para enviar una solicitud de amistad:

```proto
message UpdateFriendshipEvent {
  string userAddress = 1;
  string targetUserAddress = 2;
  FriendshipAction action = 3;
}
```

En este ejemplo, `userAddress` representa la dirección del remitente, `targetUserAddress` representa la dirección del destinatario, y `action` denota el tipo de acción a realizar (solicitar, aceptar, rechazar, cancelar o remover).

### Sending Friend Requests

Cualquier usuario puede enviar una solicitud de amistad a otro usuario. Sin embargo, para establecer una amistad, el usuario destinatario debe aceptar la solicitud.

```javascript
requestFriendship: async (address: string, message?: string) => {
  const response = await service.updateFriendshipEvent({
    event: { request: { user: { address }, message } },
    authToken: { synapseToken }
  })
  processErrors(response)
  return response.event
}
```

### Accepting or Rejecting Requests

Cuando un usuario recibe una solicitud de amistad, tiene la opción de aceptarla o rechazarla. Aceptar una solicitud establece una amistad mutua entre los usuarios. Rechazar una solicitud niega el establecimiento de una amistad.

```javascript
acceptFriendshipRequest: async (address: string) => {
  const response = await service.updateFriendshipEvent(
    UpdateFriendshipPayload.create({
      event: { accept: { user: { address } } },
      authToken: { synapseToken }
    })
  )
  processErrors(response)
  return response.event
}
```

```javascript
rejectFriendshipRequest: async (address: string) => {
  const response = await service.updateFriendshipEvent(
    UpdateFriendshipPayload.create({
      authToken: { synapseToken },
      event: { reject: { user: { address } } }
    })
  )
  processErrors(response)
  return response.event
}
```

### Canceling Friend Requests

Si un usuario decide retractar una solicitud de amistad enviada antes de que sea aceptada, puede cancelar la solicitud. Esto previene que el destinatario vea o responda a la solicitud.

```javascript
cancelFriendshipRequest: async (address: string) => {
  const response = await service.updateFriendshipEvent(
    UpdateFriendshipPayload.create({
      event: { cancel: { user: { address } } },
      authToken: { synapseToken }
    })
  )
  processErrors(response)
  return response.event
}
```

### Removing Friendships

Cualquiera de los usuarios en una amistad mutua tiene la opción de remover la amistad. Cuando una amistad es removida, se elimina de los registros de ambos usuarios, terminando la relación mutua.

```javascript
removeFriend: async (address: string) => {
  const response = await service.updateFriendshipEvent(
    UpdateFriendshipPayload.create({
      authToken: { synapseToken },
      event: { delete: { user: { address } } }
    })
  )
  processErrors(response)
  return response.event
}
```


