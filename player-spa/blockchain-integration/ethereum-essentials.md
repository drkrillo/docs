---
description: Cómo Decentraland usa la blockchain de Ethereum.
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/UWbeOuof0RchgfvQfPTf/blockchain-integration/ethereum-essentials
---

# Acerca del Blockchain

Todas las blockchains son en esencia bases de datos descentralizadas que se distribuyen entre las máquinas de una red. Las transacciones se agrupan en "bloques" y se procesan secuencialmente para formar una _cadena_ de eventos.

Ethereum es una de las blockchains más populares. Lo que la distingue de otras, como Bitcoin, es que usa la blockchain como almacenamiento para más que solo un registro de transacciones de moneda. Ethereum puede almacenar información más compleja para distinguir diferentes tipos de tokens o incluso manejar tokens únicos con características específicas. La blockchain de Ethereum también ejecuta smart contracts, estos permiten ejecutar transacciones más complejas que también pueden depender de eventos acordados.

Decentraland usa la blockchain de Ethereum para registrar la propiedad de los activos digitales y otros artículos comerciables que pueden ser leídos y reaccionados por una escena 3D.

La blockchain no se usa para almacenar el estado de la escena, la posición del jugador o cualquier cosa que necesite cambiar en tiempo real cuando un jugador interactúa con una escena, todo eso se almacena localmente en la máquina de cada jugador o en un servidor privado propiedad del dueño de la escena. Los desarrolladores de cada escena deben elegir qué información vale la pena almacenar en la blockchain y qué almacenar en un servidor privado.

## Billeteras

Los tokens de Ethereum se mantienen en billeteras. Una billetera de Ethereum puede contener varios tokens, incluyendo Ether, MANA, LAND y otros tokens que pueden ser usados por juegos o experiencias en Decentraland.

Hay muchos proveedores de billeteras donde puedes mantener tokens de Decentraland. Para usar el Marketplace, o para entrar a Decentraland, debes usar una billetera que esté integrada a tu navegador web, por lo que recomendamos que uses:

* [Metamask](https://metamask.io/)
* Billeteras de hardware [Trezor](https://trezor.io/)/[Ledger](https://www.ledger.com/)

Cada billetera tiene una clave pública y una privada. El hash de tu clave pública es la dirección única de tu billetera, utilizada para enrutar transacciones e identificar a un jugador. Tu clave privada es utilizada por tu billetera para firmar cada transacción que envías a la red y certificar que realmente fue enviada por ti. Tu clave privada también se usa para restaurar tu billetera en caso de que olvides tu contraseña, así que guárdala en un lugar seguro y no la compartas con nadie.

En Decentraland, las identidades de los jugadores se construyen alrededor de las billeteras. Como las claves públicas de las billeteras son únicas, tu escena puede usarlas para identificar a un usuario de Decentraland de manera persistente. Las billeteras también pueden contener diferentes tokens que pueden darle a un jugador un avatar único, un artículo wearable, permisos para entrar a escenas que eligen restringir el acceso, un arma especial para usar en un juego, etc.

## Transacciones

Las transacciones hacen cambios a la información que está almacenada en la blockchain. Las transacciones típicas involucran tokens cambiando de propietarios, por ejemplo, el usuario A dando su token LAND al usuario B a cambio de una cantidad de tokens MANA. En la red de Ethereum, sin embargo, una transacción también puede significar cambiar la información que está almacenada sobre un token sin cambiar su propietario. Por ejemplo, cambiar la descripción de una parcela, o fusionar varias parcelas en un Estate.

Todas las transacciones que ocurren en la cadena principal de Ethereum tienen un costo que se paga en tokens Ether. Esta tarifa se refiere como la tarifa de 'gas', y se paga al usuario de la red que 'mina' la transacción.

Cuando solicitas que una transacción tenga lugar, estableces el precio de gas que estás dispuesto a pagar para que la transacción sea minada. Las transacciones que ofrecen precios más altos se minan más rápido, ya que los mineros dan prioridad a estas. Los precios de mercado para estas transacciones oscilan regularmente, tienden a ser más caros cuando hay un mayor uso de la red. Asegúrate de que lo que ofreces no esté por debajo del precio de mercado, de lo contrario tu transacción podría permanecer en un pool sin procesar indefinidamente.

Todas las transacciones deben ser firmadas por una dirección de Ethereum, usando la clave privada de la dirección. Esto es lo que certifica que la transacción fue llevada a cabo por esa dirección.

#### Validación de transacciones

Las transacciones de blockchain no son inmediatas, requieren tiempo para ser "minadas" por uno de los nodos en la red, y luego ser propagadas a través del resto de las máquinas. Cuantas más transacciones sean solicitadas por la red, más tiempo tardan en ser validadas.

En términos breves, así es como se valida una transacción:

1. Ocurre una nueva transacción, va a un pool de transacciones no confirmadas.
2. Una de las máquinas en la red resuelve con éxito un algoritmo para minar un nuevo "bloque" que contiene un puñado de transacciones de este pool, incluida esta. Adjunta este nuevo bloque al final de la cadena.
3. El bloque se comparte con otras máquinas de la red. Cada máquina verifica que cada transacción en un bloque sea válida y verifica el hash del bloque para asegurar que sea legítimo, luego lo agrega a su propia versión de la cadena.
4. El nuevo bloque se propaga por toda la red. Hay un entendimiento compartido universalmente de que esta transacción ha tenido lugar.

#### Sidechains

Decentraland se está asociando con [Matic](https://matic.network/) para crear una _sidechain_ (un tipo especial de blockchain) que podrá manejar transacciones más rápido y más barato que la red principal de Ethereum. Esta sidechain será ideal para transacciones dentro del juego, ya que los cambios pueden ocurrir más cerca del tiempo real y a un costo muy bajo. Para transacciones que involucren artículos valiosos, todavía recomendaremos la cadena principal de Ethereum, ya que será más segura.

Cada desarrollador que trabaje en una escena podrá elegir si usar la cadena principal, la sidechain o una combinación de ambas para diferentes transacciones.

La sidechain se mantendrá interoperable con la cadena principal de Ethereum. Podrás cargar tokens de la cadena principal a la sidechain y viceversa. Las transacciones que tienen lugar en la sidechain eventualmente se reflejan en la cadena principal cuando los tokens "salen" de vuelta a la cadena principal.

#### Activar transacciones desde una escena

El código de tu escena puede activar transacciones, tanto en la cadena principal de Ethereum como en la sidechain de Decentraland. Podrías tener una tienda en tu escena que venda tokens (como NFTs), o tener un juego que recompense artículos del juego a los jugadores que logren ciertos objetivos.

El usuario siempre debe aprobar estas transacciones explícitamente en su cliente de Ethereum. Por ejemplo, cuando se usa Metamask, Metamask solicita al usuario que acepte cada transacción antes de que se procese.

## Tipos de tokens

Diferentes tipos de tokens pueden ser manejados en la red de Ethereum. Algunos estándares han surgido que agrupan tokens que comparten las mismas características.

En Decentraland, puedes usar tokens para representar artículos que se relacionen con tu juego o experiencia, como un arma o un trofeo. Como los tokens se mantienen en la billetera de un jugador, acompañan a un jugador de escena en escena, por lo que cada escena puede elegir si y cómo quieren reaccionar a cada tipo existente de token.

Lee [Qué son los NFTs](https://decentraland.org/blog/technology/what-are-nfts/) en nuestro blog para una mirada más profunda del surgimiento y evolución de los tokens no fungibles.

#### Tokens Fungibles

Si un artículo es fungible, entonces puede ser sustituido o intercambiado por cualquier artículo similar. Las monedas fiat, como el dólar estadounidense, son fungibles. Un billete de dólar puede ser intercambiado por cualquier otro billete de dólar.

Los tokens de criptomoneda como Bitcoin, Ethereum y MANA son todos fungibles porque una unidad de token puede ser intercambiada por cualquier otra unidad de token.

También podrías crear tokens fungibles personalizados para usar en escenas de Decentraland y usarlos para representar artículos que sean todos iguales y no tengan propiedades distintivas o personalizables entre ellos. Podrías, por ejemplo, crear un juego que gire en torno a recolectar una gran cantidad de artículos idénticos y representarlos a través de un token fungible. También podrías usar un token fungible para representar un boleto dorado que le da a los jugadores que lo poseen acceso a una región o servicio específico.

_ERC20_ es el estándar más aceptado para tokens fungibles en la Red de Ethereum. MANA está construido sobre este estándar.

#### Tokens No Fungibles

Los tokens no fungibles (o NFTs) tienen características que hacen que cada unidad sea objetivamente diferente de otras. Las parcelas de LAND en Decentraland son NFTs, ya que la ubicación de cada parcela es única. La adyacencia a otras parcelas, carreteras o distritos hace que estas ubicaciones sean relevantes para los propietarios de tokens.

En Decentraland, puedes usar NFTs para representar artículos del juego como avatares, wearables, armas y otros artículos del inventario. Podrías, por ejemplo, usar un solo tipo de NFT para representar todas las armas en tu juego y diferenciarlas estableciendo diferentes propiedades en estos NFT.

Los NFTs se pueden usar para proporcionar bienes digitales demostrablemente escasos. Debido a la escasez legítima posible gracias a la blockchain, los compradores pueden estar seguros de que el arte que compran es, de hecho, raro. Esto le da al arte digital un valor real que nunca antes habíamos visto.

Los artículos del juego tendrán una historia que se almacena en la blockchain. Esta historia podría considerar un artículo más valioso, por ejemplo, si se usó para lograr grandes logros o fue usado por alguien que es admirado.

Dependiendo del contrato que describe el token, cada NFT podría ser inmutable, o podrías permitir a los jugadores personalizar y cambiar ciertas características sobre ellos si así lo eligen.

_ERC721_ es el estándar más aceptado para tokens no fungibles en la Red de Ethereum. Los tokens LAND siguen el estándar ERC721.

## Smart Contracts

Un contrato consiste tanto en código (sus métodos) como en datos (su estado) que reside en una dirección específica en la blockchain de Ethereum.

Los métodos en un contrato siempre se llaman a través de una transacción que tiene el campo _to_ establecido en la dirección del contrato. El código que ejecuta el método del contrato puede incluir llamadas a otros contratos, estas activan más transacciones que tienen el campo _from_ establecido en la dirección del contrato.

Un contrato no puede activar ninguna acción por sí solo o basado en un evento de tiempo. Todas las acciones realizadas por un smart contract siempre surgen de una transacción que llama a una de las funciones del contrato.

Puedes usar smart contracts para condicionar transacciones basadas en condiciones personalizadas. Por ejemplo, los jugadores podrían apostar en el resultado de un juego, y los pagos correspondientes ocurrirían tan pronto como el resultado del juego se informe al contrato.

Todo el código de un smart contract es público para quien quiera leerlo. Esto permite a los desarrolladores crear reglas públicamente verificables.

Todos los Tokens están definidos por un smart contract que especifica sus características y qué se puede hacer con él. Decentraland ha escrito y mantiene varios smart contracts. Los tokens LAND y MANA están definidos por los contratos _LANDregistry_ y _MANAtoken_ respectivamente.

Puedes encontrar la dirección de cada contrato creado por Decentraland en [Contratos inteligentes de Decentraland](https://contracts.decentraland.org/addresses.json).

Puedes leer el código completo de cada uno de esos contratos, ya que es información pública en la blockchain. Puedes encontrar el contrato por nombre en [Etherscan](https://etherscan.io/contractsVerified) y leer su contenido allí.

## dApps

Las _dApps_ (aplicaciones descentralizadas) son aplicaciones que se construyen sobre smart contracts y la blockchain.

Una dApp puede ser tan simple como algo que valida que tu billetera posee un cierto token y te permite usar un servicio. O puede ser una aplicación completamente desarrollada con su propia interfaz de usuario, como el Marketplace de Decentraland.

## Red de prueba Sepolia

Antes de desplegar un smart contract, crear un nuevo tipo de token o una escena de Decentraland que dependa de transacciones en la red de Ethereum, necesitas asegurarte de que no tenga errores o brechas que usuarios maliciosos puedan explotar.

La red de prueba Sepolia es una versión alternativa de Ethereum que está específicamente hecha para ejecutar pruebas.

Los tokens en la red Sepolia no tienen valor real, por lo que puedes permitirte cometer errores sin correr ningún riesgo real. Puedes reponer cualquier token perdido gratis usando un faucet:

* Faucet de Ether Sepolia ([https://www.alchemy.com/faucets/ethereum-sepolia/](https://www.alchemy.com/faucets/ethereum-sepolia/))

Si estás desarrollando una escena que activa transacciones, probar estas transacciones en la red Sepolia es gratis, ya que los tokens que envías no tienen valor. En la red principal de lo contrario tendrías que pagar como mínimo una tarifa de gas real en Ether por cada transacción de prueba que realices.

Una vez que estés seguro de que tu código funciona como se espera y no puede ser explotado, puedes desplegar en la red principal de Ethereum.

## Reorgs de blockchain

Ocasionalmente, múltiples máquinas crearán bloques nuevos alternativos aproximadamente al mismo tiempo. Esto es un problema, porque esto bifurca la cadena en dos versiones divergentes que potencialmente podrían contradecirse entre sí. Cuando ocurre una bifurcación, Ethereum resuelve esto siempre dando prioridad a la cadena más larga y descartando cualquier cadena más corta. Aunque es posible que dos cadenas rivales existan al mismo tiempo, pronto una de las dos cadenas agregará otro bloque y superará a la otra. Debido al tiempo que toma resolver los algoritmos de minería, se vuelve cada vez más difícil para las cadenas rivales seguir creciendo en perfecta sincronía entre sí. Tarde o temprano una prevalecerá sobre la otra.

Cuando una cadena supera a la otra y se resuelve la disputa, las máquinas que habían adoptado la cadena más corta necesitan hacer ajustes. Esto es lo que se conoce como una "reorg". Necesitan retroceder en todas las transacciones incluidas en los bloques de la rama en la que están hasta que lleguen al punto en el que ocurrió la bifurcación. Luego necesitan agregar los nuevos bloques de la rama más larga que se considera legítima.

Las transacciones retrocedidas pueden volver al pool de transacciones pendientes hasta que sean recogidas nuevamente por un minero (o sean descartadas). Cualquier tarifa de gas pagada por estas transacciones también se retrocede.

Los bloques que se acaban de agregar al final de una cadena tienen una posibilidad sustancial de ser retrocedidos debido a los mecanismos explicados anteriormente. A medida que se agregan bloques subsiguientes al final de la cadena, se vuelve menos y menos probable que los bloques que están más atrás en la blockchain puedan ser retrocedidos, porque eso requeriría una reorg más grande. Debido a esto, cada nuevo bloque que se agrega al final de la cadena después de una transacción se llama una confirmación para esa transacción.

Al crear aplicaciones (o escenas) que usan información fuera de la blockchain, debes ser consciente de la ocurrencia de reorgs. Es posible que desees considerar las transacciones como verificadas solo cuando haya ocurrido un cierto número de confirmaciones, y la transacción ya no esté al final de la cadena.

Usar varias confirmaciones hará que la información sea muy estable, pero las transacciones tardarán mucho tiempo en reflejarse.
