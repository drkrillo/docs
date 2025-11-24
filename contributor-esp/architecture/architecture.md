# Resumen de Arquitectura

Decentraland es una plataforma de mundo virtual descentralizado construida sobre una arquitectura híbrida que combina propiedad blockchain, entrega de contenido descentralizada y servicios centralizados para una experiencia de usuario mejorada.

## Principios Arquitectónicos

### Descentralización Donde Importa

* **Propiedad** - LAND, wearables y nombres son NFTs en Ethereum
* **Gobernanza** - El DAO controla smart contracts críticos y la tesorería
* **Contenido** - Distribuido a través de nodos Catalyst
* **Identidad** - Autenticación basada en wallet (sin contraseñas)

### Centralización para Rendimiento

* **Servicios en tiempo real** - Comunicaciones WebSocket, chat de voz
* **Descubrimiento** - Búsqueda, eventos, contenido destacado
* **Características sociales** - Amigos, comunidades, notificaciones
* **Optimización** - Procesamiento de activos, generación de imágenes

## Capas del Sistema

### Clientes

Los jugadores interactúan a través de varios clientes:

* **Unity Explorer** - Cliente de escritorio principal (Windows, Mac, Linux)
* **Web Explorer** - Cliente basado en navegador
* **CLI** - Herramientas de desarrollador para creación y despliegue de escenas
* **Clientes Comunitarios** (móvil, VR, etc) - Implementaciones alternativas para el cliente de referencia usando diferentes tecnologías de renderizado.

Todos los clientes se autentican a través de wallets cripto y siguen los mismos protocolos.

### Descubrimiento (Realm Provider)

Punto de entrada único que proporciona a los clientes una descripción de realm:

* Nodos Catalyst disponibles (ubicación, capacidad, salud)
* Endpoints de servicios backend

Permite selección inteligente de servidor basada en geografía y carga.

### Contenido Descentralizado (Red Catalyst)

Red sin permisos de nodos Catalyst que almacenan y sirven:

* Definiciones y activos de escenas
* Perfiles de avatar y wearables

Cada nodo valida y almacena contenido independientemente. Los nodos sincronizan despliegues pero operan autónomamente. Ver [Red Catalyst](catalyst.md) para detalles.

### Servidores de Características Centralizados

Microservicios backend proporcionan funcionalidad mejorada:

* **Descubrimiento de servicios** - Selección de realm y monitoreo de salud
* **Comunicaciones** - Clustering WebSocket, control de acceso de voz/video
* **Características de contenido** - Worlds, eventos, places, datos de atlas
* **Características sociales** - Amigos, comunidades, notificaciones
* **Características de plataforma** - Logros, recompensas, créditos virtuales
* **Procesamiento de activos** - Optimización, miniaturas, traducciones

Ver [Servicios Backend](services.md) para documentación completa de servicios.

### Infraestructura

Sistemas de soporte y servicios de terceros:

* **Message Broker** - NATS para comunicación entre servicios
* **Media Server** - LiveKit para voz y video
* **Blockchain Indexers** - Consultas rápidas para datos NFT
* **Email Service** - SendGrid para notificaciones

Ver [Infraestructura](infrastructure.md) para detalles.

### Blockchain

Smart contracts en Ethereum y Polygon gestionan:

* **Propiedad** - LAND, wearables, nombres, estates
* **Marketplace** - Comercio y regalías
* **DAO** - Gobernanza y tesorería
* **Moneda** - Token MANA

## Modelo de Seguridad

### Identidad y Autenticación

* **Firmas de wallet** - Sin contraseñas, autenticación a través de firmas de Ethereum
* **Auth Server** - Emite tokens JWT después de verificación de firma
* **Signed Fetch** - APIs autentican solicitudes usando firmas de wallet

### Validación de Contenido

* **Verificación de firma** - Todos los despliegues firmados por wallet
* **Propiedad de puntero** - Solo el propietario de LAND puede desplegar en coordenadas
* **Límites de tamaño** - Tamaño de contenido restringido por conteo de parcelas

### Control de Acceso

* **Tokens de tiempo limitado** - Tokens JWT y LiveKit expiran
* **Limitación de tasa** - Límites de tasa de API por IP/usuario
* **Protección DDoS** - Red Catalyst distribuida

## Enfoque de Escalabilidad

### Escalado Horizontal

* **Nodos Catalyst** - Agregar nodos a medida que crece el contenido
* **Servicios backend** - Microservicios sin estado escalan independientemente
* **Islas** - Comunicaciones divididas en grupos basados en proximidad

### Optimización

* **Asset bundles** - Activos optimizados para plataforma (bundles Unity)
* **CDN** - Activos estáticos servidos a través de CDN
* **Caché** - Caché agresivo en todas las capas
* **Indexers** - Datos de blockchain almacenados en bases de datos rápidas

## Próximos Pasos

Profundiza en áreas arquitectónicas específicas:

* **[Servicios Backend](services.md)** - Especificaciones detalladas de servicios e integraciones
* **[Red Catalyst](catalyst.md)** - Entrega de contenido y despliegue
* **[Infraestructura](infrastructure.md)** - Sistemas de soporte y servicios de terceros

## Recursos Adicionales

* [Referencia de API](../../apis/README.md) - Documentación completa de API
* [Guías del Contribuidor](../contributor-guides/contributor-guides.md) - Estándares de desarrollo
* [Organización GitHub](https://github.com/decentraland) - Repositorios de código fuente
