# Controladores

Los controladores son responsables de manejar solicitudes HTTP y WebSocket en su aplicación. Sirven como el punto de entrada para solicitudes externas y actúan como el puente entre la capa de transporte y su lógica de negocio.

## Propósito

Los controladores DEBEN realizar solo estas tareas específicas:

1. **Validar entrada de capa de transporte** - Verificar autenticación, autorización y formato de entrada
2. **Llamar componentes lógicos** - Delegar lógica de negocio a componentes lógicos apropiados
3. **Transformar entrada** - Convertir datos de solicitud en parámetros para llamadas de componentes lógicos
4. **Manejar errores** - Capturar y responder apropiadamente a errores de componentes lógicos

{% hint style="warning" %}
Los controladores NO deberían contener lógica de negocio. Todas las reglas de negocio DEBEN implementarse en componentes lógicos.
{% endhint %}

## Ubicación

Todos los controladores DEBEN colocarse bajo el directorio `/src/controllers` y nombrarse en referencia a la acción que realizan.

```
src/
└── controllers/
    ├── users/
    │   ├── get-user.ts
    │   ├── create-user.ts
    │   └── update-user.ts
    ├── content/
    │   ├── publish-content.ts
    │   └── delete-content.ts
    └── health.ts
```

## Estructura del Controlador

### Handler HTTP Básico

```tsx
import { HandlerContextWithPath } from '@dcl/platform-server-commons'

export async function getUserHandler(
  context: HandlerContextWithPath<'userLogic' | 'logs', '/users/:id'>
) {
  const { 
    components: { userLogic, logs },
    params: { id },
    verification 
  } = context

  const logger = logs.getLogger('get-user-handler')

  try {
    // 1. Validar autenticación
    if (!verification?.auth) {
      return {
        status: 401,
        body: {
          error: 'Unauthorized',
          message: 'Authentication required'
        }
      }
    }

    // 2. Validar entrada
    if (!id || typeof id !== 'string') {
      return {
        status: 400,
        body: {
          error: 'Bad Request',
          message: 'Invalid user ID'
        }
      }
    }

    // 3. Llamar componente lógico
    const user = await userLogic.getUser(id)

    // 4. Retornar respuesta exitosa
    return {
      status: 200,
      body: {
        ok: true,
        data: user
      }
    }
  } catch (error) {
    // 5. Manejar errores
    logger.error('Error fetching user', error)

    if (error instanceof UserNotFoundError) {
      return {
        status: 404,
        body: {
          error: 'Not Found',
          message: error.message
        }
      }
    }

    return {
      status: 500,
      body: {
        error: 'Internal Server Error',
        message: 'An unexpected error occurred'
      }
    }
  }
}
```

### Handler de Solicitud POST

```tsx
export async function createUserHandler(
  context: HandlerContextWithPath<'userLogic' | 'logs', '/users'>
) {
  const {
    components: { userLogic, logs },
    request,
    verification
  } = context

  const logger = logs.getLogger('create-user-handler')

  try {
    // 1. Validar autenticación
    if (!verification?.auth) {
      return {
        status: 401,
        body: {
          error: 'Unauthorized',
          message: 'Authentication required'
        }
      }
    }

    // 2. Parsear y validar cuerpo de solicitud
    const body = await request.json()
    
    if (!body.username || !body.email) {
      return {
        status: 400,
        body: {
          error: 'Bad Request',
          message: 'Username and email are required'
        }
      }
    }

    // 3. Transformar entrada para componente lógico
    const userData = {
      username: body.username.toLowerCase(),
      email: body.email.toLowerCase(),
      display_name: body.display_name || body.username
    }

    // 4. Llamar componente lógico
    const user = await userLogic.createUser(userData)

    // 5. Retornar respuesta exitosa
    return {
      status: 201,
      body: {
        ok: true,
        data: user
      }
    }
  } catch (error) {
    logger.error('Error creating user', error)

    if (error instanceof UserAlreadyExistsError) {
      return {
        status: 409,
        body: {
          error: 'Conflict',
          message: error.message
        }
      }
    }

    if (error instanceof InvalidEmailError) {
      return {
        status: 400,
        body: {
          error: 'Bad Request',
          message: error.message
        }
      }
    }

    return {
      status: 500,
      body: {
        error: 'Internal Server Error',
        message: 'An unexpected error occurred'
      }
    }
  }
}
```

### Handler de Parámetros de Query

```tsx
export async function searchUsersHandler(
  context: HandlerContextWithPath<'userLogic' | 'logs', '/users/search'>
) {
  const {
    components: { userLogic, logs },
    url,
    verification
  } = context

  const logger = logs.getLogger('search-users-handler')

  try {
    // 1. Validar autenticación
    if (!verification?.auth) {
      return {
        status: 401,
        body: {
          error: 'Unauthorized',
          message: 'Authentication required'
        }
      }
    }

    // 2. Extraer y validar parámetros de query (¡en minúsculas!)
    const searchParams = new URLSearchParams(url.search)
    const query = searchParams.get('query')?.toLowerCase()
    const limit = parseInt(searchParams.get('limit') || '10', 10)
    const offset = parseInt(searchParams.get('offset') || '0', 10)

    if (!query) {
      return {
        status: 400,
        body: {
          error: 'Bad Request',
          message: 'Query parameter is required'
        }
      }
    }

    // 3. Llamar componente lógico
    const results = await userLogic.searchUsers({
      query,
      limit,
      offset
    })

    // 4. Retornar respuesta exitosa
    return {
      status: 200,
      body: {
        ok: true,
        data: results
      }
    }
  } catch (error) {
    logger.error('Error searching users', error)

    return {
      status: 500,
      body: {
        error: 'Internal Server Error',
        message: 'An unexpected error occurred'
      }
    }
  }
}
```

## Requisitos de Entrada

Todas las claves de entrada recibidas a través de controladores (cuerpo JSON, parámetros de query, parámetros de URL, headers, etc.) **DEBEN estar en minúsculas** para prevenir problemas de capitalización al procesar datos.

### Parámetros Multi-palabra

Los parámetros multi-palabra DEBEN definirse usando **snake_case**.

{% hint style="danger" %}
**Nomenclatura incorrecta:**

* `minPrice`
* `Car`
* `somethingURL`
* `userId`
{% endhint %}

{% hint style="success" %}
**Nomenclatura correcta:**

* `min_price`
* `car`
* `something_url`
* `user_id`
{% endhint %}

### Ejemplo

```tsx
// Cuerpo de solicitud
{
  "user_name": "john_doe",        // ✅ Correcto
  "email_address": "john@example.com",  // ✅ Correcto
  "display_name": "John Doe",     // ✅ Correcto
  "max_items": 100                // ✅ Correcto
}

// Parámetros de query
?search_query=test&max_results=50&sort_by=created_at  // ✅ Correcto

// Parámetros de URL
/users/:user_id/posts/:post_id   // ✅ Correcto
```

## Manejo de Errores

### Respuestas de Error Estándar

Los controladores deberían retornar formatos de respuesta de error consistentes:

```tsx
// 400 Bad Request
{
  "error": "Bad Request",
  "message": "Mensaje de error detallado",
  "details": {} // Detalles adicionales opcionales
}

// 401 Unauthorized
{
  "error": "Unauthorized",
  "message": "Authentication required"
}

// 403 Forbidden
{
  "error": "Forbidden",
  "message": "You don't have permission to access this resource"
}

// 404 Not Found
{
  "error": "Not Found",
  "message": "Resource not found"
}

// 409 Conflict
{
  "error": "Conflict",
  "message": "Resource already exists"
}

// 500 Internal Server Error
{
  "error": "Internal Server Error",
  "message": "An unexpected error occurred"
}
```

### Mapeo de Errores

Mapear errores de dominio a códigos de estado HTTP apropiados:

```tsx
function mapErrorToResponse(error: Error, logger: ILogger) {
  logger.error('Handler error', error)

  // Errores específicos del dominio
  if (error instanceof UserNotFoundError) {
    return { status: 404, body: { error: 'Not Found', message: error.message } }
  }
  
  if (error instanceof UserAlreadyExistsError) {
    return { status: 409, body: { error: 'Conflict', message: error.message } }
  }
  
  if (error instanceof UnauthorizedError) {
    return { status: 403, body: { error: 'Forbidden', message: error.message } }
  }
  
  if (error instanceof InvalidInputError) {
    return { status: 400, body: { error: 'Bad Request', message: error.message } }
  }

  // Error genérico
  return {
    status: 500,
    body: {
      error: 'Internal Server Error',
      message: 'An unexpected error occurred'
    }
  }
}
```

## Autenticación y Autorización

### Verificación de Autenticación

```tsx
// Requerir autenticación
if (!verification?.auth) {
  return {
    status: 401,
    body: {
      error: 'Unauthorized',
      message: 'Authentication required'
    }
  }
}

const userAddress = verification.auth.toLowerCase()
```

### Verificación de Autorización

```tsx
// Verificar permisos a través del componente lógico
const canAccess = await permissionsLogic.canUserAccessResource(
  userAddress,
  resourceId
)

if (!canAccess) {
  return {
    status: 403,
    body: {
      error: 'Forbidden',
      message: 'You don\'t have permission to access this resource'
    }
  }
}
```

## Mejores Prácticas

### 1. Mantener Controladores Ligeros

Los controladores deberían ser envoltorios ligeros alrededor de componentes lógicos:

```tsx
// ✅ Bien: Controlador ligero
export async function handler(context) {
  const { userLogic } = context.components
  const user = await userLogic.getUser(context.params.id)
  return { status: 200, body: { data: user } }
}

// ❌ Mal: Lógica de negocio en controlador
export async function handler(context) {
  const { database } = context.components
  const user = await database.query('SELECT * FROM users...')
  const processed = processUser(user) // ¡Lógica de negocio!
  const validated = validateUser(processed) // ¡Lógica de negocio!
  return { status: 200, body: { data: validated } }
}
```

### 2. Validar Temprano

Validar y fallar rápido:

```tsx
// Validar autenticación primero
if (!verification?.auth) {
  return { status: 401, body: { error: 'Unauthorized' } }
}

// Luego validar entrada
if (!params.id) {
  return { status: 400, body: { error: 'Bad Request' } }
}

// Luego proceder con lógica de negocio
const result = await logic.doSomething(params.id)
```

### 3. Usar Type Safety

Aprovechar TypeScript para type safety:

```tsx
interface CreateUserRequest {
  user_name: string
  email_address: string
  display_name?: string
}

const body: CreateUserRequest = await request.json()
```

### 4. Registrar Apropiadamente

Registrar eventos importantes y errores:

```tsx
logger.info('User created', { userId: user.id, userAddress })
logger.warn('Rate limit approaching', { userAddress, requests })
logger.error('Failed to create user', { error, userAddress })
```

## Probar Controladores

Ver la documentación de [Probar Servicios (WKC)](../testing-standards/testing-services-wkc.md) para guía de pruebas de integración.
