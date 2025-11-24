# Adaptadores

Los adaptadores son componentes responsables de manejar **procesos de I/O** e integraciones externas. Sirven como el puente entre su lógica de negocio y sistemas externos como bases de datos, APIs, sistemas de archivos o almacenamiento en caché.

## Propósito

Los adaptadores abstraen los detalles de implementación de dependencias externas, permitiendo:

* **Intercambiabilidad** - Intercambiar fácilmente implementaciones (ej., PostgreSQL → MongoDB)
* **Probabilidad** - Mockear dependencias externas en pruebas
* **Aislamiento** - Mantener la lógica de negocio independiente de preocupaciones de infraestructura

## Ubicación

Todos los componentes adaptadores DEBEN colocarse bajo el directorio `src/adapters` en el proyecto.

```
src/
└── adapters/
    ├── database/
    ├── storage/
    ├── cache/
    └── external-api/
```

## Interfaces Compartidas

Los adaptadores DEBERÍAN construirse con intercambiabilidad en mente. Definir interfaces compartidas en `src/types.ts` para que múltiples adaptadores puedan implementar el mismo contrato.

### Ejemplo: Interfaz de Storage

```tsx
// src/types.ts
export interface IStorage {
  set: (key: string, value: any) => Promise<void>
  get: (key: string) => Promise<any>
  delete: (key: string) => Promise<void>
}
```

## Adaptador de Memory Storage

Aquí hay una implementación simple de adaptador de almacenamiento en memoria, útil para pruebas o desarrollo.

### Estructura de Directorios

```
src/adapters/memory-storage/
├── component.ts
├── types.ts
├── errors.ts
└── index.ts
```

### Implementación: `component.ts`

```tsx
import { IStorage } from '../../types'

export function createMemoryStorageAdapter(): IStorage {
  // Estado interno - con alcance a esta instancia de componente
  const memory: Record<string, any> = {}

  async function set(key: string, value: any): Promise<void> {
    memory[key] = value
  }

  async function get(key: string): Promise<any> {
    return memory[key]
  }

  async function delete(key: string): Promise<void> {
    delete memory[key]
  }

  return {
    set,
    get,
    delete
  }
}
```

## Adaptador de Redis Storage

Aquí hay un adaptador Redis listo para producción que demuestra gestión de ciclo de vida.

### Implementación: `component.ts`

```tsx
import { createClient, RedisClientType } from 'redis'
import { IStorage } from '../../types'
import { RedisConnectionError } from './errors'

export function createRedisStorageAdapter(
  components: Pick<AppComponents, 'config' | 'logs'>
): IStorage {
  const { config, logs } = components
  const logger = logs.getLogger('redis-storage')
  
  const hostUrl = config.requireString('REDIS_URL')
  const client: RedisClientType = createClient({ url: hostUrl })

  // Método de ciclo de vida: llamado cuando el componente inicia
  async function start(): Promise<void> {
    try {
      await client.connect()
      logger.info('Redis client connected successfully')
    } catch (error) {
      logger.error('Failed to connect to Redis', error)
      throw new RedisConnectionError('Could not connect to Redis')
    }
  }

  // Método de ciclo de vida: llamado cuando el componente se detiene
  async function stop(): Promise<void> {
    try {
      await client.quit()
      logger.info('Redis client disconnected')
    } catch (error) {
      logger.error('Error disconnecting Redis client', error)
    }
  }

  async function set(key: string, value: any): Promise<void> {
    const serialized = JSON.stringify(value)
    await client.set(key, serialized)
    logger.debug(`Set key: ${key}`)
  }

  async function get(key: string): Promise<any> {
    const value = await client.get(key)
    logger.debug(`Get key: ${key}`)
    return value ? JSON.parse(value) : null
  }

  async function delete(key: string): Promise<void> {
    await client.del(key)
    logger.debug(`Deleted key: ${key}`)
  }

  return {
    [Lifecycle.ComponentStarted]: start,
    [Lifecycle.ComponentStopped]: stop,
    set,
    get,
    delete
  }
}
```

### Manejo de Errores: `errors.ts`

```tsx
export class RedisConnectionError extends Error {
  constructor(message: string) {
    super(message)
    this.name = 'RedisConnectionError'
  }
}
```

## Ejemplo de Adaptador de Base de Datos

Aquí hay un ejemplo de un adaptador de base de datos PostgreSQL.

```tsx
import { Pool, QueryResult } from 'pg'
import { IDatabase } from '../../types'

export function createPostgresAdapter(
  components: Pick<AppComponents, 'config' | 'logs'>
): IDatabase {
  const { config, logs } = components
  const logger = logs.getLogger('postgres-adapter')

  const pool = new Pool({
    connectionString: config.requireString('DATABASE_URL'),
    max: config.getNumber('DATABASE_POOL_SIZE', 20)
  })

  async function start(): Promise<void> {
    // Probar la conexión
    const client = await pool.connect()
    logger.info('Database connection pool initialized')
    client.release()
  }

  async function stop(): Promise<void> {
    await pool.end()
    logger.info('Database connection pool closed')
  }

  async function query<T = any>(
    sql: string,
    params?: any[]
  ): Promise<QueryResult<T>> {
    logger.debug('Executing query', { sql, params })
    return pool.query<T>(sql, params)
  }

  return {
    [Lifecycle.ComponentStarted]: start,
    [Lifecycle.ComponentStopped]: stop,
    query
  }
}
```

## Métodos de Ciclo de Vida

WKC proporciona métodos de ciclo de vida especiales que se llaman automáticamente:

* `[Lifecycle.ComponentStarted]` o `[START_COMPONENT]` - Llamado cuando el servicio inicia
* `[Lifecycle.ComponentStopped]` o `[STOP_COMPONENT]` - Llamado cuando el servicio se apaga

### Cuándo Usar Métodos de Ciclo de Vida

Usar métodos de ciclo de vida cuando su adaptador necesita:

* Establecer conexiones (base de datos, caché, cola de mensajes)
* Inicializar pools o clientes
* Realizar verificaciones de salud
* Limpiar recursos al apagarse
* Cerrar conexiones correctamente

## Mejores Prácticas

### 1. Manejo de Errores

Siempre manejar errores de conexión y lanzar errores personalizados significativos:

```tsx
try {
  await client.connect()
} catch (error) {
  logger.error('Connection failed', error)
  throw new ConnectionError('Failed to connect to external service')
}
```

### 2. Configuración

Usar el componente config para gestionar configuraciones del adaptador:

```tsx
const {
  host: config.requireString('DB_HOST'),
  port: config.getNumber('DB_PORT', 5432),
  timeout: config.getNumber('DB_TIMEOUT', 30000)
}
```

### 3. Logging

Registrar operaciones importantes para depuración y monitoreo:

```tsx
logger.info('Operation completed', { recordId, duration })
logger.warn('Slow query detected', { query, duration })
logger.error('Operation failed', { error, context })
```

### 4. Type Safety

Siempre tipar sus valores de retorno apropiadamente:

```tsx
async function getUser(id: string): Promise<User | null> {
  const result = await query<User>('SELECT * FROM users WHERE id = $1', [id])
  return result.rows[0] || null
}
```

### 5. Gestión de Recursos

Siempre limpiar recursos en el método de ciclo de vida stop:

```tsx
async function stop(): Promise<void> {
  await pool.end()
  await client.disconnect()
  logger.info('Resources cleaned up')
}
```

## Probar Adaptadores

Ver la documentación de [Probar Servicios (WKC)](../testing-standards/testing-services-wkc.md) para guía detallada sobre probar adaptadores.

### Ejemplo Rápido

```tsx
describe('when creating a memory storage adapter', () => {
  let storage: IStorage

  beforeEach(() => {
    storage = createMemoryStorageAdapter()
  })

  describe('when setting a value', () => {
    it('should store and retrieve the value', async () => {
      await storage.set('key', 'value')
      const result = await storage.get('key')
      expect(result).toBe('value')
    })
  })
})
```
