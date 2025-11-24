# RTK Query

RTK Query es la poderosa capa de obtención y caché de datos de Redux Toolkit. Elimina la necesidad de escribir creadores de acciones asíncronas y gestiona estados de carga, caché y sincronización de datos automáticamente.

## Configuración del Cliente Base

Todos los endpoints de RTK Query se extienden desde una sola instancia de cliente base.

### Configuración de Base Query

```tsx
// src/services/baseQuery.ts
import { fetchBaseQuery } from '@reduxjs/toolkit/query/react';
import type { RootState } from '@/app/store';

export const baseQuery = fetchBaseQuery({
  baseUrl: process.env.NEXT_PUBLIC_API_URL, // ej., https://api.decentraland.org
  
  prepareHeaders: (headers, { getState }) => {
    const state = getState() as RootState;
    
    // Agregar token de autenticación
    const token = state.user.session?.authToken;
    if (token) {
      headers.set('authorization', `Bearer ${token}`);
    }
    
    // Agregar chain ID para contexto web3
    const chainId = state.user.chainId;
    if (chainId) {
      headers.set('x-chain-id', String(chainId));
    }
    
    // Headers estándar
    headers.set('accept', 'application/json');
    headers.set('content-type', 'application/json');
    
    return headers;
  },
  
  credentials: 'omit', // o 'include' si el backend requiere cookies
});
```

### Instancia del Cliente

```tsx
// src/services/client.ts
import { createApi } from '@reduxjs/toolkit/query/react';
import { baseQuery } from './baseQuery';

export const client = createApi({
  reducerPath: 'client',
  baseQuery,
  
  // Definir todos los tipos de tag posibles para invalidación de caché
  tagTypes: [
    'User',
    'Profile',
    'Parcels',
    'Estates',
    'Credits',
    'Orders',
    'Sales',
    'NFTs',
  ],
  
  // Configuración de caché
  keepUnusedDataFor: 60,         // Mantener datos no usados por 60 segundos
  refetchOnFocus: true,           // Re-obtener cuando la ventana recupera el foco
  refetchOnReconnect: true,       // Re-obtener al reconectar
  refetchOnMountOrArgChange: 30,  // Re-obtener si los datos tienen más de 30s
  
  // Los endpoints se inyectarán en archivos de características
  endpoints: () => ({}),
});
```

## Convenciones de Tags

Los tags se usan para invalidación de caché y sincronización. Seguir estas convenciones:

### Nomenclatura de Tags

| Tipo de Recurso | Query Tags | Mutation Invalida |
|--------------|------------|---------------------|
| **Colecciones** | `'Parcels'` (plural) | `'Parcels'` |
| **Entidad Única** | `{type: 'Parcels', id: '123'}` | `{type: 'Parcels', id: '123'}` |
| **Lista + Detalle** | `['Parcels', {type: 'Parcels', id}]` | `['Parcels']` o id específico |

### Ejemplos de Tags

```tsx
// Colección: proporciona tag de lista
providesTags: ['Parcels']

// Entidad única: proporciona tag específico + tag de lista
providesTags: (result) => 
  result 
    ? [{ type: 'Parcels', id: result.id }, 'Parcels']
    : ['Parcels']

// Mutation: invalida tanto la lista como la entidad específica
invalidatesTags: (result, error, arg) => [
  { type: 'Parcels', id: arg.id },
  'Parcels'
]
```

## Crear Endpoints

Los endpoints DEBERÍAN co-localizarse con su característica en archivos `feature.client.ts`.

### Endpoint Query (Lectura)

```tsx
// src/features/land/land.client.ts
import { client } from '@/services/client';

export type Tile = {
  x: number;
  y: number;
  type: 'parcel' | 'road' | 'plaza';
  owner?: string;
};

export type Parcel = {
  id: string;
  x: number;
  y: number;
  owner: string;
  name?: string;
  description?: string;
};

export const landClient = client.injectEndpoints({
  endpoints: (build) => ({
    // Obtener todos los tiles
    getTiles: build.query<Record<string, Tile>, void>({
      query: () => '/v1/tiles',
      providesTags: ['Parcels'],
    }),
    
    // Obtener parcel por coordenadas
    getParcelByCoords: build.query<Parcel, { x: number; y: number }>({
      query: ({ x, y }) => `/v1/lands/${x}/${y}`,
      providesTags: (result, error, arg) =>
        result
          ? [{ type: 'Parcels', id: result.id }, 'Parcels']
          : ['Parcels'],
    }),
    
    // Obtener parcels por propietario
    getParcelsByOwner: build.query<Parcel[], { owner: string }>({
      query: ({ owner }) => `/v1/lands/owner/${owner}`,
      providesTags: (result) =>
        result
          ? [
              ...result.map(({ id }) => ({ type: 'Parcels' as const, id })),
              'Parcels',
            ]
          : ['Parcels'],
    }),
  }),
  overrideExisting: false,
});

// Exportar hooks
export const {
  useGetTilesQuery,
  useGetParcelByCoordsQuery,
  useGetParcelsByOwnerQuery,
} = landClient;
```

### Endpoint Mutation (Escritura)

```tsx
// src/features/land/land.client.ts (continuación)
export const landClient = client.injectEndpoints({
  endpoints: (build) => ({
    // ... endpoints query ...
    
    // Actualizar nombre de parcel
    updateParcelName: build.mutation<
      Parcel,
      { id: string; name: string }
    >({
      query: ({ id, name }) => ({
        url: `/v1/lands/${id}`,
        method: 'PATCH',
        body: { name },
      }),
      invalidatesTags: (result, error, arg) => [
        { type: 'Parcels', id: arg.id },
        'Parcels',
      ],
    }),
    
    // Transferir parcel
    transferParcel: build.mutation<
      { ok: boolean },
      { id: string; to: string }
    >({
      query: ({ id, to }) => ({
        url: `/v1/lands/${id}/transfer`,
        method: 'POST',
        body: { to },
      }),
      invalidatesTags: (result, error, arg) => [
        { type: 'Parcels', id: arg.id },
        'Parcels', // Invalidar lista para actualizar filtros de propietario
      ],
    }),
  }),
});

export const {
  useUpdateParcelNameMutation,
  useTransferParcelMutation,
} = landClient;
```

## Actualizaciones Optimistas

Usar `onQueryStarted` para actualizaciones optimistas de UI con rollback automático en caso de falla.

```tsx
// src/features/credits/credits.client.ts
import { client } from '@/services/client';

export type CreditsBalance = {
  address: string;
  amount: number;
  lastUpdated: string;
};

export const creditsClient = client.injectEndpoints({
  endpoints: (build) => ({
    getBalance: build.query<CreditsBalance, { address: string }>({
      query: ({ address }) => `/v1/credits/${address}`,
      providesTags: (result, error, arg) => [
        { type: 'Credits', id: arg.address }
      ],
    }),
    
    grantCredits: build.mutation<
      { ok: true; newBalance: number },
      { address: string; amount: number }
    >({
      query: (body) => ({
        url: `/v1/credits/grant`,
        method: 'POST',
        body,
      }),
      
      // Actualización optimista
      async onQueryStarted({ address, amount }, { dispatch, queryFulfilled }) {
        // Actualizar optimistamente el caché
        const patchResult = dispatch(
          client.util.updateQueryData('getBalance', { address }, (draft) => {
            draft.amount += amount;
            draft.lastUpdated = new Date().toISOString();
          })
        );
        
        try {
          // Esperar a que la mutación se complete
          const { data } = await queryFulfilled;
          
          // Actualizar con respuesta del servidor
          dispatch(
            client.util.updateQueryData('getBalance', { address }, (draft) => {
              draft.amount = data.newBalance;
            })
          );
        } catch {
          // Rollback en caso de falla
          patchResult.undo();
        }
      },
      
      // También invalidar para asegurar consistencia
      invalidatesTags: (result, error, arg) => [
        { type: 'Credits', id: arg.address }
      ],
    }),
  }),
});

export const { useGetBalanceQuery, useGrantCreditsMutation } = creditsClient;
```

## Opciones Avanzadas de Query

### Polling

```tsx
// Hacer polling cada 10 segundos
const { data } = useGetBalanceQuery(
  { address },
  { pollingInterval: 10000 }
);
```

### Omitir Query

```tsx
// Omitir query si la dirección no está disponible
const { data } = useGetBalanceQuery(
  { address: address! },
  { skip: !address }
);
```

### Lazy Query

```tsx
const [trigger, result] = useLazyGetParcelByCoordsQuery();

// Activar manualmente
const handleClick = () => {
  trigger({ x: 10, y: 20 });
};
```

### Transformar Respuesta

```tsx
getParcel: build.query<Parcel, string>({
  query: (id) => `/v1/lands/${id}`,
  transformResponse: (response: ApiResponse<Parcel>) => response.data,
})
```

### Serialización Personalizada

Para paginación o búsqueda, personalizar serialización de clave de caché:

```tsx
searchParcels: build.query<Parcel[], { q: string; owner?: string; page?: number }>({
  query: (args) => ({
    url: '/v1/parcels/search',
    params: args,
  }),
  
  // Clave de caché personalizada para manejar parámetros opcionales
  serializeQueryArgs: ({ endpointName, queryArgs }) => {
    const { q, owner = 'any', page = 1 } = queryArgs;
    return `${endpointName}-${q}-${owner}-${page}`;
  },
  
  // Fusionar resultados para paginación
  merge(currentCache, newItems, { arg }) {
    if (arg.page === 1) {
      return newItems;
    }
    return [...currentCache, ...newItems];
  },
  
  // Forzar re-obtención cuando los args cambian
  forceRefetch({ currentArg, previousArg }) {
    return JSON.stringify(currentArg) !== JSON.stringify(previousArg);
  },
  
  providesTags: ['Parcels'],
})
```

## Manejo de Errores

### Manejo Personalizado de Errores

```tsx
import { FetchBaseQueryError } from '@reduxjs/toolkit/query';

export function isFetchBaseQueryError(
  error: unknown
): error is FetchBaseQueryError {
  return typeof error === 'object' && error != null && 'status' in error;
}

export function isErrorWithMessage(
  error: unknown
): error is { message: string } {
  return (
    typeof error === 'object' &&
    error != null &&
    'message' in error &&
    typeof (error as any).message === 'string'
  );
}
```

Uso en componentes:

```tsx
const { data, error } = useGetParcelQuery({ id });

if (error) {
  if (isFetchBaseQueryError(error)) {
    const errMsg = 'error' in error ? error.error : JSON.stringify(error.data);
    return <div>Error: {errMsg}</div>;
  } else if (isErrorWithMessage(error)) {
    return <div>Error: {error.message}</div>;
  }
}
```

## Gestión de Caché

### Actualizaciones Manuales de Caché

```tsx
// Actualizar caché directamente
dispatch(
  client.util.updateQueryData('getBalance', { address }, (draft) => {
    draft.amount = 1000;
  })
);
```

### Invalidar Caché

```tsx
// Invalidar todas las queries de Credits
dispatch(client.util.invalidateTags(['Credits']));

// Invalidar entidad específica
dispatch(client.util.invalidateTags([{ type: 'Credits', id: address }]));
```

### Resetear Estado del Cliente

```tsx
// Resetear estado completo del cliente
dispatch(client.util.resetApiState());
```

### Prefetch de Datos

```tsx
// Prefetch de datos antes de navegación
dispatch(
  client.util.prefetch('getParcel', { id: '123' }, { force: false })
);
```

## Mejores Prácticas

### 1. Usar Nombres Descriptivos de Endpoints

```tsx
// ✅ Bien
getParcelByCoords
getParcelsByOwner
updateParcelName

// ❌ Mal
getParcel
fetch
update
```

### 2. Proporcionar Tags Completos

```tsx
// ✅ Bien: Proporciona tanto tags de lista como de entidad
providesTags: (result) =>
  result
    ? [{ type: 'Parcels', id: result.id }, 'Parcels']
    : ['Parcels']

// ❌ Mal: Solo proporciona tag de lista
providesTags: ['Parcels']
```

### 3. Manejar Estados de Carga y Error

```tsx
// ✅ Bien: Manejo completo de estados
const { data, isLoading, isFetching, isError, error } = useGetParcelQuery({ id });

if (isLoading) return <Spinner />;
if (isError) return <Error error={error} />;
if (!data) return null;

// ❌ Mal: Manejo incompleto de estados
const { data } = useGetParcelQuery({ id });
return <div>{data.name}</div>; // Puede fallar si data es undefined
```

### 4. Usar Type Guards

```tsx
// ✅ Bien: Manejo de errores type-safe
if (isFetchBaseQueryError(error)) {
  // Manejar error de fetch
} else if (isErrorWithMessage(error)) {
  // Manejar error con mensaje
}

// ❌ Mal: Casting de tipos inseguro
const message = (error as any).message;
```

## Próximos Pasos

* Aprender sobre [Gestión de Estado](state-management.md) para estado UI local
* Revisar [Patrones de Componentes](component-patterns.md) para ejemplos de uso
* Entender [Integración Web3](web3-integration.md) para datos blockchain
