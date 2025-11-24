---
description: Obtén información de parcelas land, estates o direcciones usando el CLI.
metaLinks:
  alternates:
    - https://app.gitbook.com/s/oPnXBby9S6MrsW83Y9qZ/marketplace/info-from-land
---

# Información de LAND

Puedes usar el CLI de Decentraland para consultar información directamente de tokens LAND en la blockchain y de los archivos de escena subidos al servidor de contenido.

Para ejecutar estos comandos, primero debes [Instalar el CLI](../../creator/sdk7/getting-started/using-the-cli.md).

### Obtener información sobre una escena

El comando `dcl info` devuelve el contenido del archivo _scene.json_ de una escena, incluyendo propietario, información de contacto y parcelas en la escena.

* `dcl info` desde la carpeta de la escena devuelve información sobre esa escena específica.
*   `dcl info x,y`, donde _x_ e _y_ son coordenadas de parcela, devuelve información sobre la escena en esa ubicación.

    \{% hint style="warning" %\} **📔 Nota**: No agregues un espacio entre ambas coordenadas. \{% endhint %\}
* `dcl info id`, donde _id_ es un id de estate, devuelve información sobre la escena con ese id de estate.

El comando `dcl status` devuelve una lista con los archivos desplegados en una escena. Esto solo incluye nombres de archivo y tamaños. No puedes acceder al contenido de los archivos a través del CLI.

* `dcl status` desde la carpeta de la escena devuelve información sobre esa escena específica.
* `dcl status x,y`, donde _x_ e _y_ son coordenadas de parcela (sin espacios entre ellas), devuelve información sobre la escena en esa ubicación.

{% hint style="warning" %}
**📔 Nota**: No agregues un espacio entre ambas coordenadas.
{% endhint %}

* `dcl status id`, donde _id_ es un id de estate, devuelve información sobre la escena con ese id de estate.

{% hint style="warning" %}
**📔 Nota**: Todo lo que se sube a nuestro servidor de contenido es público y accesible a través de esa red. Cuando despliegas una escena, por defecto no estás subiendo el código fuente original de la escena, en su lugar subes una versión que está compilada a JavaScript minificado, que es mucho menos legible.
{% endhint %}

### Obtener información de una dirección de billetera

El comando `dcl info` también te permite ver información sobre una billetera de Ethereum y los tokens LAND que posee.

* `dcl info xxxx`, donde _xxxx_ es la dirección de Ethereum que te interesa, devuelve una lista de todas las parcelas LAND y estates propiedad de esa dirección y sus detalles.
