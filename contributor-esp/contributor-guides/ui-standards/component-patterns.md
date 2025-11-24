# Patrones de Componentes

Esta página cubre cómo usar efectivamente Redux y RTK Query en componentes React, incluyendo hooks, patrones de optimización y mejores prácticas.

## Uso Básico de Query

Usa los hooks generados desde los endpoints de RTK Query:

```tsx
import { useGetParcelByCoordsQuery } from '@/features/land/land.client';

function ParcelCard({ x, y }: { x: number; y: number }) {
  const { data, isLoading, isError, error } = useGetParcelByCoordsQuery({ x, y });

  if (isLoading) return <div>Loading...</div>;
  if (isError) return <div>Error: {error.toString()}</div>;
  if (!data) return null;

  return (
    <div>
      <h2>{data.name || `Parcel ${x},${y}`}</h2>
      <p>Owner: {data.owner}</p>
    </div>
  );
}
```

## Optimizando Re-renders con `selectFromResult`

Limita el resultado solo a los campos que necesitas:

```tsx
function ParcelOwner({ x, y }: { x: number; y: number }) {
  // ✅ Bien: Solo se suscribe a cambios en el campo owner
  const { owner, isFetching } = useGetParcelByCoordsQuery(
    { x, y },
    {
      selectFromResult: ({ data, isFetching }) => ({
        owner: data?.owner,
        isFetching,
      }),
    }
  );

  if (isFetching) return <span>Loading...</span>;
  return <span>Owner: {owner}</span>;
}

// ❌ Mal: Re-renderiza con cualquier cambio en data
function ParcelOwnerBad({ x, y }: { x: number; y: number }) {
  const { data } = useGetParcelByCoordsQuery({ x, y });
  return <span>Owner: {data?.owner}</span>;
}
```

## Queries Condicionales con `skip`

Omite queries cuando los argumentos no están listos:

```tsx
function UserProfile() {
  const { data: session } = useGetSessionQuery();
  const userId = session?.userId;

  // Omite el query de perfil hasta que tengamos userId
  const { data: profile } = useGetProfileQuery(
    { id: userId! },
    {
      skip: !userId, // No hace fetch si userId es undefined
    }
  );

  if (!userId) return <div>Please log in</div>;
  if (!profile) return <div>Loading profile...</div>;

  return <div>{profile.name}</div>;
}
```

## Polling para Actualizaciones en Tiempo Real

```tsx
function LiveBalance({ address }: { address: string }) {
  const { data } = useGetBalanceQuery(
    { address },
    {
      pollingInterval: 10000, // Poll cada 10 segundos
      skipPollingIfUnfocused: true, // Pausa cuando la pestaña no está enfocada
    }
  );

  return <div>Balance: {data?.amount ?? 0}</div>;
}
```

## Lazy Queries

Dispara queries manualmente en lugar de en el montaje del componente:

```tsx
function SearchParcels() {
  const [trigger, result] = useLazyGetParcelsByOwnerQuery();
  const [owner, setOwner] = useState('');

  const handleSearch = () => {
    if (owner) {
      trigger({ owner });
    }
  };

  return (
    <div>
      <input
        value={owner}
        onChange={(e) => setOwner(e.target.value)}
        placeholder="Enter owner address"
      />
      <button onClick={handleSearch}>Search</button>
      
      {result.isLoading && <div>Searching...</div>}
      {result.data && (
        <ul>
          {result.data.map((parcel) => (
            <li key={parcel.id}>{parcel.name}</li>
          ))}
        </ul>
      )}
    </div>
  );
}
```

## Uso de Mutation

### Mutation Básica

```tsx
function GrantCreditsButton({ address }: { address: string }) {
  const [grantCredits, { isLoading, isError, error }] = useGrantCreditsMutation();

  const handleGrant = async () => {
    try {
      await grantCredits({ address, amount: 100 }).unwrap();
      toast.success('Credits granted successfully!');
    } catch (err) {
      toast.error('Failed to grant credits');
      console.error('Grant failed:', err);
    }
  };

  return (
    <button onClick={handleGrant} disabled={isLoading}>
      {isLoading ? 'Granting...' : 'Grant 100 Credits'}
    </button>
  );
}
```

### Mutation con Retroalimentación

```tsx
import { isFetchBaseQueryError } from '@/services/client';

function UpdateParcelName({ parcelId }: { parcelId: string }) {
  const [name, setName] = useState('');
  const [updateName, { isLoading, isSuccess, isError, error }] =
    useUpdateParcelNameMutation();

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    
    try {
      const result = await updateName({ id: parcelId, name }).unwrap();
      toast.success(`Parcel renamed to "${result.name}"`);
      setName(''); // Limpia el formulario
    } catch (err) {
      if (isFetchBaseQueryError(err)) {
        toast.error(`Error: ${JSON.stringify(err.data)}`);
      } else {
        toast.error('An unexpected error occurred');
      }
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="New name"
        disabled={isLoading}
      />
      <button type="submit" disabled={isLoading || !name}>
        {isLoading ? 'Updating...' : 'Update Name'}
      </button>
      
      {isSuccess && <p className="success">Name updated!</p>}
      {isError && <p className="error">Update failed</p>}
    </form>
  );
}
```

## Usando State de Slice

Accede al state del slice con `useAppSelector`:

```tsx
import { useAppSelector, useAppDispatch } from '@/app/hooks';
import { selectViewMode, viewModeChanged } from '@/features/ui/ui.slice';

function ViewModeToggle() {
  const dispatch = useAppDispatch();
  const viewMode = useAppSelector(selectViewMode);

  const toggleMode = () => {
    dispatch(viewModeChanged(viewMode === 'grid' ? 'list' : 'grid'));
  };

  return (
    <button onClick={toggleMode}>
      View: {viewMode === 'grid' ? '⊞ Grid' : '☰ List'}
    </button>
  );
}
```

## Combinando Múltiples Queries

```tsx
function ParcelDetails({ id }: { id: string }) {
  const { data: parcel, isLoading: parcelLoading } = useGetParcelQuery({ id });
  const { data: owner, isLoading: ownerLoading } = useGetProfileQuery(
    { id: parcel?.owner! },
    { skip: !parcel?.owner }
  );

  const isLoading = parcelLoading || ownerLoading;

  if (isLoading) return <div>Loading...</div>;
  if (!parcel) return <div>Parcel not found</div>;

  return (
    <div>
      <h1>{parcel.name}</h1>
      <p>Coordinates: {parcel.x}, {parcel.y}</p>
      {owner && <p>Owner: {owner.name}</p>}
    </div>
  );
}
```

## State Derivado con Selectores

```tsx
import { useAppSelector } from '@/app/hooks';
import { selectFilteredParcels, selectActiveFiltersCount } from '@/features/land/land.selectors';

function ParcelList() {
  const parcels = useAppSelector(selectFilteredParcels);
  const filterCount = useAppSelector(selectActiveFiltersCount);

  return (
    <div>
      <h2>
        Parcels ({parcels.length})
        {filterCount > 0 && ` - ${filterCount} filter(s) active`}
      </h2>
      <ul>
        {parcels.map((parcel) => (
          <li key={parcel.id}>{parcel.name}</li>
        ))}
      </ul>
    </div>
  );
}
```

## Despachando Acciones

```tsx
import { useAppDispatch } from '@/app/hooks';
import { modalOpened, modalClosed } from '@/features/ui/ui.slice';

function TransferButton({ parcelId }: { parcelId: string }) {
  const dispatch = useAppDispatch();

  const handleClick = () => {
    // Despacha acción para abrir modal
    dispatch(modalOpened());
  };

  return <button onClick={handleClick}>Transfer Parcel</button>;
}
```

## Selectores de Entity Adapter

```tsx
import { useAppSelector } from '@/app/hooks';
import { creditsSelectors, selectTotalCredits } from '@/features/credits/credits.slice';

function CreditsSummary() {
  // Obtiene todas las transacciones
  const allTxs = useAppSelector(creditsSelectors.selectAll);
  
  // Obtiene transacción específica
  const txId = 'tx-123';
  const tx = useAppSelector((state) => 
    creditsSelectors.selectById(state, txId)
  );
  
  // Obtiene conteo total
  const count = useAppSelector(creditsSelectors.selectTotal);
  
  // Obtiene valor derivado
  const total = useAppSelector(selectTotalCredits);

  return (
    <div>
      <p>Total Credits: {total}</p>
      <p>Transactions: {count}</p>
      <ul>
        {allTxs.map((tx) => (
          <li key={tx.id}>
            {tx.type === 'grant' ? '+' : '-'}
            {tx.amount} - {tx.description}
          </li>
        ))}
      </ul>
    </div>
  );
}
```

## Prefetching de Datos

Prefetch de datos antes de la navegación para una UX más rápida:

```tsx
import { useAppDispatch } from '@/app/hooks';
import { client } from '@/services/client';
import { Link } from 'react-router-dom';

function ParcelListItem({ parcel }: { parcel: Parcel }) {
  const dispatch = useAppDispatch();

  const handleMouseEnter = () => {
    // Prefetch de detalles del parcel al hacer hover
    dispatch(
      client.util.prefetch('getParcel', { id: parcel.id }, { force: false })
    );
  };

  return (
    <Link
      to={`/parcels/${parcel.id}`}
      onMouseEnter={handleMouseEnter}
    >
      {parcel.name}
    </Link>
  );
}
```

## Gestión Manual del Cache

```tsx
import { useAppDispatch } from '@/app/hooks';
import { client } from '@/services/client';

function RefreshButton() {
  const dispatch = useAppDispatch();

  const handleRefresh = () => {
    // Invalida todos los queries de Parcels
    dispatch(client.util.invalidateTags(['Parcels']));
    
    // O reinicia todo el state del client
    // dispatch(client.util.resetApiState());
  };

  return <button onClick={handleRefresh}>Refresh Data</button>;
}
```

## Manejando Estados de Query

```tsx
function ComprehensiveExample({ id }: { id: string }) {
  const {
    data,
    isLoading,      // Carga inicial
    isFetching,     // Cualquier fetch (incluyendo refetch)
    isSuccess,      // Query exitoso
    isError,        // Query falló
    error,          // Objeto de error
    refetch,        // Función de refetch manual
  } = useGetParcelQuery({ id });

  // Diferentes estados
  if (isLoading) {
    return <Spinner />;
  }

  if (isError) {
    return (
      <div>
        <p>Error: {error.toString()}</p>
        <button onClick={() => refetch()}>Retry</button>
      </div>
    );
  }

  if (!data) {
    return <div>No data</div>;
  }

  return (
    <div>
      {isFetching && <div className="refetch-indicator">Updating...</div>}
      <h1>{data.name}</h1>
      <button onClick={() => refetch()}>Refresh</button>
    </div>
  );
}
```

## Integración de Formularios

```tsx
import { useState } from 'react';
import { useAppDispatch } from '@/app/hooks';
import { filtersUpdated } from '@/features/land/land.slice';

function FilterForm() {
  const dispatch = useAppDispatch();
  const [owner, setOwner] = useState('');
  const [minPrice, setMinPrice] = useState('');

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    
    dispatch(filtersUpdated({
      owner: owner || undefined,
      minPrice: minPrice ? parseInt(minPrice, 10) : undefined,
    }));
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={owner}
        onChange={(e) => setOwner(e.target.value)}
        placeholder="Owner address"
      />
      <input
        type="number"
        value={minPrice}
        onChange={(e) => setMinPrice(e.target.value)}
        placeholder="Min price"
      />
      <button type="submit">Apply Filters</button>
    </form>
  );
}
```

## Mejores Prácticas

### 1. Usa Hooks Tipados

```tsx
// ✅ Bien: Usa hooks tipados
import { useAppSelector, useAppDispatch } from '@/app/hooks';

const value = useAppSelector(selectValue);
const dispatch = useAppDispatch();

// ❌ Mal: Usa hooks sin tipar
import { useSelector, useDispatch } from 'react-redux';

const value = useSelector((state: RootState) => state.value);
const dispatch = useDispatch();
```

### 2. Maneja Todos los Estados de Query

```tsx
// ✅ Bien: Manejo comprehensivo de estados
const { data, isLoading, isError, error } = useQuery(args);
if (isLoading) return <Loading />;
if (isError) return <Error error={error} />;
if (!data) return null;
return <Content data={data} />;

// ❌ Mal: Falta manejo de errores
const { data } = useQuery(args);
return <Content data={data} />; // Puede crashear
```

### 3. Usa `.unwrap()` para Mutations

```tsx
// ✅ Bien: Manejo explícito de errores
try {
  const result = await mutation(args).unwrap();
  toast.success('Success!');
} catch (error) {
  toast.error('Failed!');
}

// ❌ Mal: Sin manejo de errores
mutation(args);
```

### 4. Limita Resultados con `selectFromResult`

```tsx
// ✅ Bien: Solo se suscribe a campos necesarios
const { name } = useQuery(args, {
  selectFromResult: ({ data }) => ({ name: data?.name }),
});

// ❌ Mal: Se suscribe al objeto completo
const { data } = useQuery(args);
const name = data?.name;
```

### 5. Evita Seleccionar Slices Completos

```tsx
// ✅ Bien: Selecciona valores específicos
const viewMode = useAppSelector(selectViewMode);
const filters = useAppSelector(selectFilters);

// ❌ Mal: Selecciona el slice completo
const ui = useAppSelector((state) => state.ui);
const viewMode = ui.viewMode;
```

## Próximos Pasos

* Aprende sobre [Web3 Integration](web3-integration.md) para patrones blockchain
* Revisa [Testing & Performance](testing-and-performance.md) para optimización
* Ve [RTK Query](rtk-query.md) para configuración avanzada de endpoints
