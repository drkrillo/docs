# Configuración del Store

Esta página cubre cómo configurar su store Redux, configurar hooks tipados y organizar su estructura de proyecto.

## Estructura de Carpetas

Los proyectos DEBEN seguir esta estructura para consistencia y mantenibilidad:

```
src/
  app/
    store.ts              # Configuración del store
    hooks.ts              # Hooks tipados (useAppDispatch/useAppSelector)
  shared/
    types/                # DTOs (API), modelos de dominio, mappers
    utils/                # Utilidades compartidas
  services/
    client.ts             # Cliente base RTK Query
    baseQuery.ts          # Base query con auth/chainId/retry
  features/
    user/
      user.client.ts      # Endpoints RTK Query
      user.slice.ts       # Slice de estado UI
      user.selectors.ts   # Selectores memoizados
      __tests__/          # Pruebas
    land/
      land.client.ts
      land.slice.ts
      land.selectors.ts
    credits/
      credits.client.ts
      credits.slice.ts
      credits.selectors.ts
```

### Principios de Organización de Carpetas

* **Basado en características** - Agrupar por dominio de negocio, no por rol técnico
* **Co-localización** - Mantener código relacionado junto
* **Separación clara** - Distinguir entre datos remotos (`.client.ts`) y estado local (`.slice.ts`)

## Configuración del Store

El store DEBE configurarse con `configureStore` de RTK e incluir todo el middleware necesario.

### Configuración Básica del Store

```tsx
// src/app/store.ts
import { configureStore } from '@reduxjs/toolkit';
import { client } from '@/services/client';
import userReducer from '@/features/user/user.slice';
import landReducer from '@/features/land/land.slice';
import creditsReducer from '@/features/credits/credits.slice';

export const store = configureStore({
  reducer: {
    // Reducer del cliente RTK Query (DEBE ser incluido)
    [client.reducerPath]: client.reducer,
    
    // Slices de características
    user: userReducer,
    land: landReducer,
    credits: creditsReducer,
  },
  
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware({
      serializableCheck: {
        // Ignorar redux-persist u otras rutas no serializables conocidas
        ignoredActions: ['persist/PERSIST', 'persist/REHYDRATE'],
        ignoredPaths: ['register'],
      },
    }).concat(client.middleware), // DEBE incluir middleware RTK Query
    
  devTools: process.env.NODE_ENV !== 'production',
});

// Exportar tipos para usar a través de la app
export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
```

### Opciones de Configuración

#### Verificación de Serializable

El middleware `serializableCheck` valida que todo el estado sea serializable. Configurarlo para ignorar excepciones conocidas:

```tsx
serializableCheck: {
  // Acciones a ignorar
  ignoredActions: [
    'persist/PERSIST',
    'persist/REHYDRATE',
  ],
  // Rutas de estado a ignorar
  ignoredPaths: ['register', 'socket.connection'],
}
```

{% hint style="warning" %}
**Nunca deshabilitar `serializableCheck` completamente.** En su lugar, configurar excepciones y mantener datos no serializables fuera de Redux.
{% endhint %}

#### Dev Tools

Habilitar Redux DevTools en desarrollo para depuración:

```tsx
devTools: process.env.NODE_ENV !== 'production'
```

Para depuración de producción (si es necesario), usar configuración específica:

```tsx
devTools: process.env.NODE_ENV !== 'production' ? true : {
  name: 'Decentraland App',
  trace: false,
  traceLimit: 25,
}
```

## Hooks Tipados

Crear versiones tipadas de `useDispatch` y `useSelector` para mejor inferencia de tipos y experiencia de desarrollador.

### Configuración de Hooks

```tsx
// src/app/hooks.ts
import { TypedUseSelectorHook, useDispatch, useSelector } from 'react-redux';
import type { RootState, AppDispatch } from './store';

// Hook de dispatch tipado
export const useAppDispatch = () => useDispatch<AppDispatch>();

// Hook de selector tipado
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector;
```

### Uso en Componentes

```tsx
// ❌ Mal: Usando hooks estándar
import { useDispatch, useSelector } from 'react-redux';
import { RootState } from '@/app/store';

function Component() {
  const dispatch = useDispatch(); // Sin inferencia de tipos
  const user = useSelector((state: RootState) => state.user); // Tipado manual
}

// ✅ Bien: Usando hooks tipados
import { useAppDispatch, useAppSelector } from '@/app/hooks';

function Component() {
  const dispatch = useAppDispatch(); // Tipado automáticamente
  const user = useAppSelector((state) => state.user); // RootState inferido
}
```

### Beneficios de Hooks Tipados

1. **Autocompletado** - IntelliSense completo para forma del estado
2. **Type Safety** - Detectar errores en tiempo de compilación
3. **Refactorización** - Renombrar propiedades de estado con confianza
4. **Menos Código Repetitivo** - No necesidad de especificar `RootState` repetidamente

## Configuración del Provider

Envolver su app con el Provider de Redux:

### Next.js App Router

```tsx
// app/providers.tsx
'use client';

import { Provider } from 'react-redux';
import { store } from '@/app/store';

export function Providers({ children }: { children: React.ReactNode }) {
  return <Provider store={store}>{children}</Provider>;
}
```

```tsx
// app/layout.tsx
import { Providers } from './providers';

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        <Providers>{children}</Providers>
      </body>
    </html>
  );
}
```

### Next.js Pages Router

```tsx
// pages/_app.tsx
import { Provider } from 'react-redux';
import { store } from '@/app/store';
import type { AppProps } from 'next/app';

export default function App({ Component, pageProps }: AppProps) {
  return (
    <Provider store={store}>
      <Component {...pageProps} />
    </Provider>
  );
}
```

### React (Vite/CRA)

```tsx
// main.tsx / index.tsx
import { Provider } from 'react-redux';
import { store } from '@/app/store';

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <Provider store={store}>
      <App />
    </Provider>
  </React.StrictMode>
);
```

## Configuración de Entorno

Configurar ajustes específicos de entorno:

```tsx
// src/config/constants.ts
export const API_URL = process.env.NEXT_PUBLIC_API_URL || 'https://api.decentraland.org';
export const WS_URL = process.env.NEXT_PUBLIC_WS_URL || 'wss://api.decentraland.org';
export const CHAIN_ID = process.env.NEXT_PUBLIC_CHAIN_ID || '1';

// Configuración RTK Query
export const RTK_QUERY_CONFIG = {
  keepUnusedDataFor: 60, // segundos
  refetchOnFocus: true,
  refetchOnReconnect: true,
  refetchOnMountOrArgChange: 30, // segundos
} as const;
```

## Definiciones de Tipos

Crear definiciones de tipos compartidos para consistencia:

```tsx
// src/shared/types/api.types.ts
/** Envoltorio de respuesta API */
export interface ApiResponse<T> {
  ok: boolean;
  data: T;
  error?: string;
}

/** Respuesta paginada */
export interface PaginatedResponse<T> {
  items: T[];
  total: number;
  page: number;
  limit: number;
}

/** Error común de API */
export interface ApiError {
  message: string;
  code: string;
  details?: Record<string, any>;
}
```

```tsx
// src/shared/types/domain.types.ts
/** Modelo de dominio User */
export interface User {
  id: string;
  address: string;
  name?: string;
  avatar?: string;
  createdAt: string;
}

/** Modelo de dominio Parcel */
export interface Parcel {
  id: string;
  x: number;
  y: number;
  owner: string;
  name?: string;
}
```

## Múltiples Instancias de Store

Para pruebas o micro-frontends, puede necesitar múltiples instancias de store:

```tsx
// src/app/store.ts
import { configureStore } from '@reduxjs/toolkit';

export function createStore(preloadedState?: Partial<RootState>) {
  return configureStore({
    reducer: {
      // ... reducers
    },
    preloadedState,
    // ... otra configuración
  });
}

// Instancia de store por defecto
export const store = createStore();

export type AppStore = ReturnType<typeof createStore>;
export type RootState = ReturnType<AppStore['getState']>;
export type AppDispatch = AppStore['dispatch'];
```

## Mejores Prácticas

### 1. Store Único

Siempre usar una sola instancia de store por aplicación:

```tsx
// ✅ Bien: Un store
export const store = configureStore({ ... });

// ❌ Mal: Múltiples stores
export const userStore = configureStore({ ... });
export const cartStore = configureStore({ ... });
```

### 2. Carga Lazy de Reducers

Para code splitting, inyectar reducers dinámicamente:

```tsx
import { combineReducers } from '@reduxjs/toolkit';

const staticReducers = {
  user: userReducer,
};

export function createReducer(asyncReducers = {}) {
  return combineReducers({
    ...staticReducers,
    ...asyncReducers,
  });
}

// En el store
let currentReducers = createReducer();

export function injectReducer(key: string, reducer: Reducer) {
  currentReducers = createReducer({ [key]: reducer });
  store.replaceReducer(currentReducers);
}
```

### 3. Hot Module Replacement

Habilitar HMR para reducers en desarrollo:

```tsx
if (process.env.NODE_ENV === 'development' && module.hot) {
  module.hot.accept('./reducer', () => {
    const newRootReducer = require('./reducer').default;
    store.replaceReducer(newRootReducer);
  });
}
```

### 4. Persistencia de Estado

Al usar redux-persist, configurar cuidadosamente:

```tsx
import { persistStore, persistReducer } from 'redux-persist';
import storage from 'redux-persist/lib/storage';

const persistConfig = {
  key: 'root',
  storage,
  // Solo persistir slices específicos
  whitelist: ['user', 'preferences'],
  // Nunca persistir caché RTK Query
  blacklist: ['client'],
};

const persistedReducer = persistReducer(persistConfig, rootReducer);
```

## Próximos Pasos

* Aprender sobre [RTK Query](rtk-query.md) para obtención de datos
* Entender [Gestión de Estado](state-management.md) para estado local
* Revisar [Patrones de Componentes](component-patterns.md) para ejemplos de uso
