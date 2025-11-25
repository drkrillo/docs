---
description: Directrices para Subir Wearables al Editor
---

# Uploading Wearables

Una vez que exportes tu wearable, tendrás que subirlo al builder. Este documento cubrirá el proceso de subir wearables.

### Subiendo Tu Archivo

Recuerda que necesitas crear una colección antes de poder subir tu archivo. Si no sabes cómo hacer eso, consulta [Creating a Collection](https://github.com/decentraland/docs-creator/blob/main/creator/wearables-and-emotes/manage-collections/creating-a-collection.md). Para subir tu wearable, simplemente arrastra y suelta el archivo en la ventana _**New Item**_ o busca en tu computadora. Detectará automáticamente si el archivo es un emote o wearable. **Recuerda que el tamaño máximo de archivo de la colección es 3MB**.

![](../images/wearables-and-emotes/uploading-wearables/01_new_item.png)

Cuando subas el archivo, se te pedirá que selecciones un body shape, ingreses un nombre y definas la rarity y la categoría. También puedes agregar la miniatura para el wearable.

#### **Body Shape**

Para asegurar que tus wearables puedan ser usados por los avatares previstos, necesitas subir archivos GLB separados para cada body shape. Si tienes dos versiones separadas del wearable, una para masculino y una para femenino, puedes agregar una de las representations durante el proceso de carga y luego agregar la otra más tarde usando el editor. Si tu wearable está destinado a ser unisex, asegúrate de subir un solo archivo GLB que esté diseñado para ajustarse tanto a versiones masculinas como femeninas.

![](../images/wearables-and-emotes/uploading-wearables/03_body_shape.png)

#### **Rarity**

Selecciona la Rarity de tu ítem.

![](../images/wearables-and-emotes/uploading-wearables/04_rarity.png)

|| Rarity    | Number of Items |
|| --------- | --------------- |
|| Unique    | 1               |
|| Mythic    | 10              |
|| Exotic    | 50              |
|| Legendary | 100             |
|| Epic      | 1,000           |
|| Rare      | 5,000           |
|| Uncommon  | 10,000          |
|| Common    | 100,000         |

#### **Category**

Los wearables están organizados en diferentes categorías, dependiendo de qué parte de un avatar modifican. Selecciona la categoría apropiada para tu ítem.

![](../images/wearables-and-emotes/uploading-wearables/05_category.png)

#### **Custom Thumbnails**

Puedes agregar tu propia miniatura personalizada haciendo clic en el ícono de cámara y buscando en tu computadora. **La miniatura debe ser un archivo .png cuadrado de 256px con fondo transparente.** Las colecciones que contengan miniaturas sin fondos transparentes no serán aceptadas por el Curation Committee.

![](../images/wearables-and-emotes/uploading-wearables/06_thumbnail.png)

{% hint style="warning" %}
⚠️ Tener un buen render de tu wearable es crucial para hacerlo más atractivo para los usuarios potenciales en el marketplace. **Es importante evitar agregar cualquier gráfico que no sea el wearable en sí, porque esto puede causar que el comité de curación lo rechace.**

![](../images/wearables-and-emotes/uploading-wearables/07_thumbnail.png)
{% endhint %}

#### **Properties**

Debajo de la miniatura encontrarás las propiedades de tu wearable, número de triángulos de tu modelo, número de materiales y texturas.

![](../images/wearables-and-emotes/uploading-wearables/08_properties.png)

### **Subiendo Mouth, Eyes and Eyebrows**

Las categorías mouth, eyes y eyebrows tienen un comportamiento diferente en el editor porque estos son solo archivos .png. Para subirlos simplemente arrastra y suelta el archivo png como una imagen transparente (256X256 pixels). Mouth será automáticamente teñido por el color de piel, lo mismo para las eyebrows teñidas por el color del cabello.

{% hint style="warning" %}
Si quieres que el asset esté enmascarado, para que una parte del mouth o eyebrows no se vea afectada por el teñido, sube un archivo zip con tanto el png como los archivos de máscara. Recuerda que el archivo de máscara debe tener un sufijo "\\\_mask" para funcionar.
{% endhint %}

![](../images/wearables-and-emotes/uploading-wearables/02_mouth_wearable.png)

Después de subir tus wearables terminarás con una pantalla como esta, que muestra los ítems en tu colección.

![](../images/wearables-and-emotes/uploading-wearables/11_list_wearables.png)

### **Estableciendo el Precio de Tus Wearables**

Una vez que publiques tu colección y sea aprobada, puedes entonces habilitar las ventas en tu colección.

![](../images/wearables-and-emotes/uploading-wearables/25_enable_sales.png)

Puedes establecer el precio de tu wearable haciendo clic en _**Put up for sale**_. Todo esto se puede editar en cualquier momento, así que no te preocupes si quieres cambiarlo más tarde. Los precios se establecen en MANA. Recuerda que cuando acuñas wearables, se acuñan directamente en Matic/Polygon. Cuando un usuario compre tu ítem, la transacción se realizará en MANA de Matic/Polygon.

También podrías _**Make it Free**_, lo que significa que el precio se establecerá como 0 MANA y la dirección de beneficiario será nula. Ten en cuenta que hacerlo gratis (venta primaria) no impide que se venda a cualquier precio como venta secundaria.

No olvides establecer la dirección de beneficiario, que es la que recibirá el MANA de tus ventas. Puedes usar cualquier dirección de Ethereum que desees. Para completar automáticamente la dirección con la que iniciaste sesión, haz clic en _**I'm the Beneficiary.**_

![](../images/wearables-and-emotes/uploading-wearables/12_set_price.png)

Pon el ítem a la venta y volverás a la lista de wearables en tu colección. Cuando hagas clic en el ítem, obtendrás su información general. Haz clic en el botón _**Preview**_ para verlo en el editor.

![](../images/wearables-and-emotes/uploading-wearables/13_click_item.png)

## El Editor

Una vez que hagas clic en _**Preview**_, tendrás el editor abierto. Puedes editar toda la información de tu wearable, así como agregar nuevas, como descripción, tags y overrides. También es aquí donde agregas otra representation de body shape a tu wearable.

![](../images/wearables-and-emotes/uploading-wearables/14_editor.png)

### **Description**

Esta es una breve declaración describiendo tu ítem que se mostrará en el marketplace.

### **Overrides**

Los overrides determinan qué categorías de Wearable o partes del cuerpo del avatar tu ítem ocultará. Por ejemplo, un sombrero con cabello adjunto podría necesitar ocultar la categoría _Hair_. Un casco de buzo de aguas profundas puede requerir ocultar accesorios de cabeza como aretes, eyewear, tiaras, etc., que no serían visibles. Se pueden seleccionar múltiples opciones para cada override.

* **Base Body**: Esto se refiere a partes centrales del avatar como la _head_ y las _hands_. Por ejemplo, si estás creando un ítem de **Handwear** como una mano mecánica de robot, probablemente necesitarás ocultar _hands_ para prevenir superposición y clipping.
* **Wearables**: Esto incluye otras categorías de Wearable. Puedes ocultar múltiples categorías. Para más detalles sobre cada categoría y cómo interactúan los ítems, consulta [**Creating Wearables**](https://docs.decentraland.org/creator/wearables/creating-wearables/).

{% hint style="warning" %}
Nota: Los overrides que selecciones serán la configuración predeterminada sugerida para tu Wearable. Sin embargo, los usuarios pueden personalizar qué Wearables están ocultos o mostrándose desde el Backpack.
{% endhint %}

### **Tags**

Los tags son simplemente palabras descriptivas que los usuarios pueden usar al buscar o filtrar ítems. ¡Estos son relevantes para competencias o eventos!

### **VRM Export Permission**

Cuando esta propiedad está habilitada, permitirá a los propietarios de tu ítem incluirlo en VRM Avatar Exports para que puedan mostrarlo fuera de Decentraland.

### **Outline**

El toggle Outline controla si tu wearable es compatible con el sistema de renderizado de contorno de Decentraland (usado en configuraciones de calidad Medium/High). Por defecto, esto está habilitado para todos los nuevos wearables.

Deberías deshabilitar esta opción si tu wearable:

* Tiene contornos creados manualmente en el modelo
* Usa normales invertidas
* Muestra artefactos visuales cuando se aplica el efecto de contorno

La mayoría de los wearables funcionan bien con la configuración predeterminada (habilitado). Solo deshabilita esto si notas problemas visuales relacionados con el contorno en tu wearable. Una vez que deshabilites el contorno, necesitarás guardar los cambios haciendo clic en _**Save**_.

![](../images/wearables-and-emotes/uploading-wearables/26_outline.png)

### **Agregando Otra Representation**

Si tu wearable tiene una representation diferente para masculino y femenino necesitarás subir otro archivo. Hasta ahora, solo tienes uno subido. En el ejemplo, era la versión femenina del Krampus Sweater. Para agregar la otra representation, haz clic en los tres puntos (_…_) en la parte superior derecha, junto a _**Properties**_ y selecciona _**Add male/female**_ representation. En el ejemplo a continuación, necesitábamos la versión masculina.

![](../images/wearables-and-emotes/uploading-wearables/15_add_variation.png)

Una vez que hagas clic en eso, obtendrás la ventana Add Male/Female representation, arrastra y suelta el otro archivo de representation para subirlo. Una vez subido, aparecerá otra ventana mostrando el wearable y si todo está bien, simplemente haz clic en Save.

![](../images/wearables-and-emotes/uploading-wearables/16_add_male_representation_01.png) ![](../images/wearables-and-emotes/uploading-wearables/17_add_male_representation_02.png)

### **Preview**

Para previsualizar tu wearable, pasa tu mouse sobre el ícono de wearable en la parte superior izquierda y haz clic en el símbolo de ojo.

![](../images/wearables-and-emotes/uploading-wearables/18_preview_wearables_03.gif)

Haciendo clic en el ícono en la parte inferior izquierda podrás editar el avatar. Esto es muy útil si tienes una versión masculina y femenina de tu wearable, así que asegúrate de verificar cómo se ven ambas versiones en el editor, probando diferentes emotes para identificar si hay problemas de skinning y mezclando otros wearables para ver cómo combina con diferentes ropas. Cuando hayas terminado de editar tu wearable, haz clic en _**Save**_.

![](../images/wearables-and-emotes/uploading-wearables/19_edit_avatar_wearable_03.gif)

### **Probando en el Mundo**

Incluso después de probar el wearable en el editor, es importante verificar cómo realmente se verá y se comportará en Decentraland. Para probarlo en el mundo, ve a la pestaña Collections. Selecciona la colección deseada y haz clic en el botón _**See in World**_.

![](../images/wearables-and-emotes/uploading-wearables/20_test_in_world_01.png)

Después de hacer clic, aparecerá el siguiente pop-up. Seleccionar _**Empty Parcels**_ te teletransportará a un lugar sin demasiado contenido, que cargará más rápido. Seleccionar _**Genesis Plaza**_ te llevará a la plaza principal.

![](../images/wearables-and-emotes/uploading-wearables/23_see_in_world.png)

Una vez que selecciones See in world, se abrirá una nueva pestaña en tu navegador y obtendrás este mensaje.

![](../images/wearables-and-emotes/uploading-wearables/27_wearable_preview.png)

Haz clic en _**TRUST PEER-TESTING.DECENTRALAND.ORG**_ y aparecerá un pop-up. Simplemente haz clic en Open Decentraland. Para probar tu wearable, ve al backpack y equípalo.

![](../images/wearables-and-emotes/uploading-wearables/28_wearable_world.gif)

### **Antes de Publicar**

Asegúrate de establecer el precio correctamente, agregar una buena descripción y verificar si toda la información y configuraciones son correctas. Si has completado toda la información necesaria verás _**Done**_ como el estado de tu ítem.
