

### When is needed to create a new entity?
Primero, verifica si una entidad existente puede resolver el caso de uso necesario para evitar sobre-ingeniería de una solución.
Si ninguno de los tipos de entidad existentes definidos en https://github.com/decentraland/common-schemas/blob/main/src/platform/entity.ts#L13 satisface los requisitos, entonces puede que necesites crear un nuevo tipo de entidad.

### First answer:
- ¿Es la nueva entidad una Colección NFT?
- ¿Cómo se validará la propiedad?
- ¿Cuál será el tamaño de ella?
- ¿Cuál es la cantidad de entidades que se crearán?


### Steps

Primero crea un ADR en https://github.com/decentraland/adr donde definirás el schema y la fecha para comenzar a validar la nueva entidad.

#### Repositories

##### @dcl/schemas
1. Crea el nuevo tipo de entidad en el enum: https://github.com/decentraland/common-schemas/blob/main/src/platform/entity.ts#L19
2. Crea la carpeta y archivos en https://github.com/decentraland/common-schemas/tree/main/src/platform generando el tipo y JSON schema, por ejemplo: https://github.com/decentraland/common-schemas/blob/main/src/platform/profile/profile.ts#L20

##### @dcl/content-validator

Implementa las validaciones definidas en el ADR, teniendo en cuenta la fecha de ese ADR en https://github.com/decentraland/content-validator/blob/main/src/validations/timestamps.ts. Por ejemplo: https://github.com/decentraland/content-validator/blob/main/src/validations/items/emotes.ts#L8

1. Agrega el código para verificar la propiedad del nuevo tipo de entidad en https://github.com/decentraland/content-validator/tree/main/src/validations/access-checker
2. Implementa el validationForType para el nuevo tipo, por ejemplo: https://github.com/decentraland/content-validator/blob/main/src/validations/profile.ts#L112
3. Aquí necesitarás definir el tamaño máximo por entidad en https://github.com/decentraland/content-validator/blob/main/src/validations/ADR51.ts.

##### @dcl/urn-resolver
1. Si la nueva entidad corresponde a una Colección, entonces no hay que agregar nada.
2. Si no, entonces crea un nuevo urn correspondiente a la nueva entidad y defínelo en https://github.com/decentraland/urn-resolver agregando un resolver en https://github.com/decentraland/urn-resolver/blob/main/src/resolvers.ts#L23

##### Catalyst: Content Server
1. No se necesita cambio en la base de datos.
2. Actualiza todas las bibliotecas modificadas detalladas arriba.

##### Catalyst: Lambdas Server
1. Actualiza todas las bibliotecas modificadas detalladas arriba.
2. Verifica si la sanitización de perfiles necesita ser actualizada (endpoint `/lambdas/profiles`)
3. Verifica si el endpoint de colecciones erc necesita ser actualizado (endpoint `/lambdas/collections`)
4. Crea un endpoint para mostrar esa entidad, por ejemplo `/lambdas/wearables-by-owner`
