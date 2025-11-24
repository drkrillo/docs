# Gestión de Estado

Esta página cubre la creación de slices de Redux para gestión de estado UI y local usando `createSlice` y `createEntityAdapter` de Redux Toolkit.

## Cuándo Usar Slices vs RTK Query

Elegir la herramienta correcta para su estado:

| Tipo de Estado | Herramienta | Ejemplos |
|------------|------|----------|
| **Datos remotos** (propiedad del servidor) | RTK Query | Perfiles de usuario, NFTs, ítems de catálogo, órdenes |
| **Estado UI** (propiedad del cliente) | createSlice | Filtros, modales, preferencias de vista, estado de formulario |
| **Colecciones normalizadas** | createEntityAdapter | Listas ordenadas/filtradas, actualizaciones optimistas |

{% hint style="warning" %}
**No duplicar los mismos datos** tanto en un slice como en RTK Query. Elegir una fuente de verdad.
{% endhint %}

## Crear un Slice Básico

Usar `createSlice` para estado UI simple:

```tsx
// src/features/ui/ui.slice.ts
import { createSlice, PayloadAction } from '@reduxjs/toolkit';
import type { RootState } from '@/app/store';

interface UIState {
  sidebarOpen: boolean;
  modalOpen: boolean;
  viewMode: 'grid' | 'list';
  theme: 'light' | 'dark';
}

const initialState: UIState = {
  sidebarOpen: true,
  modalOpen: false,
  viewMode: 'grid',
  theme: 'light',
};

const uiSlice = createSlice({
  name: 'ui',
  initialState,
  reducers: {
    // Toggles booleanos
    sidebarToggled(state) {
      state.sidebarOpen = !state.sidebarOpen;
    },
    
    // Establecer valor específico
    modalOpened(state) {
      state.modalOpen = true;
    },
    
    modalClosed(state) {
      state.modalOpen = false;
    },
    
    // Acciones con payload
    viewModeChanged(state, action: PayloadAction<'grid' | 'list'>) {
      state.viewMode = action.payload;
    },
    
    themeChanged(state, action: PayloadAction<'light' | 'dark'>) {
      state.theme = action.payload;
    },
    
    // Múltiples propiedades
    uiReset() {
      return initialState;
    },
  },
});

// Exportar acciones
export const {
  sidebarToggled,
  modalOpened,
  modalClosed,
  viewModeChanged,
  themeChanged,
  uiReset,
} = uiSlice.actions;

// Exportar reducer
export default uiSlice.reducer;

// Exportar selectores
export const selectSidebarOpen = (state: RootState) => state.ui.sidebarOpen;
export const selectModalOpen = (state: RootState) => state.ui.modalOpen;
export const selectViewMode = (state: RootState) => state.ui.viewMode;
export const selectTheme = (state: RootState) => state.ui.theme;
```

## Usar Entity Adapters

Para colecciones normalizadas (listas con IDs), usar `createEntityAdapter`:

```tsx
// src/features/credits/credits.slice.ts
import { createSlice, createEntityAdapter, PayloadAction } from '@reduxjs/toolkit';
import type { RootState } from '@/app/store';

export type CreditTransaction = {
  id: string;
  address: string;
  amount: number;
  type: 'grant' | 'spend';
  timestamp: number;
  description?: string;
};

// Crear entity adapter
const txAdapter = createEntityAdapter<CreditTransaction>({
  selectId: (tx) => tx.id,
  sortComparer: (a, b) => b.timestamp - a.timestamp, // Más reciente primero
});

// Crear slice con estado inicial del adapter
const creditsSlice = createSlice({
  name: 'credits',
  initialState: txAdapter.getInitialState({
    sending: false,
    error: null as string | null,
  }),
  reducers: {
    // Agregar una sola transacción
    txAdded: txAdapter.addOne,
    
    // Agregar múltiples transacciones
    txsAdded: txAdapter.addMany,
    
    // Actualizar una transacción
    txUpdated: txAdapter.updateOne,
    
    // Remover una transacción
    txRemoved: txAdapter.removeOne,
    
    // Limpiar todas las transacciones
    txsCleared: txAdapter.removeAll,
    
    // Reducer personalizado con estado extra
    sendingStarted(state) {
      state.sending = true;
      state.error = null;
    },
    
    sendingSucceeded(state, action: PayloadAction<CreditTransaction>) {
      state.sending = false;
      txAdapter.addOne(state, action.payload);
    },
    
    sendingFailed(state, action: PayloadAction<string>) {
      state.sending = false;
      state.error = action.payload;
    },
  },
});

// Exportar acciones
export const {
  txAdded,
  txsAdded,
  txUpdated,
  txRemoved,
  txsCleared,
  sendingStarted,
  sendingSucceeded,
  sendingFailed,
} = creditsSlice.actions;

// Exportar reducer
export default creditsSlice.reducer;

// Crear selectores
const selectCreditsState = (state: RootState) => state.credits;

export const creditsSelectors = txAdapter.getSelectors(selectCreditsState);

// Selectores personalizados adicionales
export const selectIsSending = (state: RootState) => state.credits.sending;
export const selectError = (state: RootState) => state.credits.error;

// Selectores memoizados
export const selectTotalCredits = (state: RootState) => {
  const txs = creditsSelectors.selectAll(state);
  return txs.reduce((total, tx) => {
    return total + (tx.type === 'grant' ? tx.amount : -tx.amount);
  }, 0);
};
```

## Métodos de Entity Adapter

### Mutaciones de Estado

```tsx
// Agregar
txAdapter.addOne(state, entity)
txAdapter.addMany(state, entities)

// Actualizar
txAdapter.updateOne(state, { id, changes })
txAdapter.updateMany(state, updates)

// Upsert (agregar o actualizar)
txAdapter.upsertOne(state, entity)
txAdapter.upsertMany(state, entities)

// Remover
txAdapter.removeOne(state, id)
txAdapter.removeMany(state, ids)
txAdapter.removeAll(state)

// Set (reemplazar todo)
txAdapter.setAll(state, entities)
txAdapter.setOne(state, entity)
txAdapter.setMany(state, entities)
```

### Selectores Generados

```tsx
const selectors = txAdapter.getSelectors(selectState);

// Seleccionar todas las entidades como array
selectors.selectAll(state)

// Seleccionar entidades como { [id]: entity }
selectors.selectEntities(state)

// Seleccionar todos los IDs como array
selectors.selectIds(state)

// Seleccionar conteo total
selectors.selectTotal(state)

// Seleccionar entidad única por ID
selectors.selectById(state, id)
```

## Ejemplo de Estado Complejo

Combinando múltiples preocupaciones en un slice:

```tsx
// src/features/land/land.slice.ts
import { createSlice, createEntityAdapter, PayloadAction } from '@reduxjs/toolkit';
import type { RootState } from '@/app/store';

export type LandFilter = {
  owner?: string;
  minPrice?: number;
  maxPrice?: number;
  types?: ('parcel' | 'estate')[];
};

export type SelectedParcel = {
  x: number;
  y: number;
  id?: string;
};

const selectedParcelsAdapter = createEntityAdapter<SelectedParcel>({
  selectId: (p) => `${p.x},${p.y}`,
});

interface LandState {
  // Estado de vista
  mapCenter: { x: number; y: number };
  mapZoom: number;
  
  // Estado de filtro
  filters: LandFilter;
  
  // Estado de selección (usando adapter)
  selectedParcels: ReturnType<typeof selectedParcelsAdapter.getInitialState>;
  
  // Estado UI
  showGrid: boolean;
  highlightOwned: boolean;
}

const initialState: LandState = {
  mapCenter: { x: 0, y: 0 },
  mapZoom: 1,
  filters: {},
  selectedParcels: selectedParcelsAdapter.getInitialState(),
  showGrid: true,
  highlightOwned: false,
};

const landSlice = createSlice({
  name: 'land',
  initialState,
  reducers: {
    // Controles del mapa
    mapCenterChanged(state, action: PayloadAction<{ x: number; y: number }>) {
      state.mapCenter = action.payload;
    },
    
    mapZoomed(state, action: PayloadAction<number>) {
      state.mapZoom = action.payload;
    },
    
    // Filtros
    filtersUpdated(state, action: PayloadAction<Partial<LandFilter>>) {
      state.filters = { ...state.filters, ...action.payload };
    },
    
    filtersCleared(state) {
      state.filters = {};
    },
    
    // Selección
    parcelSelected(state, action: PayloadAction<SelectedParcel>) {
      selectedParcelsAdapter.addOne(state.selectedParcels, action.payload);
    },
    
    parcelDeselected(state, action: PayloadAction<string>) {
      selectedParcelsAdapter.removeOne(state.selectedParcels, action.payload);
    },
    
    selectionCleared(state) {
      selectedParcelsAdapter.removeAll(state.selectedParcels);
    },
    
    // Toggles UI
    gridToggled(state) {
      state.showGrid = !state.showGrid;
    },
    
    ownedHighlightToggled(state) {
      state.highlightOwned = !state.highlightOwned;
    },
  },
});

export const {
  mapCenterChanged,
  mapZoomed,
  filtersUpdated,
  filtersCleared,
  parcelSelected,
  parcelDeselected,
  selectionCleared,
  gridToggled,
  ownedHighlightToggled,
} = landSlice.actions;

export default landSlice.reducer;

// Selectores
export const selectMapCenter = (state: RootState) => state.land.mapCenter;
export const selectMapZoom = (state: RootState) => state.land.mapZoom;
export const selectFilters = (state: RootState) => state.land.filters;
export const selectShowGrid = (state: RootState) => state.land.showGrid;
export const selectHighlightOwned = (state: RootState) => state.land.highlightOwned;

// Selectores de selección
const selectSelectedParcelsState = (state: RootState) => state.land.selectedParcels;
export const selectedParcelsSelectors = selectedParcelsAdapter.getSelectors(
  selectSelectedParcelsState
);
```

## Selectores Memoizados

Usar `createSelector` de Reselect para estado computado/derivado:

```tsx
// src/features/land/land.selectors.ts
import { createSelector } from '@reduxjs/toolkit';
import type { RootState } from '@/app/store';
import { selectFilters } from './land.slice';

// Lógica de filtrado costosa - memoizada
export const selectActiveFiltersCount = createSelector(
  [selectFilters],
  (filters) => {
    return Object.values(filters).filter(Boolean).length;
  }
);

// Combinar múltiples selectores
export const selectHasActiveFilters = createSelector(
  [selectActiveFiltersCount],
  (count) => count > 0
);

// Múltiples entradas
export const selectFilteredParcels = createSelector(
  [
    (state: RootState) => state.land.allParcels, // asumiendo que esto existe
    selectFilters,
  ],
  (parcels, filters) => {
    return parcels.filter((parcel) => {
      if (filters.owner && parcel.owner !== filters.owner) return false;
      if (filters.minPrice && parcel.price < filters.minPrice) return false;
      if (filters.maxPrice && parcel.price > filters.maxPrice) return false;
      if (filters.types && !filters.types.includes(parcel.type)) return false;
      return true;
    });
  }
);
```

## Lógica Asíncrona con Extra Reducers

Manejar respuestas de RTK Query o async thunk en su slice:

```tsx
import { createSlice } from '@reduxjs/toolkit';
import { creditsClient } from './credits.client';

const slice = createSlice({
  name: 'credits',
  initialState: { lastGranted: null as number | null },
  reducers: {},
  extraReducers: (builder) => {
    builder
      .addMatcher(
        creditsClient.endpoints.grantCredits.matchFulfilled,
        (state, action) => {
          state.lastGranted = action.payload.newBalance;
        }
      )
      .addMatcher(
        creditsClient.endpoints.grantCredits.matchRejected,
        (state) => {
          state.lastGranted = null;
        }
      );
  },
});
```

## Mejores Prácticas

### 1. Mantener el Estado Mínimo

```tsx
// ✅ Bien: Solo almacenar lo que necesita
interface State {
  userId: string | null;
  isAuthenticated: boolean;
}

// ❌ Mal: Almacenar valores derivados/computados
interface State {
  userId: string | null;
  isAuthenticated: boolean;
  hasUserId: boolean; // Puede ser computado
  userIdLength: number; // Puede ser computado
}
```

### 2. Usar Mutaciones Amigables con Immer

```tsx
// ✅ Bien: Mutación directa (Immer lo maneja)
reducers: {
  itemAdded(state, action) {
    state.items.push(action.payload);
    state.count += 1;
  }
}

// ❌ Mal: Spread manual (innecesario)
reducers: {
  itemAdded(state, action) {
    return {
      ...state,
      items: [...state.items, action.payload],
      count: state.count + 1,
    };
  }
}
```

### 3. Organizar Reducers Lógicamente

```tsx
// ✅ Bien: Agrupado por funcionalidad
reducers: {
  // Controles de modal
  modalOpened(state) { ... },
  modalClosed(state) { ... },
  
  // Controles de filtro
  filterApplied(state, action) { ... },
  filterCleared(state) { ... },
  
  // Reset
  stateReset() { return initialState; },
}
```

### 4. Tipar Acciones Apropiadamente

```tsx
// ✅ Bien: Tipo de payload explícito
userUpdated(state, action: PayloadAction<{ id: string; name: string }>) {
  state.user = action.payload;
}

// ❌ Mal: Payload sin tipar
userUpdated(state, action) {
  state.user = action.payload; // Sin type safety
}
```

## Probar Slices

```tsx
// land.slice.test.ts
import reducer, { mapCenterChanged, mapZoomed } from './land.slice';

describe('land slice', () => {
  const initialState = {
    mapCenter: { x: 0, y: 0 },
    mapZoom: 1,
    // ... otro estado
  };

  it('should handle mapCenterChanged', () => {
    const newCenter = { x: 10, y: 20 };
    const actual = reducer(initialState, mapCenterChanged(newCenter));
    expect(actual.mapCenter).toEqual(newCenter);
  });

  it('should handle mapZoomed', () => {
    const actual = reducer(initialState, mapZoomed(2));
    expect(actual.mapZoom).toBe(2);
  });
});
```

## Próximos Pasos

* Revisar [Patrones de Componentes](component-patterns.md) para usar slices en componentes
* Aprender sobre [Integración Web3](web3-integration.md) para estado blockchain
* Ver [Pruebas y Rendimiento](testing-and-performance.md) para consejos de optimización
