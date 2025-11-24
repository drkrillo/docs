# Componentes

Los componentes son piezas de software auto-contenidas diseñadas como cajas negras que reciben otros componentes (o ninguno) para realizar sus tareas. El objetivo principal es desacoplar secciones de código y maximizar la probabilidad.

## Estructura de Directorios

Todos los componentes DEBEN definirse dentro de un directorio dedicado que contenga al menos estos cuatro archivos:

* `component.ts` - Contiene el código de implementación del componente
* `types.ts` - Contiene la interfaz del componente y tipos expuestos
* `errors.ts` - Contiene todas las clases de error exportadas
* `index.ts` - Exporta la API pública del componente

### Ejemplo de Estructura

```
src/
└── components/
    └── my-component/
        ├── component.ts
        ├── types.ts
        ├── errors.ts
        └── index.ts
```

## Archivo de Tipos

Los componentes se conforman a una interfaz que define su API pública. Esta interfaz puede compartirse entre componentes intercambiables o usarse para entender los métodos expuestos por el componente.

Todos los componentes que tienen su propia interfaz DEBEN definirla en un archivo `types.ts` dentro del directorio del componente.

Otros tipos expuestos por el componente TAMBIÉN DEBEN colocarse en este archivo.

### Ejemplo: `types.ts`

```tsx
export type Something = {
  aValue: boolean
  anotherValue: string
}

export interface IMyComponent {
  myFunction: () => boolean
  getSomething: () => Something
}
```

### Tipos Compartidos

Los tipos compartidos entre múltiples componentes, como interfaces comunes, DEBEN colocarse en un archivo `types.ts` en el directorio raíz del código fuente del proyecto (`/src/types.ts`).

## Archivo del Componente

El archivo del componente contiene la función creadora del componente. Esta función sigue una convención específica de nomenclatura y estructura.

### Convención de Nomenclatura

Las funciones creadoras de componentes DEBEN nombrarse como: `create` + `ComponentName` + `Component`

**Ejemplo:** `createMyNewComponent`

### Firma de la Función

La función creadora DEBE:

1. Recibir un objeto de componentes como el primer parámetro conteniendo todas las dependencias
2. Opcionalmente recibir parámetros de configuración adicionales
3. Retornar un objeto conteniendo los métodos expuestos (públicos)

### Estructura del Componente

Al inicio de la función creadora del componente:

1. Extraer dependencias del objeto de componentes
2. Inicializar variables comunes (ej., loggers, configuración)
3. Definir funciones auxiliares internas
4. Definir métodos públicos
5. Retornar la API pública

### Ejemplo: `component.ts`

```tsx
import { IMyComponent, Something } from './types'
import { WrongStringError } from './errors'

export function createMyNewComponent(
  components: Pick<AppComponents, 'logs'>
): IMyComponent {
  const { logs } = components
  const logger = logs.getLogger('my-component')

  // Método interno - no expuesto
  function computeString(fstString: string, sndString: string): string {
    if (fstString.length === 0) {
      throw new WrongStringError()
    }
    
    return fstString + ' ' + sndString
  }

  // Método público - expuesto en objeto de retorno
  function myFunction(): boolean {
    return true
  }

  // Método público - expuesto en objeto de retorno
  function getSomething(): Something {
    logger.info('Getting something')
    
    return {
      aValue: true,
      anotherValue: computeString('aString', 'anotherString')
    }
  }

  // Retorna la API pública
  return {
    myFunction,
    getSomething
  }
}
```

## Archivo de Errores

El archivo de errores contiene clases de error personalizadas que un componente puede lanzar. Estos errores pueden usarse en otros componentes o controladores para identificar y manejar correctamente condiciones de error específicas.

### Ejemplo: `errors.ts`

```tsx
export class WrongStringError extends Error {
  constructor(message?: string) {
    super(message || 'Incorrect String')
    this.name = 'WrongStringError'
  }
}

export class ComponentNotInitializedError extends Error {
  constructor() {
    super('Component has not been initialized')
    this.name = 'ComponentNotInitializedError'
  }
}
```

### Mejores Prácticas para Manejo de Errores

* Crear clases de error específicas para diferentes condiciones de error
* Incluir mensajes de error significativos
* Establecer la propiedad `name` para que coincida con el nombre de la clase para facilitar la depuración
* Documentar qué errores pueden ser lanzados por cada método público

## Archivo Index

El archivo index sirve como el punto de entrada público para el componente, exportando solo lo que debería ser accesible a otras partes de la aplicación.

### Ejemplo: `index.ts`

```tsx
export { createMyNewComponent } from './component'
export type { IMyComponent, Something } from './types'
export { WrongStringError } from './errors'
```

## Mejores Prácticas

1. **Responsabilidad Única** - Cada componente debería tener un propósito claro
2. **Dependencias Explícitas** - Todas las dependencias deberían inyectarse a través del parámetro components
3. **Inmutabilidad** - Preferir estructuras de datos inmutables donde sea posible
4. **Manejo de Errores** - Usar clases de error personalizadas para diferentes condiciones de error
5. **Logging** - Usar el logger para depuración y monitoreo
6. **Type Safety** - Aprovechar completamente el sistema de tipos de TypeScript
7. **Documentación** - Documentar métodos complejos y lógica de negocio

## Ciclo de Vida del Componente

Algunos componentes pueden necesitar realizar operaciones de configuración o limpieza. WKC proporciona métodos de ciclo de vida:

* `[START_COMPONENT]` - Llamado cuando el componente inicia
* `[STOP_COMPONENT]` - Llamado cuando el componente se detiene

Ver la sección [Adaptadores](adapters.md) para ejemplos de componentes usando métodos de ciclo de vida.
