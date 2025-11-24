
El Social Service es un servidor que gestiona amistades dentro de Decentraland. Utilizando la plataforma Matrix, sirve como la fuente autoritativa para manejar actualizaciones de Amistades, como solicitudes, aceptaciones, rechazos, cancelaciones y eliminaciones.

## Use Cases

El Social Service aborda los siguientes casos de uso relacionados con amistades:
- Solicitar una Amistad
- Aceptar una Solicitud de Amistad
- Rechazar una Solicitud de Amistad
- Cancelar una Solicitud de Amistad
- Remover un Amigo
- Listar todos mis amigos
- Listar amigos mutuos entre yo y otro usuario

## Context

Antes de la introducción del Social Service, los datos de amistad se almacenaban y recuperaban directamente desde Matrix. Esto llevó a varios problemas con los flujos de trabajo de amistad e información poco confiable. Además, implementar nuevas características era desafiante. Como Matrix principalmente maneja características de chat, el Social Service actúa como un proxy, aplicando la lógica de negocio necesaria antes de interactuar con Matrix.

## Architecture

### Version History

1. [Primera Versión](https://adr.decentraland.org/adr/ADR-113): Inicialmente, el Social Service era una API REST que solo ofrecía endpoints GET para listar todos los amigos y conexiones mutuas. Carecía de características relacionadas con actualizaciones o envío de solicitudes.

2. [Segunda Versión](https://adr.decentraland.org/adr/ADR-189): Esta versión introdujo actualizaciones al flujo de amistad. Para abordar estos cambios, se implementó un servidor WebSocket RPC. Este servidor maneja mensajes esenciales, manteniendo conexiones abiertas con clientes para proporcionar actualizaciones oportunas. Las conexiones WebSocket también facilitan características como [seguimiento de presencia](https://github.com/decentraland/adr/blob/420e63926d20166ae832e3de62dd9e3f2370cd49/content/ADR-246-poc-presence.md) dentro del Social Service.

### URLs

#### .zone Domain

Servidores de Entorno de Desarrollo para pruebas y cuando se usan testnets.

- [Login con Matrix](https://social.decentraland.zone)
- [REST API](https://social.decentraland.zone)
- [WebSocket API](https://rpc-social.decentraland.zone)

#### .org Domain

Servidores de Entorno de Producción y cuando se usa mainnet.

- [Login con Matrix](https://social.decentraland.org)
- [REST API](https://social.decentraland.org)
- [WebSocket API](https://rpc-social.decentraland.org)

## Use the Javascript Client

Para usar el Javascript Client por favor referirse a [https://github.com/decentraland/social-rpc-client-js#using-the-client](https://github.com/decentraland/social-rpc-client-js#using-the-client)

## Reference Client Implementations

- [Matrix Client](https://github.com/decentraland/matrix-client)
- [JS Client](https://github.com/decentraland/social-rpc-client-js)
- [Rust Reference Client](https://github.com/decentraland/social-client)
