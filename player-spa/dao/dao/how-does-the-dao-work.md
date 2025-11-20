---
description: >-
  Descripción general de las diferentes plataformas y entidades que conforman el
  DAO de Decentraland.
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/UWbeOuof0RchgfvQfPTf/dao/dao/how-does-the-dao-work
---

# Cómo funciona el DAO

Para evitar las tarifas de gas muy altas asociadas con la gobernanza completa en la cadena, el DAO de Decentraland usa una combinación de votación gratuita fuera de la cadena para la comunidad y una billetera multi-sig controlada por un "Comité del DAO" para promulgar esas decisiones fuera de la cadena en la blockchain de Ethereum. Esto permite que todos los que poseen MANA, NAMEs o LAND participen en el DAO sin tener que pagar tarifas cada vez que quieran votar o abrir una propuesta.

El uso de una billetera multi-sig controlada por un comité de personas confiables garantiza la seguridad del DAO junto con garantizar que las propuestas aprobadas resulten en una acción en la cadena. Un segundo multisig propiedad del SAB proporciona una segunda capa de protección encima del Comité del DAO.

El DAO es un sistema complejo creado a partir de varias capas de entidades y plataformas, cada una de las cuales se enumera y describe a continuación:

## dApp de Gobernanza

La interfaz principal para el DAO de Decentraland se encuentra en [governance.decentraland.org](https://governance.decentraland.org). Aquí es donde los usuarios inician sesión, crean propuestas y votan.

_Esto está actualmente mantenido por un equipo de desarrollo financiado por una_ [_subvención del DAO_](https://governance.decentraland.org/proposal/?id=ed53e850-5e70-11ec-8188-4352ce3d30e7)_._

## Snapshot

[Snapshot](https://snapshot.org/#/) es una plataforma de votación fuera de la cadena que proporciona una forma gratuita para que los poseedores de tokens voten en propuestas. Al almacenar tanto las propuestas como los votos en IPFS como mensajes firmados criptográficamente, Snapshot permite resultados seguros y fácilmente impugnables.

El DAO de Decentraland usa Snapshot para alojar las propuestas y votos generados por la comunidad.

Cada vez que se abre una propuesta en governance.decentraland.org, también se crea automáticamente en [el espacio Snapshot de Decentraland](https://snapshot.org/#/snapshot.dcl.eth). Esto permite al DAO registrar y almacenar propuestas, votos y resultados de manera segura y descentralizada - con los resultados mostrados de vuelta en governance.decentraland.org. Desde el espacio Snapshot los usuarios también pueden [delegar su VP](https://snapshot.org/#/delegate/snapshot.dcl.eth) a otros miembros de la comunidad.

## Aragon

[Aragon](https://aragon.org/) es una plataforma segura para crear y gestionar la colección de smart contracts necesarios para ejecutar un DAO. El backend del DAO de Decentraland está construido usando Aragon, por lo que cada vez que una propuesta que resulta en una acción vinculante es aprobada por la comunidad en Snapshot, es comprometida a la red principal de Ethereum en Aragon por el Comité del DAO.

## Comité del DAO

El Comité del DAO es un grupo de tres individuos confiables que han sido seleccionados por la comunidad para mantener claves en una billetera multi-sig. Este multi-sig es responsable de promulgar cualquier voto aprobado con una acción vinculante, como financiar una Subvención, prohibir un nombre, agregar o eliminar un POI, implementar una propuesta de Gobernanza o agregar un nodo Catalyst.

El Comité del DAO es supervisado por el SAB, que tiene la capacidad de pausar y cancelar cualquier acción iniciada por el Comité.

Cada transacción en la cadena iniciada por el Comité del DAO tiene un retraso automático de 24 horas antes de que se complete, permitiendo al SAB o al Comité revocar la transacción.

![Comité del DAO](https://github.com/decentraland/documentation/blob/main/static/images/DAO%20Organizational%20Chart/DAO%20Committee.png?raw=true)

## Junta Asesora de Seguridad (SAB)

La Junta Asesora de Seguridad actúa como garante de la seguridad de los smart contracts de Decentraland, y está encargada de supervisar el trabajo del Comité del DAO y responder a informes de vulnerabilidades y errores en cualquiera de los contratos de Decentraland.

El SAB incluye 5 expertos en Solidity que inicialmente han sido seleccionados por el equipo de desarrollo de Decentraland.

Cada vez que se va a hacer una modificación a los contratos de LAND o Estate, la actualización debe ser apoyada unánimemente por el multi-sig del SAB. Se requieren al menos tres signatarios sin votos disidentes para hacer cualquier cambio a los contratos de LAND o Estate.

El SAB tiene la capacidad de pausar, reanudar o cancelar cualquier acción tomada por el Comité del DAO.

Iniciar la adición o eliminación de un miembro del SAB se puede hacer iniciando un proceso de propuesta de Gobernanza en [governance.decentraland.org](https://governance.decentraland.org).

![Junta Asesora de Seguridad](https://github.com/decentraland/documentation/blob/main/static/images/DAO%20Organizational%20Chart/Security%20Advisory%20Board.png?raw=true)

## Comité de Curación de Wearables

El Comité de Curación es responsable de revisar y aprobar wearables y emotes enviados por la comunidad de Decentraland.

![Comité de Curación de Wearables](https://github.com/decentraland/documentation/blob/main/static/images/DAO%20Organizational%20Chart/Wearables%20Curation%20Last.png?raw=true)

## Comité de Revocaciones

El Comité de Revocaciones es responsable de revisar casos planteados por la comunidad con respecto al Programa de Subvenciones y tiene la responsabilidad de proporcionar una resolución, que puede ser revocar un contrato de adjudicación como acción final.

![Comité de Revocaciones](https://github.com/lordlikedao/documentation/blob/main/static/images/DAO%20Organizational%20Chart/Revocation.png?raw=true)

## Escuadrón de Gobernanza

El Escuadrón de Gobernanza es responsable de desarrollar, mejorar y mantener la infraestructura tecnológica y herramientas de toma de decisiones del DAO.

![Escuadrón de Gobernanza](https://github.com/decentraland/documentation/blob/main/static/images/DAO%20Organizational%20Chart/Governance%20Squad.png?raw=true)

## Escuadrón de Facilitación

El Escuadrón de Facilitación es responsable de gestionar comunicaciones y alinear diferentes partes interesadas, fomentando la toma de decisiones colectiva y operaciones de gobernanza eficientes.

![Escuadrón de Facilitación](https://github.com/decentraland/documentation/blob/main/static/images/DAO%20Organizational%20Chart/Facilitation%20last.png?raw=true)

## Escuadrón de Soporte de Subvenciones

El Escuadrón de Soporte de Subvenciones tiene la responsabilidad de estar en diálogo constante con la comunidad de beneficiarios de subvenciones, para entender sus necesidades y proporcionar soporte a sus bloqueadores y solicitudes.

![Escuadrón de Soporte de Subvenciones](https://github.com/decentraland/documentation/blob/main/static/images/DAO%20Organizational%20Chart/Grant%20Support.png?raw=true)

## El Foro

El [Foro de Decentraland](https://forum.decentraland.org) es el centro de comunicación para el DAO. Cada vez que se crea una nueva propuesta, se abre automáticamente un hilo acompañante en el Foro bajo la [sección de Gobernanza](https://forum.decentraland.org/c/governance/). Estos temas de propuesta se pueden encontrar navegando por el Foro, o haciendo clic en el botón "Discutir en el Foro" en la página de detalles de cualquier propuesta en la interfaz de Gobernanza. Estos hilos son un espacio donde los votantes pueden discutir los diversos impactos y problemas que podrían resultar de una propuesta.

## Servidores de Discord

El DAO opera en dos servidores de Discord:

* [Canal #dao](https://discord.com/channels/417796904760639509/538106198419832844) en el servidor Discord de Decentraland para anuncios generales, discusiones y preguntas.
* Un [servidor Discord dedicado](https://dcl.gg/daodiscord) solo para las operaciones del DAO con discusiones más profundas, canales específicos para grupos de trabajo que abordan problemas complejos y espacios para que los propietarios de subvenciones comunitarias publiquen actualizaciones sobre su proyecto. En este canal el DAO también organiza su Town Hall mensual.
