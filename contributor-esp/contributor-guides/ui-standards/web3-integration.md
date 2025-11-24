# Integración Web3

Esta página cubre patrones para integrar funcionalidad blockchain con Redux y RTK Query, incluyendo manejo de conexiones de wallet, transacciones y eventos on-chain.

## Principio Fundamental: Mantén Objetos Web3 Fuera de Redux

{% hint style="danger" %}
**Nunca almacenes esto en Redux:**

* `window.ethereum`
* Instancias de Provider (`ethers.Provider`, `Web3Provider`)
* Instancias de Signer
* Conexiones WebSocket
* Instancias de Contract
* Instancias de `AbortController`

Estos son no serializables y violan los principios de Redux.
{% endhint %}

## Arquitectura Recomendada

### Web3 Context (Fuera de Redux)

```tsx
// src/contexts/Web3Context.tsx
import { createContext, useContext, useEffect, useState, ReactNode } from 'react';
import { ethers } from 'ethers';

interface Web3ContextValue {
  provider: ethers.providers.Web3Provider | null;
  signer: ethers.Signer | null;
  account: string | null;
  chainId: number | null;
  connect: () => Promise<void>;
  disconnect: () => void;
}

const Web3Context = createContext<Web3ContextValue | undefined>(undefined);

export function Web3Provider({ children }: { children: ReactNode }) {
  const [provider, setProvider] = useState<ethers.providers.Web3Provider | null>(null);
  const [signer, setSigner] = useState<ethers.Signer | null>(null);
  const [account, setAccount] = useState<string | null>(null);
  const [chainId, setChainId] = useState<number | null>(null);

  const connect = async () => {
    if (!window.ethereum) {
      throw new Error('MetaMask not installed');
    }

    const web3Provider = new ethers.providers.Web3Provider(window.ethereum);
    const accounts = await web3Provider.send('eth_requestAccounts', []);
    const network = await web3Provider.getNetwork();
    const signer = web3Provider.getSigner();

    setProvider(web3Provider);
    setSigner(signer);
    setAccount(accounts[0]);
    setChainId(network.chainId);
  };

  const disconnect = () => {
    setProvider(null);
    setSigner(null);
    setAccount(null);
    setChainId(null);
  };

  // Escucha cambios de cuenta
  useEffect(() => {
    if (!window.ethereum) return;

    const handleAccountsChanged = (accounts: string[]) => {
      if (accounts.length === 0) {
        disconnect();
      } else {
        setAccount(accounts[0]);
      }
    };

    const handleChainChanged = (chainIdHex: string) => {
      const newChainId = parseInt(chainIdHex, 16);
      setChainId(newChainId);
    };

    window.ethereum.on('accountsChanged', handleAccountsChanged);
    window.ethereum.on('chainChanged', handleChainChanged);

    return () => {
      window.ethereum?.removeListener('accountsChanged', handleAccountsChanged);
      window.ethereum?.removeListener('chainChanged', handleChainChanged);
    };
  }, []);

  return (
    <Web3Context.Provider
      value={{ provider, signer, account, chainId, connect, disconnect }}
    >
      {children}
    </Web3Context.Provider>
  );
}

export function useWeb3() {
  const context = useContext(Web3Context);
  if (!context) {
    throw new Error('useWeb3 must be used within Web3Provider');
  }
  return context;
}
```

### Redux Slice para State Web3

Almacena solo state Web3 serializable en Redux:

```tsx
// src/features/web3/web3.slice.ts
import { createSlice, PayloadAction } from '@reduxjs/toolkit';
import type { RootState } from '@/app/store';

interface Web3State {
  account: string | null;
  chainId: number | null;
  isConnected: boolean;
  pendingTxs: Record<string, PendingTransaction>;
}

interface PendingTransaction {
  hash: string;
  type: 'transfer' | 'mint' | 'approve';
  status: 'pending' | 'confirmed' | 'failed';
  timestamp: number;
}

const initialState: Web3State = {
  account: null,
  chainId: null,
  isConnected: false,
  pendingTxs: {},
};

const web3Slice = createSlice({
  name: 'web3',
  initialState,
  reducers: {
    connected(state, action: PayloadAction<{ account: string; chainId: number }>) {
      state.account = action.payload.account;
      state.chainId = action.payload.chainId;
      state.isConnected = true;
    },
    
    disconnected(state) {
      state.account = null;
      state.chainId = null;
      state.isConnected = false;
      state.pendingTxs = {};
    },
    
    chainChanged(state, action: PayloadAction<number>) {
      state.chainId = action.payload;
    },
    
    accountChanged(state, action: PayloadAction<string>) {
      state.account = action.payload;
    },
    
    txPending(state, action: PayloadAction<PendingTransaction>) {
      state.pendingTxs[action.payload.hash] = action.payload;
    },
    
    txConfirmed(state, action: PayloadAction<string>) {
      if (state.pendingTxs[action.payload]) {
        state.pendingTxs[action.payload].status = 'confirmed';
      }
    },
    
    txFailed(state, action: PayloadAction<string>) {
      if (state.pendingTxs[action.payload]) {
        state.pendingTxs[action.payload].status = 'failed';
      }
    },
    
    txRemoved(state, action: PayloadAction<string>) {
      delete state.pendingTxs[action.payload];
    },
  },
});

export const {
  connected,
  disconnected,
  chainChanged,
  accountChanged,
  txPending,
  txConfirmed,
  txFailed,
  txRemoved,
} = web3Slice.actions;

export default web3Slice.reducer;

// Selectors
export const selectAccount = (state: RootState) => state.web3.account;
export const selectChainId = (state: RootState) => state.web3.chainId;
export const selectIsConnected = (state: RootState) => state.web3.isConnected;
export const selectPendingTxs = (state: RootState) => 
  Object.values(state.web3.pendingTxs);
```

## Sincronizando Context con Redux

Conecta el context Web3 con Redux:

```tsx
// src/components/Web3Sync.tsx
import { useEffect } from 'react';
import { useWeb3 } from '@/contexts/Web3Context';
import { useAppDispatch } from '@/app/hooks';
import { connected, disconnected, chainChanged, accountChanged } from '@/features/web3/web3.slice';
import { client } from '@/services/client';

export function Web3Sync() {
  const { account, chainId, connect } = useWeb3();
  const dispatch = useAppDispatch();

  // Sincroniza estado de conexión
  useEffect(() => {
    if (account && chainId) {
      dispatch(connected({ account, chainId }));
    } else {
      dispatch(disconnected());
    }
  }, [account, chainId, dispatch]);

  // Reinicia cache en cambio de cuenta/chain
  useEffect(() => {
    if (account || chainId) {
      // Opción 1: Reinicia todo el state del client
      dispatch(client.util.resetApiState());
      
      // Opción 2: Invalida tags específicos
      // dispatch(client.util.invalidateTags(['User', 'Parcels', 'Credits']));
    }
  }, [account, chainId, dispatch]);

  // Auto-conecta al montar si estaba conectado previamente
  useEffect(() => {
    const autoConnect = async () => {
      const wasConnected = localStorage.getItem('walletConnected');
      if (wasConnected && window.ethereum) {
        try {
          await connect();
        } catch (error) {
          console.error('Auto-connect failed:', error);
        }
      }
    };

    autoConnect();
  }, [connect]);

  return null;
}
```

## Ciclo de Vida de Transacciones

### Enviando Transacciones

```tsx
// src/hooks/useTransferParcel.ts
import { useCallback } from 'react';
import { useWeb3 } from '@/contexts/Web3Context';
import { useAppDispatch } from '@/app/hooks';
import { txPending, txConfirmed, txFailed } from '@/features/web3/web3.slice';
import { client } from '@/services/client';
import { ParcelContract } from '@/contracts';

export function useTransferParcel() {
  const { signer, account } = useWeb3();
  const dispatch = useAppDispatch();

  return useCallback(async (parcelId: string, to: string) => {
    if (!signer || !account) {
      throw new Error('Wallet not connected');
    }

    // Obtiene instancia del contrato
    const contract = ParcelContract.connect(signer);

    try {
      // Envía transacción
      const tx = await contract.transfer(parcelId, to);

      // Agrega a pendientes
      dispatch(txPending({
        hash: tx.hash,
        type: 'transfer',
        status: 'pending',
        timestamp: Date.now(),
      }));

      // Actualiza cache optimistically
      dispatch(
        client.util.updateQueryData('getParcel', { id: parcelId }, (draft) => {
          draft.owner = to;
        })
      );

      // Espera confirmación
      const receipt = await tx.wait();

      if (receipt.status === 1) {
        // Transacción confirmada
        dispatch(txConfirmed(tx.hash));
        
        // Invalida queries afectados
        dispatch(client.util.invalidateTags([
          { type: 'Parcels', id: parcelId },
          'Parcels',
        ]));
      } else {
        // Transacción falló
        dispatch(txFailed(tx.hash));
        
        // Revierte actualización optimistic
        dispatch(client.util.invalidateTags([
          { type: 'Parcels', id: parcelId },
        ]));
      }

      return receipt;
    } catch (error) {
      // Transacción rechazada o falló
      if (error.hash) {
        dispatch(txFailed(error.hash));
      }
      
      // Revierte actualización optimistic
      dispatch(client.util.invalidateTags([
        { type: 'Parcels', id: parcelId },
      ]));
      
      throw error;
    }
  }, [signer, account, dispatch]);
}
```

### Usando en Componentes

```tsx
function TransferParcelButton({ parcelId }: { parcelId: string }) {
  const transferParcel = useTransferParcel();
  const [recipient, setRecipient] = useState('');
  const [isLoading, setIsLoading] = useState(false);

  const handleTransfer = async () => {
    setIsLoading(true);
    try {
      await transferParcel(parcelId, recipient);
      toast.success('Transfer successful!');
      setRecipient('');
    } catch (error) {
      if (error.code === 4001) {
        toast.error('Transaction rejected');
      } else {
        toast.error('Transfer failed');
      }
    } finally {
      setIsLoading(false);
    }
  };

  return (
    <div>
      <input
        value={recipient}
        onChange={(e) => setRecipient(e.target.value)}
        placeholder="Recipient address"
      />
      <button onClick={handleTransfer} disabled={isLoading || !recipient}>
        {isLoading ? 'Transferring...' : 'Transfer'}
      </button>
    </div>
  );
}
```

## Escuchando Eventos On-Chain

```tsx
// src/hooks/useParcelEvents.ts
import { useEffect } from 'react';
import { useWeb3 } from '@/contexts/Web3Context';
import { useAppDispatch } from '@/app/hooks';
import { client } from '@/services/client';
import { ParcelContract } from '@/contracts';

export function useParcelEvents() {
  const { provider } = useWeb3();
  const dispatch = useAppDispatch();

  useEffect(() => {
    if (!provider) return;

    const contract = ParcelContract.connect(provider);

    // Escucha eventos Transfer
    const handleTransfer = (from: string, to: string, tokenId: string) => {
      console.log(`Parcel ${tokenId} transferred from ${from} to ${to}`);
      
      // Invalida queries afectados
      dispatch(client.util.invalidateTags([
        { type: 'Parcels', id: tokenId },
        'Parcels',
      ]));
    };

    // Suscribe a eventos
    contract.on('Transfer', handleTransfer);

    // Limpieza
    return () => {
      contract.off('Transfer', handleTransfer);
    };
  }, [provider, dispatch]);
}
```

## RTK Query con Datos Web3

Crea endpoints que usan datos blockchain:

```tsx
// src/features/nft/nft.client.ts
import { client } from '@/services/client';
import { ethers } from 'ethers';

export const nftClient = client.injectEndpoints({
  endpoints: (build) => ({
    // Híbrido: fetch desde API y verifica on-chain
    getNFTWithOwnership: build.query<NFT, { id: string; account?: string }>({
      async queryFn({ id, account }, { getState }) {
        try {
          // Fetch metadata desde API
          const response = await fetch(`/api/nfts/${id}`);
          const nft = await response.json();

          // Verifica ownership on-chain si se proporciona account
          if (account && window.ethereum) {
            const provider = new ethers.providers.Web3Provider(window.ethereum);
            const contract = NFTContract.connect(provider);
            const owner = await contract.ownerOf(id);
            
            nft.isOwner = owner.toLowerCase() === account.toLowerCase();
          }

          return { data: nft };
        } catch (error) {
          return { error: error.message };
        }
      },
      providesTags: (result, error, arg) => [
        { type: 'NFTs', id: arg.id },
      ],
    }),
  }),
});
```

## Estrategias de Invalidación de Cache

### En Cambio de Network

```tsx
// Invalida todos los datos cuando cambia la network
useEffect(() => {
  if (chainId) {
    dispatch(client.util.resetApiState());
  }
}, [chainId, dispatch]);
```

### En Cambio de Account

```tsx
// Invalida datos específicos del usuario
useEffect(() => {
  if (account) {
    dispatch(client.util.invalidateTags(['User', 'Credits', 'NFTs']));
  }
}, [account, dispatch]);
```

### Después de Confirmación de Transacción

```tsx
// Invalida datos relacionados después de transacción
if (receipt.status === 1) {
  dispatch(client.util.invalidateTags([
    { type: 'Parcels', id: parcelId },
    'Parcels',
    'User', // Puede afectar balance del usuario
  ]));
}
```

## Mejores Prácticas

### 1. Separa Responsabilidades

```tsx
// ✅ Bien: Web3 en context, state en Redux
const { signer } = useWeb3(); // Desde context
const account = useAppSelector(selectAccount); // Desde Redux

// ❌ Mal: Todo en Redux
const { signer, account } = useAppSelector(selectWeb3); // No hagas esto
```

### 2. Maneja Estados de Transacciones

```tsx
// ✅ Bien: Rastrea todos los estados
const tx = await contract.transfer(...);
dispatch(txPending(tx.hash));
const receipt = await tx.wait();
if (receipt.status === 1) {
  dispatch(txConfirmed(tx.hash));
} else {
  dispatch(txFailed(tx.hash));
}

// ❌ Mal: Fire and forget
await contract.transfer(...);
```

### 3. Actualizaciones Optimistas con Rollback

```tsx
// ✅ Bien: Optimistic con rollback
dispatch(client.util.updateQueryData(...));
try {
  await tx.wait();
  dispatch(client.util.invalidateTags(...));
} catch {
  dispatch(client.util.invalidateTags(...)); // Rollback
}

// ❌ Mal: Sin rollback
dispatch(client.util.updateQueryData(...));
await tx.wait(); // ¿Qué pasa si esto falla?
```

### 4. Limpieza de Event Listeners

```tsx
// ✅ Bien: Limpia listeners
useEffect(() => {
  contract.on('Transfer', handler);
  return () => contract.off('Transfer', handler);
}, [contract]);

// ❌ Mal: Memory leak
useEffect(() => {
  contract.on('Transfer', handler);
}, [contract]);
```

## Próximos Pasos

* Revisa [Testing & Performance](testing-and-performance.md) para optimización
* Ve [Component Patterns](component-patterns.md) para ejemplos de uso
* Entiende [RTK Query](rtk-query.md) para gestión de cache
