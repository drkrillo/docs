# Testing & Performance

Esta página cubre estrategias de testing para aplicaciones Redux/RTK Query y técnicas de optimización de performance.

## Testing de Redux Slices

### Tests Básicos de Reducers

```tsx
// user.slice.test.ts
import reducer, { userLoggedIn, userLoggedOut } from './user.slice';

describe('user slice', () => {
  const initialState = {
    account: null,
    isAuthenticated: false,
  };

  it('should handle userLoggedIn', () => {
    const account = '0x123...';
    const actual = reducer(initialState, userLoggedIn({ account }));
    
    expect(actual.account).toBe(account);
    expect(actual.isAuthenticated).toBe(true);
  });

  it('should handle userLoggedOut', () => {
    const loggedInState = {
      account: '0x123...',
      isAuthenticated: true,
    };
    
    const actual = reducer(loggedInState, userLoggedOut());
    
    expect(actual.account).toBeNull();
    expect(actual.isAuthenticated).toBe(false);
  });
});
```

### Testing de Entity Adapters

```tsx
// credits.slice.test.ts
import reducer, { txAdded, txsCleared, creditsSelectors } from './credits.slice';

describe('credits slice with entity adapter', () => {
  it('should add a transaction', () => {
    const initialState = reducer(undefined, { type: 'unknown' });
    const tx = { id: '1', address: '0x123', amount: 100, type: 'grant', timestamp: Date.now() };
    
    const actual = reducer(initialState, txAdded(tx));
    
    expect(creditsSelectors.selectById({ credits: actual }, '1')).toEqual(tx);
    expect(creditsSelectors.selectTotal({ credits: actual })).toBe(1);
  });

  it('should clear all transactions', () => {
    const initialState = reducer(undefined, { type: 'unknown' });
    const withTx = reducer(initialState, txAdded({ id: '1', /* ... */ }));
    
    const actual = reducer(withTx, txsCleared());
    
    expect(creditsSelectors.selectTotal({ credits: actual })).toBe(0);
  });
});
```

## Testing de Selectors

### Selectors Simples

```tsx
// user.selectors.test.ts
import { selectAccount, selectIsAuthenticated } from './user.slice';

describe('user selectors', () => {
  const mockState = {
    user: {
      account: '0x123...',
      isAuthenticated: true,
    },
    // ... otros slices
  };

  it('should select account', () => {
    expect(selectAccount(mockState)).toBe('0x123...');
  });

  it('should select authentication status', () => {
    expect(selectIsAuthenticated(mockState)).toBe(true);
  });
});
```

### Selectors Memoizados

```tsx
// land.selectors.test.ts
import { selectFilteredParcels, selectActiveFiltersCount } from './land.selectors';

describe('land selectors', () => {
  const mockState = {
    land: {
      filters: { owner: '0x123', minPrice: 100 },
      parcels: [
        { id: '1', owner: '0x123', price: 150 },
        { id: '2', owner: '0x456', price: 200 },
      ],
    },
  };

  it('should count active filters', () => {
    expect(selectActiveFiltersCount(mockState)).toBe(2);
  });

  it('should filter parcels', () => {
    const result = selectFilteredParcels(mockState);
    expect(result).toHaveLength(1);
    expect(result[0].id).toBe('1');
  });

  it('should memoize results', () => {
    const result1 = selectFilteredParcels(mockState);
    const result2 = selectFilteredParcels(mockState);
    
    // Misma referencia = memoizado
    expect(result1).toBe(result2);
  });
});
```

## Testing de RTK Query con MSW

### Setup de MSW

```tsx
// src/test/server.ts
import { setupServer } from 'msw/node';
import { http, HttpResponse } from 'msw';

const handlers = [
  http.get('/api/v1/parcels/:id', ({ params }) => {
    return HttpResponse.json({
      id: params.id,
      x: 10,
      y: 20,
      owner: '0x123',
      name: 'Test Parcel',
    });
  }),

  http.post('/api/v1/credits/grant', async ({ request }) => {
    const body = await request.json();
    return HttpResponse.json({
      ok: true,
      newBalance: 1000,
    });
  }),
];

export const server = setupServer(...handlers);
```

```tsx
// src/test/setup.ts
import { server } from './server';

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());
```

### Testing de Queries

```tsx
// land.client.test.tsx
import { render, screen, waitFor } from '@testing-library/react';
import { Provider } from 'react-redux';
import { setupStore } from '@/app/store';
import { useGetParcelQuery } from './land.client';

function TestComponent({ id }: { id: string }) {
  const { data, isLoading, isError } = useGetParcelQuery({ id });

  if (isLoading) return <div>Loading...</div>;
  if (isError) return <div>Error</div>;
  if (!data) return null;

  return <div>{data.name}</div>;
}

describe('land client', () => {
  it('should fetch and display parcel data', async () => {
    const store = setupStore();

    render(
      <Provider store={store}>
        <TestComponent id="1" />
      </Provider>
    );

    expect(screen.getByText('Loading...')).toBeInTheDocument();

    await waitFor(() => {
      expect(screen.getByText('Test Parcel')).toBeInTheDocument();
    });
  });
});
```

### Testing de Mutations

```tsx
// credits.client.test.tsx
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import { Provider } from 'react-redux';
import { setupStore } from '@/app/store';
import { useGrantCreditsMutation } from './credits.client';

function TestComponent() {
  const [grant, { isLoading, isSuccess }] = useGrantCreditsMutation();

  return (
    <div>
      <button onClick={() => grant({ address: '0x123', amount: 100 })}>
        Grant
      </button>
      {isLoading && <div>Loading...</div>}
      {isSuccess && <div>Success!</div>}
    </div>
  );
}

describe('credits client mutations', () => {
  it('should grant credits successfully', async () => {
    const store = setupStore();

    render(
      <Provider store={store}>
        <TestComponent />
      </Provider>
    );

    fireEvent.click(screen.getByText('Grant'));

    await waitFor(() => {
      expect(screen.getByText('Success!')).toBeInTheDocument();
    });
  });
});
```

### Testing de Optimistic Updates

```tsx
// credits.client.test.ts
import { server } from '@/test/server';
import { http, HttpResponse } from 'msw';
import { setupStore } from '@/app/store';
import { creditsClient } from './credits.client';

describe('optimistic updates', () => {
  it('should update cache optimistically and rollback on error', async () => {
    const store = setupStore();
    const address = '0x123';

    // Prefetch balance inicial
    await store.dispatch(
      creditsClient.endpoints.getBalance.initiate({ address })
    );

    const initialBalance = creditsClient.endpoints.getBalance.select({ address })(
      store.getState()
    ).data?.amount;

    expect(initialBalance).toBe(100); // desde handler MSW

    // Mock de failure
    server.use(
      http.post('/api/v1/credits/grant', () => {
        return HttpResponse.json({ error: 'Failed' }, { status: 500 });
      })
    );

    // Dispara mutation
    const mutation = store.dispatch(
      creditsClient.endpoints.grantCredits.initiate({
        address,
        amount: 50,
      })
    );

    // Verifica actualización optimistic
    const optimisticBalance = creditsClient.endpoints.getBalance.select({
      address,
    })(store.getState()).data?.amount;

    expect(optimisticBalance).toBe(150); // 100 + 50

    // Espera que falle la mutation
    await expect(mutation).rejects.toThrow();

    // Verifica rollback
    const rolledBackBalance = creditsClient.endpoints.getBalance.select({
      address,
    })(store.getState()).data?.amount;

    expect(rolledBackBalance).toBe(100); // De vuelta al original
  });
});
```

## Optimización de Performance

### Usa `selectFromResult` para Prevenir Re-renders

```tsx
// ✅ Bien: Solo se suscribe a campos específicos
const { owner } = useGetParcelQuery(
  { id },
  {
    selectFromResult: ({ data }) => ({
      owner: data?.owner,
    }),
  }
);

// Componente solo re-renderiza cuando owner cambia
```

### Evita Seleccionar State Completo

```tsx
// ✅ Bien: Selecciona valores específicos
const viewMode = useAppSelector(selectViewMode);
const filters = useAppSelector(selectFilters);

// ❌ Mal: Selecciona el slice completo
const ui = useAppSelector((state) => state.ui);
```

### Memoiza Selectors Costosos

```tsx
import { createSelector } from '@reduxjs/toolkit';

// ✅ Bien: Selector memoizado
export const selectFilteredParcels = createSelector(
  [selectAllParcels, selectFilters],
  (parcels, filters) => {
    // Lógica costosa de filtrado
    return parcels.filter(/* ... */);
  }
);

// ❌ Mal: Computado en componente
function Component() {
  const parcels = useAppSelector(selectAllParcels);
  const filters = useAppSelector(selectFilters);
  
  // ¡Re-computa en cada render!
  const filtered = parcels.filter(/* ... */);
}
```

### Usa Entity Adapters para Datos Normalizados

```tsx
// ✅ Bien: Normalizado con entity adapter
const adapter = createEntityAdapter<Parcel>();

// Lookups eficientes por ID
const parcel = adapter.getSelectors().selectById(state, id);

// ❌ Mal: Lookup en array
const parcel = state.parcels.find((p) => p.id === id);
```

### Ajusta Configuración de Cache de RTK Query

```tsx
export const client = createApi({
  // ...
  keepUnusedDataFor: 60, // Mantiene datos por 60 segundos
  refetchOnMountOrArgChange: 30, // Refetch si los datos tienen más de 30s
  refetchOnFocus: true, // Refetch cuando la ventana recupera el foco
  refetchOnReconnect: true, // Refetch al reconectar
});
```

### Prefetch para Mejor UX

```tsx
function ParcelListItem({ parcel }: { parcel: Parcel }) {
  const dispatch = useAppDispatch();

  const handleMouseEnter = () => {
    // Prefetch al hacer hover
    dispatch(
      client.util.prefetch('getParcel', { id: parcel.id }, { force: false })
    );
  };

  return (
    <Link to={`/parcels/${parcel.id}`} onMouseEnter={handleMouseEnter}>
      {parcel.name}
    </Link>
  );
}
```

### Estrategia de Polling

```tsx
// Poll solo cuando sea necesario
const { data } = useGetBalanceQuery(
  { address },
  {
    pollingInterval: isActive ? 10000 : 0, // Poll solo cuando está activo
    skipPollingIfUnfocused: true, // Pausa cuando la pestaña no está enfocada
  }
);
```

## Redux DevTools

### Habilita en Desarrollo

```tsx
export const store = configureStore({
  // ...
  devTools: process.env.NODE_ENV !== 'production',
});
```

### Action Sanitizer

Sanitiza datos sensibles en DevTools:

```tsx
const actionSanitizer = (action: any) => {
  if (action.type === 'user/loggedIn') {
    return {
      ...action,
      payload: {
        ...action.payload,
        authToken: '***REDACTED***',
      },
    };
  }
  return action;
};

export const store = configureStore({
  // ...
  devTools: {
    actionSanitizer,
  },
});
```

## Checklist de Mejores Prácticas

### Performance

- [ ]  Usa `selectFromResult` para resultados de query grandes
- [ ]  Memoiza selectors costosos con `createSelector`
- [ ]  Usa entity adapters para colecciones normalizadas
- [ ]  Evita seleccionar slices completos en componentes
- [ ]  Ajusta `keepUnusedDataFor` según tu caso de uso
- [ ]  Prefetch de datos antes de navegación
- [ ]  Usa polling estratégicamente (solo cuando sea necesario)

### Testing

- [ ]  Test unitario de todos los reducers y acciones
- [ ]  Test de selectors memoizados para correctitud y performance
- [ ]  Usa MSW para tests de endpoints RTK Query
- [ ]  Test de optimistic updates y lógica de rollback
- [ ]  Test de manejo de errores en componentes
- [ ]  Escribe tests de integración para flujos críticos

### Calidad de Código

- [ ]  Usa hooks tipados (`useAppSelector`, `useAppDispatch`)
- [ ]  Maneja todos los estados de query (loading, error, success)
- [ ]  Usa `.unwrap()` para manejo de errores en mutations
- [ ]  Invalida o actualiza cache después de mutations
- [ ]  Mantén datos no serializables fuera de Redux
- [ ]  Documenta selectors y lógica complejos

## Anti-Patrones a Evitar

{% hint style="danger" %}
**No hagas esto:**

1. Almacenar objetos no serializables (providers, signers) en Redux
2. Duplicar datos en slices y RTK Query
3. Despachar acciones durante render
4. Crear selectors que retornan objetos nuevos sin memoización
5. Ignorar estados de loading y error
6. Fetch de los mismos datos en múltiples componentes sin RTK Query
7. Hacer over-poll o poll sin `skipPollingIfUnfocused`
{% endhint %}

## Monitoreando Performance

### Rastrea Llamadas a Selectors

```tsx
import { createSelector } from '@reduxjs/toolkit';

const selectExpensiveData = createSelector(
  [selectData],
  (data) => {
    console.log('Selector called'); // Solo debe loguear cuando data cambia
    return expensiveOperation(data);
  }
);
```

### Monitorea Re-renders

```tsx
import { useEffect, useRef } from 'react';

function useRenderCount() {
  const renderCount = useRef(0);
  
  useEffect(() => {
    renderCount.current += 1;
    console.log('Render count:', renderCount.current);
  });
}

function Component() {
  useRenderCount(); // Rastrea re-renders
  // ...
}
```

## Próximos Pasos

* Revisa [Component Patterns](component-patterns.md) para ejemplos de uso
* Ve [RTK Query](rtk-query.md) para estrategias de caching
* Entiende [State Management](state-management.md) para optimización de slices
