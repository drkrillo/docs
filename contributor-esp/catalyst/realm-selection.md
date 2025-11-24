
Un [Catalyst](https://github.com/decentraland/catalyst) es un Servidor que agrupa diferentes Servicios requeridos por el Mundo de Decentraland. Algunas de las responsabilidades principales del servidor son la gestión del almacenamiento descentralizado para la mayoría del contenido necesario por el cliente y la orquestación de comunicaciones entre peers.

Al entrar a un Cliente de Decentraland, necesitas conectarte a un Catalyst específico para el soporte de servicios backend Content, Lambdas & Comms. Se necesita usar uno de los Catalysts del DAO, podría elegirse aleatoriamente pero eso podría llevar al usuario a acceder a un servicio backend con gran latencia o un realm con una baja cantidad de usuarios mientras están conectados a otro.

Para mitigar esto, se implementa un algoritmo para selección de realm en Kernel como se describe en [ADR-86](https://rfc.decentraland.org/adr/ADR-86).

Es un algoritmo configurable que usa diferentes criterios en una lista ordenada, si una de las condiciones es lo suficientemente fuerte para tomar una decisión entonces elige un realm, si no entonces delega la decisión al siguiente criterio. Si ninguna condición fue lo suficientemente fuerte, entonces se toma una decisión predeterminada (aleatoria).

La configuración predeterminada es:
1. Prioriza Nodos Catalyst con la mayor cantidad de usuarios
2. Prioriza Nodos Catalyst con más usuarios en la ubicación visitada


Otros criterios que pueden usarse son:
- Large Latency: Prioriza los Catalysts con menor latencia
- Load Balancing: Es un Mecanismo Round Robin para distribuir todos los peers entre todos los Catalysts.

