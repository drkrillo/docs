# Cómo crear nuevos componentes para ECS7

Para crear nuevos componentes, necesitamos hacer un par de cosas antes de comenzar a codificarlos en el renderizador.
Hay una guía paso a paso que debes seguir. Listaré todos los pasos, y luego puedes seguir cada paso con una explicación más larga.

1. Crear la definición proto en [@dcl/protocol](https://github.com/decentraland/protocol)
2. Generar el nuevo código TypeScript proto en el [js-sdk-toolchain](https://github.com/decentraland/js-sdk-toolchain)
3. Crear un test para `js-sdk-toolchain`
4. Generar el nuevo código C# proto en el proyecto
5. Codificar el nuevo componente
6. Asegurar que el componente sigue la convención


## Crear la definición proto en [@dcl/protocol](https://github.com/decentraland/protocol)

Para crear una definición, debes ir a este repo: https://github.com/decentraland/protocol

1. Crear la definición proto en esta carpeta: https://github.com/decentraland/protocol/tree/main/ecs/components
2. Crear un PR con los nuevos cambios

> **_NOTA:_**  Después de crear el PR, un Bot de GitHub comentará con el enlace del paquete para probar el PR. Puedes usar ese enlace para pruebas en los siguientes pasos

Cosas a tener en cuenta
- Estamos usando proto 3, por lo que toda la definición del proto debe compilar con su [sintaxis](https://developers.google.com/protocol-buffers/docs/proto3/)
- Tenemos algunos tipos comunes que no pueden ser recreados
- El proto debe tener la definición básica
- Debes agregar el siguiente código para enumerar el componente
```
import "common/id.proto";
option (ecs_component_id) = 1100;
```

Ejemplo de .proto
```
syntax = "proto3";

import "common/id.proto";
option (ecs_component_id) = 1020;

message PBAudioSource {
  optional bool playing = 1;
  optional float volume = 2; // default=1.0f
  optional bool loop = 3;
  optional float pitch = 4; // default=1.0f
  string audio_clip_url = 5;
}
```

## Generar el nuevo código TypeScript proto en el [js-sdk-toolchain](https://github.com/decentraland/js-sdk-toolchain)

Descargar el siguiente repo: https://github.com/decentraland/js-sdk-toolchain

Dentro de él, navegar a `packages/dcl/ecs` (`https://github.com/decentraland/js-sdk-toolchain/tree/main/packages/%40dcl/ecs`)

Y allí, puedes ejecutar
```
npm install @dcl/protocol@next
````

O el comando generado por el Bot de GitHub en tu PR de @dcl/protocol (esto debe ser temporal para probar los PR).

Luego ejecutar los siguientes comandos en la raíz del proyecto `js-sdk-toolchain`:

```
make install
make build
```

Y empujar el código generado.

## Generar el nuevo código C# proto en el proyecto

Para generar el código C#, necesitamos ir a la ruta `protocol-gen` en la raíz del repositorio `@decentraland/unity-renderer`. Y ejecutar los siguientes comandos:

```
npm install
npm run build-components
```

Para actualizar a la última versión del `@dcl/protocol` (rama main), debemos actualizar usando

```
npm install @dcl/protocol@next
npm run build-components
```

Para probar un PR, podemos usar una URL generada por el Bot de GitHub en el PR de `@dcl/protocol`:
Ejemplo:
```
npm install "https://sdk-team-cdn.decentraland.org/@dcl/protocol/branch//dcl-protocol-1.0.0-3143233696.commit-45f1290.tgz"
npm run build-components
```

Después de mergear el PR de @dcl/protocol, debemos usar el `@dcl/protocol@next` y generar el código.

## Codificar el nuevo componente

Ahora es momento de implementar la funcionalidad del nuevo componente.

Los componentes tienen cinco partes esenciales.

- ComponentID
El ID que el componente usará. Debe ser único y generado desde la definición proto

- Model
Este es el modelo del componente. Estos son los datos que usaremos para manejar el componente. Ha sido autogenerado con la generación proto y tiene el mismo nombre del archivo proto con un PB al frente. Por ejemplo, si tienes una definición `BoxShape.proto` la clase generada del modelo será `PBBoxShape`

- Component Handler
El manejador de componente gestionará toda la funcionalidad del componente. En esta clase debes implementar la `IECSComponentHandler<ModelClass>` (ModelClass es el modelo. Es una clase generada desde el proto, el nombre será PB + nombre del archivo .proto).
Esta interfaz tiene 3 métodos que son importantes implementar para crear un componente

```
        void OnComponentCreated(IParcelScene scene, IDCLEntity entity);
        void OnComponentRemoved(IParcelScene scene, IDCLEntity entity);
        void OnComponentModelUpdated(IParcelScene scene, IDCLEntity entity, ModelType model);
```

- Serializer
Cada componente es responsable de implementar su serialización y deserialización del componente. Este serializador debe poder serializar/deserializar a array de bytes
- Register
Esto registrará el componente en el sistema, conectándolo al sistema. Este registro registrará el componente en la fábrica y el escritor de componentes

El diseño de los componentes es evitar la herencia, por lo que animamos a usar funciones puras tanto como sea posible

Para crearlos, debemos seguir los siguientes pasos

1. Crear la carpeta del componente y ensamblado. Tenemos todos los componentes bajo la siguiente carpeta `DCLPlugins/ECS7/ECSComponents`.
Necesitas crear una carpeta y un nuevo ensamblado que contendrá el componente
2. En el nuevo ensamblado, debes referenciar el siguiente `DCL.ECSComponents.Data`. Esto referenciará el nuevo modelo del componente que acabas de actualizar
3. Debes crear el manejador de componente con toda la lógica (Mira `ECSBoxShapeComponentHandler.cs` como ejemplo)
4. Debes crear la clase serializadora (probablemente puedas copiarla de otra clase y adaptarla)
5. Debes crear la clase register
```sh
   public static class AudioSourceSerializer
    {
        public static byte[] Serialize(PBAudioSource model)
        {
            int size = model.CalculateSize();
            byte[] buffer = new byte[size];
            CodedOutputStream output = new CodedOutputStream(buffer);
            model.WriteTo(output);
            return buffer;
        }

        public static PBAudioSource Deserialize(object data)
        {
            return PBAudioSource.Parser.ParseFrom((byte[])data);
        }
    }
```
6. Agregar el nuevo registro a la clase `ECS7ComponentsComposer` con su ID correspondiente
```sh
   public class ECS7ComponentsComposer : IDisposable
    {
        private readonly TransformRegister transformRegister;
        private readonly SphereShapeRegister sphereShapeRegister;
        private readonly BoxShapeRegister boxShapeRegister;
        private readonly PlaneShapeRegister planeShapeRegister;
        private readonly CylinderShapeRegister cylinderShapeRegister;
        private readonly AudioStreamRegister audioStreamRegister;
        private readonly AudioSourceRegister audioSourceRegister;

        public ECS7ComponentsComposer(ECSComponentsFactory componentsFactory, IECSComponentWriter componentsWriter)
        {
            transformRegister = new TransformRegister(ComponentID.TRANSFORM, componentsFactory, componentsWriter);
            sphereShapeRegister = new SphereShapeRegister(ComponentID.SPHERE_SHAPE, componentsFactory, componentsWriter);
            boxShapeRegister = new BoxShapeRegister(ComponentID.BOX_SHAPE, componentsFactory, componentsWriter);
            planeShapeRegister = new PlaneShapeRegister(ComponentID.PLANE_SHAPE, componentsFactory, componentsWriter);
            cylinderShapeRegister = new CylinderShapeRegister(ComponentID.CYLINDER_SHAPE, componentsFactory, componentsWriter);
            audioStreamRegister = new AudioStreamRegister(ComponentID.AUDIO_STREAM, componentsFactory, componentsWriter);
            audioSourceRegister = new AudioSourceRegister(ComponentID.AUDIO_SOURCE, componentsFactory, componentsWriter);
        }

        public void Dispose()
        {
            transformRegister.Dispose();
            sphereShapeRegister.Dispose();
            boxShapeRegister.Dispose();
            planeShapeRegister.Dispose();
            cylinderShapeRegister.Dispose();
            audioStreamRegister.Dispose();
            audioSourceRegister.Dispose();
        }
    }
```
¡Y ahora tienes tu componente agregado y funcionando!

## Asegurar que el componente sigue la convención
Hay una lista de verificación que necesitamos tener en cuenta al desarrollar nuevos componentes, esta parte intenta resumirlos.

- Unit test
Todos los componentes deben incluir unit test que cubran su funcionalidad, dispose y la serialización/deserialización al menos para asegurar que el componente funcionará
- Tener en cuenta qué sucede cuando el componente no está dentro de la escena (Revisar la clase `SceneBoundariesChecker` para más información)
- Si el componente renderiza algo en el mundo, debe agregar la información renderizable al data store, de esta manera agregamos la información del renderizador a la escena para que cuente hacia el límite
- Si el componente renderiza algo en el mundo, debe agregar el `MeshesInfo` a la entidad
- Debe ser lo más eficiente posible. Este código será ejecutado muchas veces, por lo que necesitamos asegurar que todo funcione lo más suave posible
- Debe funcionar con `Hot reload` en el modo preview. Si has codificado el `OnComponentRemoved` correctamente, esto funcionará automáticamente, pero el hot reload es una forma de probar que todo funciona bien con el dispose del componente
- Si el componente usa un recurso, debes implementar la gestión de recursos con un `AssetPromiseKeeper`. El componente debe notificar al `AssetPromiseKeeper` cuando el recurso es usado y cuando ya no se usa
