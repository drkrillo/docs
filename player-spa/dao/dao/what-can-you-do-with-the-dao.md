---
description: El DAO permite a los usuarios crear y votar en propuestas que dan forma al metaverso.
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/UWbeOuof0RchgfvQfPTf/dao/dao/what-can-you-do-with-the-dao
---

# Qué puedes hacer con el DAO

El DAO permite dos tipos generales de propuestas: **propuestas con acciones vinculantes directas**, y **propuestas de gobernanza**.

## Propuestas con acciones vinculantes directas

El DAO permite a la comunidad votar en **acciones vinculantes** que resultarán en cambios hechos a los smart contracts de Decentraland en la red de Ethereum. Esas acciones vinculantes son:

* Financiar un proyecto comunitario transfiriendo una porción de los recursos del DAO a un contrato de adjudicación de subvención.
* Agregar un nodo catalyst a la red de servidores que alojan y ejecutan el mundo virtual de Decentraland.
* Agregar o eliminar puntos de interés (POIs), o ubicaciones destacadas dentro del mundo virtual, a una lista que se muestra a los usuarios. Esta lista ayuda a los usuarios a encontrar ubicaciones populares e interesantes para explorar.
* Prohibir un nombre de Decentraland. Este tipo de propuesta permite a los usuarios asegurar que los avatares no puedan recibir nombres ofensivos y dañinos.

## Propuestas de gobernanza

Algunas propuestas no son tan simples como agregar o eliminar un artículo de una lista, requieren señalización comunitaria, discusiones y caminos de implementación. Esas propuestas deben ser enviadas a través de un proceso de gobernanza de tres etapas que comienza con una encuesta y termina con una propuesta vinculante.

El proceso de votación incluye tres pasos: una Encuesta de Pre-Propuesta, una Propuesta Borrador y una Propuesta de Gobernanza. Cada nivel tendrá umbrales de envío y aprobación progresivamente crecientes para asegurar que las decisiones importantes de gobernanza sean tomadas por una mayoría representativa (basada en Poder de Voto). Cada paso debe alcanzar el umbral de VP definido para ser promovido al siguiente.

### Etapa 1: Encuesta de Pre-Propuesta

* Umbral de Envío: 100 VP
* Umbral de Aprobación: 500K VP (Una encuesta que alcanza al menos 500K VP y no obtiene una mayoría del poder de voto participante, aún puede avanzar a la etapa de Propuesta Borrador - asegurando que todos los problemas con suficiente apoyo tengan un camino inicial hacia la aprobación en política.)
* Período de Votación: 5 Días
* Objetivo: Introducir un problema de gobernanza a la comunidad, medir el sentimiento de la comunidad y determinar si hay suficiente apoyo para avanzar con la redacción de una propuesta inicial.

### Etapa 2: Propuesta Borrador

* Umbral de Envío: 1,000 VP
* Umbral de Aprobación: 1M VP y mayoría simple (51%) del poder de voto participante (Una Propuesta Borrador que falla o no alcanza este umbral puede ser enmendada y reenviada una vez.)
* Período de Votación: 1 Semana
* Objetivo: Presentar una política potencial a la comunidad en un formato estructurado y formalizar la discusión sobre los impactos potenciales de la propuesta y caminos de implementación. Una Propuesta Borrador que falla o no alcanza este umbral puede ser enmendada y reenviada una vez.

### Etapa 3: Propuesta de Gobernanza

* Umbral de Envío: 2,500 VP
* Umbral de Aprobación: 6M VP y mayoría simple del VP participante (o criterios de aceptación necesarios para su categoría)
* Período de Votación: 2 Semanas
* Objetivo: Formalizar la versión aprobada de una Propuesta Borrador en un resultado de Gobernanza vinculante.

Es importante notar que cualquiera que cumpla con el umbral de envío puede tomar una propuesta aprobada (ya sea una Encuesta o un Borrador) y moverla a la siguiente etapa, no solo el autor de la propuesta.

## ¿Qué hay de modificar los smart contracts de LAND o Estate?

Ahora mismo, el DAO posee tanto los smart contracts de LAND como de Estate. Cualquier modificación a cualquiera de estos contratos debe ser llevada a cabo por la Junta Asesora de Seguridad (SAB) y el Comité del DAO – grupos de personas confiables y elegidas encargadas de asegurar la seguridad continua de estas piezas importantes de la infraestructura de Decentraland.

El SAB debe votar a través de una billetera multi-sig para aprobar cualquier cambio a los contratos de LAND o Estate, previniendo que un miembro SAB deshonesto introduzca una vulnerabilidad.

Actualmente, no hay categorías de propuesta predefinidas para modificar los smart contracts de LAND o Estate a través de la interfaz del DAO. Eso no significa que sea imposible para un miembro que no es SAB iniciar cambios, pero es un proceso más largo que requiere obtener apoyo de la comunidad más amplia además de tener la experiencia técnica necesaria para suministrar los cambios de código y un tercero de confianza para auditar esos cambios.

El código fuente de los contratos de LAND y Estate está disponible en GitHub [aquí](https://github.com/decentraland/land/tree/master/contracts).

En términos generales, el proceso para modificar cualquiera de los contratos sería:

* Encuestar a la comunidad a través del DAO publicando una **encuesta de pre-propuesta**, y discutir públicamente tus cambios propuestos para reunir apoyo
* Después de obtener el apoyo inicial de la comunidad, publicar una **Propuesta Borrador** detallando los cambios y obtener aceptación a través de una votación.
* Escribir el código actualizado para ser fusionado en el contrato
* Obtener una revisión de código exitosa y auditoría de un tercero de buena reputación
* Presentar el código auditado a la comunidad usando una **Propuesta de Gobernanza** vinculante y obtener su aprobación para que sea fusionado con el contrato
* Una vez aprobado por otro voto comunitario, el Comité del DAO o SAB realizaría la actualización del contrato con el nuevo código
