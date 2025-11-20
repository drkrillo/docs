---
description: El DAO es la plataforma de toma de decisiones para Decentraland.
metaLinks:
  alternates:
    - https://app.gitbook.com/s/UWbeOuof0RchgfvQfPTf/dao/dao/what-is-the-dao
---

# Qué es el DAO

El DAO de Decentraland es la herramienta de toma de decisiones para los poseedores de MANA, NAMEs y LAND en el mundo virtual de Decentraland. A través de votos en el DAO, la comunidad puede emitir subvenciones y hacer cambios a las listas de nombres prohibidos, POIs y nodos catalyst. El DAO también controla los smart contracts de LAND y Estate.

Emitir subvenciones y hacer cambios a los registros y contratos propiedad del DAO solo se puede hacer usando propuestas predefinidas accesibles en [governance.decentraland.org](https://governance.decentraland.org).

Estas propuestas, los votos enviados y los resultados finales se almacenan en IPFS a través de Snapshot, un cliente de votación sin gas. Las propuestas aprobadas con acciones vinculantes son promulgadas en la blockchain de Ethereum por un comité mediante una billetera multi-sig. Este comité es supervisado por la Junta Asesora de Seguridad (SAB), otro multisig con titulares de claves confiables. Este Comité fue votado por la comunidad en la versión anterior del DAO. [La propuesta original se puede encontrar aquí](https://forum.decentraland.org/t/proposal-for-a-more-accessible-and-affordable-dao/450).

El resto de este documento explica con mayor detalle qué es el DAO, cómo funciona y para qué se puede usar.

Para un tutorial detallado sobre cómo usar el DAO de Decentraland, visita la [Guía de Usuario del DAO](../dao-userguide.md).

## El DAO está impulsado por smart contracts

Todos los DAOs, u organizaciones autónomas descentralizadas, son parte de un nuevo enfoque para la gestión organizacional y toma de decisiones hecho posible por Ethereum.

Ethereum amplió lo que es posible con las blockchains al agregar la capacidad de descentralizar el manejo de datos más complejos que solo registros de propiedad de tokens. Ethereum hizo esto al permitir que las personas pongan smart contracts en una blockchain.

### ¿Qué es un smart contract?

Un smart contract es un programa de computadora que se ejecuta en la blockchain de Ethereum. Puede almacenar tanto funciones (bits de código que hacen cosas) como datos (información). Los smart contracts a menudo se comparan con máquinas expendedoras. Si pones entradas específicas, obtienes salidas específicas. Si me acerco a una máquina expendedora, inserto $1 y presiono el botón de "soda naranja", entonces obtendré una soda naranja si queda alguna en la máquina. Si no quedan más sodas naranjas, recuperaré mi dólar.

Los smart contracts funcionan de la misma manera, las personas pueden interactuar con ellos enviando información con la expectativa de recibir resultados o información específicos. Al igual que la máquina expendedora no tiene una personita dentro repartiendo sodas, los smart contracts son automáticos (nos atrevemos a decir, autónomos).

Si deseas aprender más sobre los smart contracts de Ethereum, la [documentación de Ethereum](https://ethereum.org/en/developers/docs/smart-contracts/) es el mejor lugar para sumergirte.

### El DAO controla los smart contracts críticos de Decentraland

La segunda cualidad importante de los smart contracts es su **capacidad de poseer otros smart contracts**.

Así es, cada smart contract tiene su propia dirección (al igual que la dirección de tu billetera de Ethereum) que le permite poseer otros smart contracts y criptomonedas.

Entonces, en términos un poco más técnicos, un DAO es uno o más smart contracts que pueden realizar tareas específicas predefinidas y mantener la propiedad de criptomonedas. Los DAOs están construidos de tal manera que solo realizarán sus tareas bajo condiciones específicas, como la aprobación de una propuesta votada por un grupo de personas que poseen un cierto token (como MANA, NAMEs o LAND). Todo esto se hace en una blockchain. De ahí el nombre, "organización autónoma descentralizada".

El DAO de Decentraland también posee una suma de MANA y otros tokens junto con los smart contracts de LAND y Estate. [Este fondo](https://governance.decentraland.org/transparency/) se ha reservado para ayudar a patrocinar subvenciones comunitarias y ayudar a hacer crecer la plataforma Decentraland de acuerdo con las decisiones y direcciones votadas por la comunidad.

{% hint style="warning" %}
**📔 Nota** El DAO no posee, y por lo tanto no puede modificar, el [smart contract de MANA](https://etherscan.io/address/0x0f5d2fb29fb7d3cfee444a200298f468908cc942#readContract).

El propietario del contrato MANA es el [contrato TokenSale](https://etherscan.io/address/0xa66d83716c7cfe425b44d0f7ef92de263468fb3d#readContract). El propietario del contrato TokenSale es un contrato separado que se autodestruyó en el despliegue ([como puedes ver en Etherscan aquí](https://etherscan.io/address/0xdf861993edbe95bafbfa7760838f8ebbd5afda9f)). Esto significa que no hay otro contrato o billetera con los permisos para modificar o pausar el suministro de MANA.
{% endhint %}

Hay otra información que el DAO controla también, como la lista de nombres dañinos u ofensivos que no están permitidos en Decentraland, una lista de ubicaciones notables (POIs o Puntos de Interés) para ser promovidos a nuevos usuarios, y la lista de servidores administrados por la comunidad que alojan el mundo virtual de Decentraland.

Transferir cualquiera de los MANA del DAO, modificar los smart contracts de LAND o Estate, o modificar cualquiera de la otra información listada controlada por el DAO **solo se puede hacer** con la aprobación de los poseedores de MANA, NAMEs y LAND.
