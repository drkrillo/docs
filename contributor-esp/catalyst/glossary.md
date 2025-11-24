### Catalyst

[Catalyst](https://github.com/decentraland/catalyst) es el nombre dado al **Paquete de Servicios de Decentraland**, cada [nodo](https://decentraland.github.io/catalyst-monitor) Catalyst expone un conjunto de servicios que funcionan como la columna vertebral de la plataforma y también expone una [API](https://decentraland.github.io/catalyst-api-specs/) pública.

### Bootstrap

Cuando el Catalyst se inicia, el nodo tendrá un estado de `Bootstrapping` mientras obtiene contenido de otros catalysts hasta que se ponga al día con los demás. Y luego pasa al siguiente estado `Syncing` en el cual está listo para servir contenido.

### Catalyst Client

[Librería](https://github.com/decentraland/catalyst-client) que puede usarse para interactuar con los servidores Catalyst. Permite tanto obtener datos como desplegar nuevas entidades al servidor especificado.

### Entity

Tipo de Dato, definido con [schemas](https://github.com/decentraland/common-schemas), que no puede almacenarse en la blockchain debido a su tamaño. Algunos de los [tipos soportados](https://github.com/decentraland/common-schemas/blob/main/src/platform/entity.ts#L13) actuales son los siguientes:

- [Wearable](https://github.com/decentraland/common-schemas/blob/main/src/platform/item/wearable/wearable.ts)
- [Emote](https://github.com/decentraland/common-schemas/blob/main/src/platform/item/emote/emote.ts)
- [Scene](https://github.com/decentraland/common-schemas/blob/main/src/platform/scene/scene.ts)
- [Profile](https://github.com/decentraland/common-schemas/blob/main/src/platform/profile/profile.ts)
- [Stores](https://github.com/decentraland/common-schemas/blob/main/src/dapps/store.ts)
- [Outfits](https://github.com/decentraland/common-schemas/blob/main/src/platform/outfits/outfits.ts)

### Deployment

Instancias de Entities creadas en el Content Server e identificadas con un [EntityId](https://github.com/decentraland/common-schemas/blob/main/src/platform/entity.ts#L31) único. Los Deployments pueden ser de cualquier [Entity Type](https://github.com/decentraland/common-schemas/blob/main/src/platform/entity.ts#L13).

### Failed Deployment

Instancia de una Entity desplegada en un Content Server que falló al pasar las [Validaciones de Contenido](https://adr.decentraland.org/adr/ADR-51). Las validaciones están implementadas en la librería [content validator](https://github.com/decentraland/content-validator).

### Active Entity

Una entidad que está actualmente desplegada en un puntero, por lo tanto todas las entidades activas representan el contenido en vivo de Decentraland: escenas, perfiles, wearables, etc.

### Pointer

Los punteros son los ids o ubicaciones a las que pertenece un Deployment. Su formato es diferente según el `Entity Type`, por ejemplo:

- **Wearables**: Usa URN como punteros `urn:decentraland:ethereum:collections-v1:dg_atari_dillon_francis:gerald_patchwork_knit_sweater`
- **Scenes**: Usa parcelas o array de parcelas como punteros, por ejemplo `--10,20` o `[(0,0), (0,1), (0,-1)]`
- **Profiles**: Usa una dirección de Wallet como puntero `0xb01ddb6d4c89314f74a32dfc2f3bd0f8311a6d75`
- **Stores**: Usa un URN como puntero `urn:decentraland:off-chain:marketplace-stores:{wallet}`
- **Outfits**: Usa una dirección de Wallet como puntero con sufijo `:outfits`, `0xb01ddb6d4c89314f74a32dfc2f3bd0f8311a6d75:outfits`

### URN

[URN](https://en.wikipedia.org/wiki/Uniform_Resource_Name) significa Uniform Resource Name. Se usan como identificadores únicos globales para activos a través de los proyectos de Metaverso.

- URN Resolver: https://github.com/decentraland/urn-resolver
- URN Namespace: https://github.com/common-metaverse/urn-namespaces

### Content Server

El [Content Server](https://github.com/decentraland/catalyst/tree/main/content) es una parte del nodo Catalyst encargada de gestionar el almacenamiento descentralizado de Deployments de Entity. Actualmente, todo el contenido almacenado en un Content Server se sincroniza con el resto de los Catalysts del DAO.

### Lambdas

El Servicio [Lambdas](https://github.com/decentraland/catalyst/tree/main/lambdas) es una parte del nodo Catalyst que funciona como una capa de reconciliación entre los Content Servers y la Blockchain, sanitizando contenido, validando propiedad y ayudando a recuperar información para los clientes Catalyst.

### Synchronization

Los Content Servers ofrecen la capacidad de desplegar contenido en ellos. También aseguran que cualquier contenido desplegado en los otros Catalysts del DAO esté disponible allí también. Resolver esto se llama **sincronización**.

### Content SNAPSHOT

Un conjunto de todas las Active Entities de un tipo específico en un [content server](#content-server). Estas instantáneas de contenido se usan para sincronizar contenido entre nodos Catalyst.

### Realm

Cuando estás conectado a Decentraland, solo podrías interactuar con Peers conectados al mismo Realm. Como nombres amigables, los Realms usan nombres de Dioses Griegos como Hela o Loki. Cada realm está determinado por un solo [Nodo Catalyst](https://decentraland.github.io/catalyst-monitor/).

### Peer

Un usuario conectado a Decentraland.

### Lighthouse

El [Lighthouse](https://github.com/decentraland/lighthouse) es el nombre usado para referenciar la versión anterior del Servicio de Comunicaciones alojado en los nodos Catalyst y usado para agrupar usuarios en una red P2P.
Las responsabilidades principales eran determinar cuáles son los candidatos para una conexión P2P y hacer la señalización WebRTC para establecer la conexión. La mayor parte de esta lógica se hacía a través de dos componentes externos: el PeerJS Server (conecta peers [WebRTC](https://webrtc.org/)) y Archipelago (recibe posiciones de usuarios y los agrupa en islas).

### Backend for Frontend (aka BFF)

El Servicio [BFF](https://github.com/decentraland/explorer-bff) fue creado para resolver las necesidades del cliente y habilitar desarrollo más rápido de nuevas características sin romper las APIs existentes. En el contexto del Servicio de Comunicación de Catalyst, estará a cargo de gestionar la señalización P2P y así reemplazar el Lighthouse.

### Catalyst Owner

Un [Catalyst Owner](https://github.com/decentraland/catalyst-owner) es un individuo u organización que ejecuta un nodo Catalyst. Para agregar un nodo Catalyst a la red del DAO se necesita enviar una [propuesta](https://governance.decentraland.org/?type=catalyst) y los nodos del DAO representan la Red de Decentraland confiable.


### Archipelago Service

El [Archipelago Service](https://github.com/decentraland/archipelago-service) anteriormente era una librería usada por el Lighthouse. Este servicio tendrá la misma responsabilidad que la librería tenía: agrupar peers en clusters para que puedan comunicarse eficientemente entre ellos. Cada cluster se llama una isla. Por otro lado, el servicio también necesita poder balancear islas usando los transportes de comunicaciones disponibles y siguiendo un conjunto de reglas definidas por el [Catalyst Owner](https://github.com/decentraland/catalyst-owner), para, por ejemplo, usar LiveKit para una isla en la escena del Casino y P2P en Genesis Plaza.

### Island

Cluster de peers conectados.

### Communication Transport

Transport es la tecnología usada para compartir actualizaciones entre Peers conectados a una Island.

### P2P Transport

Usa conexiones [WebRTC](https://webrtc.org/) entre Peers, no se necesita infraestructura extra para escalar ya que la carga está del lado del cliente y los Servers solo gestionan la señalización. La latencia aumenta a medida que se agregan más Peers a una Island. Hay un límite en la cantidad de conexiones WebRTC que un cliente puede tener, por lo tanto, para tener islas más grandes, se necesitan más conexiones indirectas (saltos) entre peers.

### LiveKit Transport

[LiveKit](https://livekit.io/) es un proyecto de código abierto que proporciona conferencias escalables y multiusuario sobre WebRTC. En lugar de hacer una red P2P, los Peers están conectados a una Unidad de Reenvío Selectivo (SFU) encargada de gestionar el relay de mensajes y diferentes aspectos de calidad de la comunicación. Los SFUs son la infraestructura agregada necesaria para proporcionar comunicaciones de alto rendimiento / alta calidad entre multitudes en escenas designadas.

### WebSocket Transport

El [WebSocket Transport](https://github.com/decentraland/ws-room-service) es más ligero que LiveKit, también requiere un Server que gestiona el relay de mensajes a través de salas WebSocket. Las salas son equivalentes a islas.
Este transporte proporciona menos latencia y mejor rendimiento que islas P2P pero también requiere la infraestructura extra.

### NATS

[NATS](https://nats.io/) es un message broker que habilita el intercambio de datos y comunicación entre servicios. En el contexto del Servidor Catalyst se usa para comunicar algunos de los servicios internos.

### DAO

DAO significa "Organización Autónoma Descentralizada". El DAO de Decentraland posee los smart contracts y activos más importantes que conforman Decentraland – el Contrato LAND, el Contrato Estates, Wearables, Content Servers y el Marketplace. También posee una cartera sustancial de MANA que le permite ser verdaderamente autónomo así como subsidiar varias operaciones e iniciativas en todo Decentraland.

[https://dao.decentraland.org/en/](https://dao.decentraland.org/en/)
