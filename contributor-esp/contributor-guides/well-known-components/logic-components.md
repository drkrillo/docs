# Componentes Lógicos

{% hint style="info" %}
Esta sección está actualmente siendo desarrollada. Vuelva pronto para documentación completa sobre componentes lógicos.
{% endhint %}

Los componentes lógicos son piezas de software que contienen la **lógica de negocio** de su aplicación. Están organizados semánticamente por dominio o funcionalidad y sirven como la capa de orquestación entre controladores y adaptadores.

## Propósito

Los componentes lógicos:

* Implementan reglas de negocio y lógica de dominio
* Orquestan operaciones a través de múltiples adaptadores
* Encapsulan flujos de trabajo complejos
* Permanecen independientes de preocupaciones de la capa de transporte (HTTP, WebSocket)
* Pueden ser probados unitariamente exhaustivamente sin dependencias de I/O

## Ubicación

Los componentes lógicos DEBERÍAN colocarse bajo el directorio `src/logic` o `src/components`, organizados por dominio:

```
src/
└── logic/
    ├── users/
    ├── content/
    ├── permissions/
    └── notifications/
```

## Características

### 1. Consumo de Adaptadores

Los componentes lógicos son los consumidores principales de adaptadores. Usan adaptadores para interactuar con recursos externos mientras se enfocan en reglas de negocio.

```tsx
export function createUserLogic(
  components: Pick<AppComponents, 'database' | 'cache' | 'logs'>
): IUserLogic {
  const { database, cache, logs } = components
  const logger = logs.getLogger('user-logic')

  async function getUserProfile(userId: string): Promise<UserProfile> {
    // Verificar caché primero
    const cached = await cache.get(`user:${userId}`)
    if (cached) {
      return cached
    }

    // Obtener de base de datos
    const user = await database.query('SELECT * FROM users WHERE id = $1', [userId])
    
    // Aplicar lógica de negocio
    const profile = transformUserToProfile(user)
    
    // Cachear el resultado
    await cache.set(`user:${userId}`, profile, { ttl: 3600 })
    
    return profile
  }

  return {
    getUserProfile
  }
}
```

### 2. Reglas de Negocio

Los componentes lógicos hacen cumplir reglas de negocio y validación:

```tsx
async function createUser(userData: CreateUserInput): Promise<User> {
  // Regla de negocio: el nombre de usuario debe ser único
  const existing = await database.query(
    'SELECT id FROM users WHERE username = $1',
    [userData.username]
  )
  
  if (existing.rows.length > 0) {
    throw new UserAlreadyExistsError(userData.username)
  }

  // Regla de negocio: validar datos de usuario
  if (!isValidEmail(userData.email)) {
    throw new InvalidEmailError(userData.email)
  }

  // Crear el usuario
  const user = await database.query(
    'INSERT INTO users (username, email) VALUES ($1, $2) RETURNING *',
    [userData.username, userData.email]
  )

  logger.info('User created', { userId: user.id })
  
  return user
}
```

### 3. Orquestación de Flujos de Trabajo

Los componentes lógicos coordinan operaciones complejas de múltiples pasos:

```tsx
async function publishContent(
  userId: string,
  content: ContentInput
): Promise<PublishedContent> {
  // Paso 1: Validar permisos
  const canPublish = await permissions.canUserPublish(userId)
  if (!canPublish) {
    throw new UnauthorizedError('User cannot publish content')
  }

  // Paso 2: Procesar contenido
  const processed = await processContent(content)

  // Paso 3: Almacenar contenido
  const stored = await storage.save(processed)

  // Paso 4: Actualizar índices
  await searchIndex.index(stored)

  // Paso 5: Notificar suscriptores
  await notifications.notifySubscribers(userId, stored)

  logger.info('Content published', { contentId: stored.id, userId })

  return stored
}
```

## Mejores Prácticas

### 1. Responsabilidad Única

Cada componente lógico debería enfocarse en un dominio o contexto delimitado:

```tsx
// Bien: Enfocado en dominio de usuario
createUserLogic()
createContentLogic()
createPermissionsLogic()

// Evitar: Responsabilidades mixtas
createUserAndContentLogic()
```

### 2. Inyección de Dependencias

Siempre inyectar dependencias a través del parámetro components:

```tsx
export function createOrderLogic(
  components: Pick<AppComponents, 'database' | 'payments' | 'inventory' | 'logs'>
): IOrderLogic {
  // Usar dependencias inyectadas
}
```

### 3. Manejo de Errores

Lanzar errores de dominio significativos que los controladores pueden capturar y manejar:

```tsx
// Definir errores específicos del dominio
export class InsufficientInventoryError extends Error {
  constructor(public readonly productId: string, public readonly requested: number) {
    super(`Insufficient inventory for product ${productId}. Requested: ${requested}`)
    this.name = 'InsufficientInventoryError'
  }
}
```

### 4. Lógica de Negocio Pura

Mantener los componentes lógicos libres de preocupaciones de la capa de transporte:

```tsx
// Bien: Lógica de negocio pura
async function calculateOrderTotal(items: OrderItem[]): Promise<number> {
  return items.reduce((total, item) => total + item.price * item.quantity, 0)
}

// Evitar: Preocupaciones de capa HTTP/transporte
async function calculateOrderTotal(req: Request, res: Response): Promise<void> {
  // No hacer esto en componentes lógicos
}
```

## Probar Componentes Lógicos

Los componentes lógicos deberían ser probados unitariamente exhaustivamente. Ver la documentación de [Probar Servicios (WKC)](../testing-standards/testing-services-wkc.md) para detalles.

```tsx
describe('when creating a user', () => {
  let userLogic: IUserLogic
  let mockDatabase: jest.Mocked<IDatabase>

  beforeEach(() => {
    mockDatabase = createMockDatabase()
    userLogic = createUserLogic({ database: mockDatabase, logs: mockLogs })
  })

  describe('and the username already exists', () => {
    beforeEach(() => {
      mockDatabase.query.mockResolvedValueOnce({ rows: [{ id: '123' }] })
    })

    it('should throw UserAlreadyExistsError', async () => {
      await expect(
        userLogic.createUser({ username: 'existing', email: 'test@test.com' })
      ).rejects.toThrow(UserAlreadyExistsError)
    })
  })
})
```

## Próximamente

Esta sección se expandirá con:

* Ejemplos detallados de componentes lógicos dirigidos por dominio
* Patrones para manejar flujos de trabajo complejos
* Guías para organizar lógica de dominio grande
* Integración con sistemas de eventos
* Estrategias de caché
* Patrones de gestión de transacciones
