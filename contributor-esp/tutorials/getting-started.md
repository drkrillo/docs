¿Alguna vez has sentido curiosidad sobre cómo funcionan los nodos Catalyst de Decentraland? ¿Alguna vez has querido ejecutar tu propio nodo y te sentiste abrumado sobre por dónde empezar? ¿Estás construyendo contenido para Decentraland y te resulta difícil hacer todo el ciclo de desarrollo y pruebas en servidores de producción?

Si estás (o has estado allí), no temas más. Este tutorial te ayudará en el proceso de planificación, averiguar qué necesitas decidir y luego guiarte paso a paso para iniciar el servidor. Además, en caso de que algo no salga según el plan, hay una sección de solución de problemas al final con algunos de los problemas más comunes y cómo solucionarlos.

¿Listo? Manos a la obra.

## Hardware requirements

Los servidores de Decentraland Foundation están desplegados en Amazon AWS en instancias `t2.xlarge`. Entonces, mientras que esos servidores están destinados para uso público, el tuyo puede estar bien con especificaciones mucho más pequeñas, dependiendo de cuál será el uso previsto.

Un AWS EC2 `t2.xlarge` tiene el siguiente hardware:

- 4 vCPUs.
- 16 Gb RAM

Y para almacenamiento de archivos + almacenamiento de DB, un SSD / HDD con capacidad de 2 Tb debería ser más que suficiente. Al momento de escribir esto, los servidores de la foundation están usando un poco más de 1 Tb para todo el almacenamiento.

## Software pre-requisites

Los siguientes son requeridos para ejecutar catalyst-owner:

- Sistema operativo Linux / MacOS
- Docker / docker-compose
- Git
- LiveKit Cluster 

## Some options to consider before we start

- ¿Quieres que tu nodo esté expuesto públicamente en Internet? Si es así, necesitarás una instancia expuesta a internet y una URL pública para conectarte a ella.
- ¿El nodo se usará para el desarrollo de escenas, wearables, etc? En este caso, las especificaciones de hardware pueden ser mucho menores, ya que probablemente serán una o dos personas accediendo, no cientos de usuarios.
- ¿Quieres sincronizar todos los tipos de entidad? Si estás usando esto para desarrollar escenas, probablemente quieras omitir la sincronización de perfiles, ya que toman mucho tiempo, ancho de banda y lo más importante espacio en disco y no te beneficiarán para tu objetivo.

## LiveKit

El servicio Comms del Catalyst necesita un cluster externo de LiveKit para orquestar las comunicaciones entre jugadores. Para eso, hay dos opciones: usar una cuenta de LiveKit Cloud o ejecutar tu propio cluster de LiveKit.

La recomendación es configurar una [cuenta de Livekit Cloud](https://cloud.livekit.io/) que viene con un tier gratuito que puede gestionar 100 usuarios, o pagar el servicio según el tráfico. Este enfoque es más fácil ya que no requiere provisionar infraestructura extra y el servicio puede gestionar el escalado.
De lo contrario, se necesitará provisionar un cluster de LiveKit. LiveKit hace un muy buen trabajo con su documentación:
- [Deployment](https://docs.livekit.io/oss/deployment/)
- [Distributed Setup](https://docs.livekit.io/oss/deployment/distributed/)

*En caso de que quieras profundizar en los detalles técnicos necesarios para soportar comunicaciones basadas en transporte LiveKit, consulta el [ADR-70 New Communications Architecture](https://adr.decentraland.org/adr/ADR-70).*

## Step by step guide

Lo primero que hay que hacer es clonar el repositorio [catalyst-owner](https://github.com/decentraland/catalyst-owner) desde GitHub

```bash
> git clone https://github.com/decentraland/catalyst-owner.git
Cloning into 'catalyst-owner'...
remote: Enumerating objects: 2392, done.
remote: Counting objects: 100% (602/602), done.
remote: Compressing objects: 100% (168/168), done.
remote: Total 2392 (delta 506), reused 481 (delta 430), pack-reused 1790
Receiving objects: 100% (2392/2392), 926.05 KiB | 1.09 MiB/s, done.
Resolving deltas: 100% (1257/1257), done.
> cd catalyst-owner
```

Una vez hecho esto, es momento de configurar las variables de entorno para el nodo, y hacer cualquier cambio apropiado.

```bash
# En la carpeta /catalyst-owner
> cp .env.example .env
> cp .env-advanced.example .env-advanced
```

Ahora usando tu editor de texto favorito, haz cualquier cambio necesario en estos archivos. Por ejemplo, configurar la variable de entorno `EMAIL` con un email válido es requerido para que puedas recibir actualizaciones sobre la expiración del certificado de Certbot.

También configurar tu valor particular para `CATALYST_URL` puede ser requerido, especialmente si tu servidor va a estar expuesto públicamente en Internet. Para uso en tu máquina local, el valor predeterminado de `http://localhost` funcionará.

#### Comms Service

Una vez que el cluster de LiveKit esté disponible, necesitarás establecer las variables específicas de LiveKit para que el servicio Comms funcione, por ejemplo:

```
LIVEKIT_HOST=wss://livekit-1.mydomain.org
LIVEKIT_API_KEY=API-JjHuvM
LIVEKIT_API_SECRET=J7YSHmNzkNCEfT2
ROOM_PREFIX=my-prefix
```

La variable `ROOM_PREFIX` es opcional y puede configurarse para identificar salas de LiveKit creadas por el Catalyst. Esto es útil si más de un Catalyst está usando el mismo Cluster de LiveKit.

#### Content Server 

Hay una variable llamada `CONTENT_SERVER_STORAGE` que define la carpeta local que el servidor de contenido usará para almacenamiento. Esto por defecto es `CONTENT_SERVER_STORAGE=./storage`. Puedes cambiar este valor para almacenar archivos en otro lugar, o al menos asegurarte de que la carpeta referenciada existe. Puede que necesites crearla así:

```bash
# En la carpeta /catalyst-owner o donde CONTENT_SERVER_STORAGE esté apuntando
> mkdir storage
```

Hay otra variable interesante `SYNC_IGNORED_ENTITY_TYPES` que permite ignorar ciertos tipos de entidad durante el proceso de sincronización. Si vas a usar el servidor Catalyst para desarrollo, quizás no necesites que se sincronice todo tipo de contenido. Puedes establecer la variable de entorno `SYNC_IGNORED_ENTITY_TYPES="profile,store"` para que solo las escenas y wearables se traigan de los servidores del DAO. Esto ahorrará mucho tiempo, ancho de banda y almacenamiento en disco en tu servidor.

>💡 Para una lista más exhaustiva de variables de entorno soportadas, por favor echa un vistazo a la sección [Environment variables](#environment-variables) abajo.

### Launch the Catalyst node 

Una vez que todas las variables de entorno han sido configuradas, es momento de iniciar el nodo. Eso debería ser tan fácil como ejecutar el script `init.sh` en la carpeta raíz.

```bash
# En la carpeta /catalyst-owner

> ./init.sh
## Loading env variables... [ OK ]
[ OK ]
## Checking if email is configured... [ OK ]
## Checking if storage is configured... [ OK ]
## Checking if catalyst url is configured... [ OK ]
 WARNING: You are not running latest image of Catalyst's Content and Catalyst's Lambdas Nodes.
 WARNING: You are not running latest image of Catalyst's Archipelago Node.
 WARNING: You are not running latest image of Catalyst's Explorer BFF Node.
 - DOCKER_TAG:               45a5154f11b53d55aadfdf7f958e2fce6a964824
 - LIGHTHOUSE_DOCKER_TAG:    latest
 - CATALYST_URL:             http://localhost
 - CONTENT_SERVER_STORAGE:   ./storage
 - EMAIL:                    a@a.com
 - ETH_NETWORK:              mainnet
 - REGENERATE:               0

Starting in 5 seconds...
45a5154f11b53d55aadfdf7f958e2fce6a964824: Pulling from decentraland/catalyst-content
Digest: sha256:d6c2981c57cb9367fdb3228d15b07e4d26e62c902944115937de24ecd3ab6aac
Status: Image is up to date for decentraland/catalyst-content:45a5154f11b53d55aadfdf7f958e2fce6a964824
docker.io/decentraland/catalyst-content:45a5154f11b53d55aadfdf7f958e2fce6a964824
45a5154f11b53d55aadfdf7f958e2fce6a964824: Pulling from decentraland/catalyst-lambdas
Digest: sha256:5605812dd29316afc32e561d2a2ce1311f164f07ef3cd99a1cd60727518636e5
Status: Image is up to date for decentraland/catalyst-lambdas:45a5154f11b53d55aadfdf7f958e2fce6a964824
docker.io/decentraland/catalyst-lambdas:45a5154f11b53d55aadfdf7f958e2fce6a964824
latest: Pulling from decentraland/catalyst-lighthouse
Digest: sha256:fedc10b714823909f0a1c17955eed2f22a02e4f784090848d3253ef734e410d4
Status: Image is up to date for decentraland/catalyst-lighthouse:latest
docker.io/decentraland/catalyst-lighthouse:latest
latest: Pulling from decentraland/archipelago-service
Digest: sha256:e2c1d6fa96a5cfbbf6fb37e3840c0724fb5abeef8c366025a28fab3ad33d6480
Status: Image is up to date for quay.io/decentraland/archipelago-service:latest
quay.io/decentraland/archipelago-service:latest
latest: Pulling from decentraland/explorer-bff
Digest: sha256:fa9e305c44972a8613b01f4cd573d6fb84b0fb03ce953a506fbb06053202913c
Status: Image is up to date for quay.io/decentraland/explorer-bff:latest
quay.io/decentraland/explorer-bff:latest
Stopping nginx ... done
## Using HTTP because CATALYST_URL is set to http://localhost
## Replacing value $katalyst_host on nginx server file... [ OK ]
## Restarting containers...
Creating network "catalyst-owner_default" with the default driver
Creating catalyst-owner_comms-server_1 ... done
Creating node-exporter                   ... done
Creating catalyst-owner_lambdas_1      ... done
Creating postgres                      ... done
Creating catalyst-owner_certbot_1        ... done
Creating nats                            ... done
Creating postgres-exporter               ... done
Creating catalyst-owner_content-server_1 ... done
Creating catalyst-owner_archipelago_1    ... done
Creating catalyst-owner_explorer-bff_1   ... done
Creating nats-exporter                   ... done
Creating cadvisor                        ... done
Creating nginx                           ... done
## Catalyst server is up and running at http://localhost
```

Si todo salió bien, ahora tenemos un nodo completo de Decentraland ejecutándose. Ahora puedes ir al navegador y escribir la URL. Si usas el predeterminado, debería ser [`http://localhost`](http://localhost/).

Podemos verificar los logs del servidor de contenido con este comando docker:

```bash
> docker logs catalyst-owner_content-server_1
```

Aunque el servidor ahora está ejecutándose, no está 100% listo para estar en funcionamiento. Necesita sincronizar el contenido de los otros [servidores del DAO](https://decentraland.github.io/catalyst-monitor) para que pueda ofrecer la misma experiencia a usuarios conectados a él. La sincronización puede tomar mucho tiempo. En una buena conexión a internet, 6 horas debería ser bastante común. Así que... tiempo de tomar un café, una siesta, un buen descanso nocturno, y volver mañana.

Una forma de saber si la sincronización está completa consiste en verificar los resultados del endpoint de estado de contenido: [http://localhost/content/status](http://localhost/content/status) (usa tu URL aquí).
```json
{
  "synchronizationStatus": {
    "lastSyncWithDAO": 1658153514917,
    "synchronizationState": "Bootstrapping"
  },
  "snapshot": {
    "entities": {
      "profile": 1271331,
      "scene": 23477,
      "wearable": 17351,
      "store": 890
    },
    "lastUpdatedTime": 1658153414530
  },
  "version": "v3",
  "commitHash": "9a2e0d5d05d02646df2e1e5d00436d3166a07aa1",
  "catalystVersion": "4.8.6",
  "ethNetwork": "mainnet"
}
```

Mientras `synchronizationState` sea `Bootstrapping`, el nodo suspenderá temporalmente la aceptación de nuevos deployments. Esta medida asegura que no se desplieguen nuevas entidades hasta que el nodo esté actualizado dentro de la red del DAO. Una vez que el estado cambie a `Syncing`, indica que el nodo se ha puesto al día exitosamente y ahora está recibiendo continuamente las últimas actualizaciones. Este es el estado saludable en el que el nodo está completamente funcionando y aceptando nuevos deployments.

```json
{
  "synchronizationStatus": {
    "lastSyncWithDAO": 1658153872301,
    "synchronizationState": "Syncing"
  },
  "snapshot": {
    "entities": {
      "profile": 1271317,
      "scene": 23477,
      "store": 890,
      "wearable": 17350
    },
    "lastUpdatedTime": 1658152050030
  },
  "version": "v3",
  "commitHash": "9a2e0d5d05d02646df2e1e5d00436d3166a07aa1",
  "catalystVersion": "4.8.6",
  "ethNetwork": "mainnet"
}
```

Si eres más del tipo cli, puedes hacer grep en los logs de la imagen docker del servidor de contenido para este mensaje: `Starting to sync entities from servers pointer changes`. Una vez que veas ese texto, el servidor de contenido está completamente sincronizado y listo para uso completo.

## Environment variables

La siguiente es una lista completa de todas las variables de entorno del servidor de contenido con sus valores predeterminados, como se registra en los logs del servidor de contenido durante el inicio:

```jsx
STORAGE_ROOT_FOLDER: "/app/storage/content_server/"
DENYLIST_FILE_NAME: "denylist.txt"
DENYLIST_URLS: "https://config.decentraland.org/denylist"
SYNC_IGNORED_ENTITY_TYPES: ""
FOLDER_MIGRATION_MAX_CONCURRENCY: 1000
SERVER_PORT: 6969
LOG_REQUESTS: false
UPDATE_FROM_DAO_INTERVAL: 1800000
SYNC_WITH_SERVERS_INTERVAL: 45000
CHECK_SYNC_RANGE: 1200000
DECENTRALAND_ADDRESS: "0x1337e0507eb4ab47e08a179573ed4533d9e22a7b"
DEPLOYMENTS_DEFAULT_RATE_LIMIT_TTL: 60
DEPLOYMENTS_DEFAULT_RATE_LIMIT_MAX: 300
ETH_NETWORK: "mainnet"
LOG_LEVEL: "debug"
FETCH_REQUEST_TIMEOUT: "2m"
USE_COMPRESSION_MIDDLEWARE: false
BOOTSTRAP_FROM_SCRATCH: false
REQUEST_TTL_BACKWARDS: 1200000
LAND_MANAGER_SUBGRAPH_URL: "https://api.thegraph.com/subgraphs/name/decentraland/land-manager"
COLLECTIONS_L1_SUBGRAPH_URL: "https://api.thegraph.com/subgraphs/name/decentraland/collections-ethereum-mainnet"
COLLECTIONS_L2_SUBGRAPH_URL: "https://api.thegraph.com/subgraphs/name/decentraland/collections-matic-mainnet"
THIRD_PARTY_REGISTRY_L2_SUBGRAPH_URL: "https://api.thegraph.com/subgraphs/name/decentraland/tpr-matic-mainnet"
BLOCKS_L1_SUBGRAPH_URL: "https://api.thegraph.com/subgraphs/name/decentraland/blocks-ethereum-mainnet"
BLOCKS_L2_SUBGRAPH_URL: "https://api.thegraph.com/subgraphs/name/decentraland/blocks-matic-mainnet"
PSQL_DATABASE: "content"
PSQL_HOST: "postgres"
PSQL_SCHEMA: "public"
PSQL_PORT: "5432"
GARBAGE_COLLECTION: true
GARBAGE_COLLECTION_INTERVAL: 21600000
PG_IDLE_TIMEOUT: 30000
PG_QUERY_TIMEOUT: 60000
PG_STREAM_QUERY_TIMEOUT: 600000
SNAPSHOT_FREQUENCY_IN_MILLISECONDS: 21600000
CUSTOM_DAO: undefined
DISABLE_SYNCHRONIZATION: false
SYNC_STREAM_TIMEOUT: "10m"
CONTENT_SERVER_ADDRESS: "http://localhost/content/"
REPOSITORY_QUEUE_MAX_CONCURRENCY: 50
REPOSITORY_QUEUE_MAX_QUEUED: 300
REPOSITORY_QUEUE_TIMEOUT: "1m"
ENTITIES_CACHE_SIZE: 150000
DEPLOYMENT_RATE_LIMIT_MAX: {}
DEPLOYMENT_RATE_LIMIT_TTL: {}
VALIDATE_API: false
RETRY_FAILED_DEPLOYMENTS_DELAY_TIME: 900000
```

## Using your node for production

Si quieres ejecutar tu propio servidor y ayudar a escalar la red, primero que todo eso es increíble, la comunidad y la foundation realmente aprecian que lo hagas.

En este caso, tendrás que pasar por el proceso de solicitar aprobación al DAO para unirte a la red visitando [este enlace](https://governance.decentraland.org/submit/catalyst/). También puedes solicitar un grant de MANA para cubrir los gastos de infraestructura y gestión.

Es importante que tus especificaciones de hardware estén más alineadas con lo sugerido arriba en los requisitos de hardware, ya que el servidor será usado por cualquier miembro de la comunidad para entrar a Decentraland.

## API Specs

Para más detalles sobre las APIs para servidores de content, lambdas y comms puedes consultar las [API specs](https://decentraland.github.io/catalyst-api-specs).

## Troubleshooting/FAQ

Algunas cosas podrían salir mal al iniciar tu servidor. Aquí hay algunos de los problemas más comunes encontrados con el tiempo y cómo solucionarlos.

### Port 5432 is used by local postgres

Si ejecutas el nodo en una máquina usada para desarrollo, o si ya está alojando otros servicios, es probable que ya haya un servidor de base de datos postgres ejecutándose en esa máquina. Así que el puerto 5432 ya estará tomado.

La forma más fácil de solucionar esto es detener el postgres que ya está ejecutándose localmente y luego reiniciar el contenedor docker de postgres usando `docker start postgres`.

Si tienes conocimiento de docker compose, puedes empezar a jugar con `docker-compose.yml` y variables de entorno para intentar que funcione en un puerto diferente. Por el bien de mantener las cosas simples aquí, evitaremos ir por ese camino en este tutorial.

### Port 80 port is used by local nginx

Lo mismo puede suceder con el puerto 80 (e incluso el puerto 443). Si estás ejecutando nginx en la máquina local, es probable que los puertos 80/443 ya estén vinculados a ese servicio, por lo que el contenedor Docker de Catalyst no podrá usarlos.

Nuevamente, el enfoque más simple sería detener el servicio nginx local y luego reiniciar el contenedor docker usando `docker start nginx`. O alternativamente, necesitas aprovechar `docker-compose.yml` y `.env` para que funcione en un puerto diferente.

## Additional docs for reference

Si quieres entender mejor qué hace un servidor Catalyst, qué servicios incluye, etc. puedes consultar el repo de arquitectura https://github.com/decentraland/architecture que explica las partes en detalle.

Si te gustaría contribuir al código de servidores Catalyst, asegúrate de revisar la [guía de contribución](https://github.com/decentraland/catalyst/blob/main/docs/CONTRIBUTING.md) para aprender sobre nuestro proceso de desarrollo, cómo proponer correcciones de errores y mejoras, y cómo construir y probar tus cambios.

Y finalmente, si necesitas contactar al equipo, puedes hacerlo vía [Discord](https://discord.com/channels/417796904760639509/948230185457696820) o enviando [GitHub issues](https://github.com/decentraland/catalyst/issues).
