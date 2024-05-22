

# Zustand

[Zustand (opens in a new tab)](https://docs.pmnd.rs/zustand/getting-started/introduction) is a state management library that supports JavaScript and TypeScript. You can see it in use in the [`react` template (opens in a new tab)](https://github.com/latticexyz/mud/tree/main/templates/react).

## With React[](#with-react)

Zustand is native to React. The way you use it is very similar to the way you'd use `useState`, but with a bigger state tree that is synchronised with the contract state by MUD. To change state you send a transaction to a [System](/world/systems).

### Initialization[](#initialization)

To initialize the synchronization you use [`syncToZustand` (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/store-sync/src/zustand/syncToZustand.ts).

```ts
import { syncToZustand } from "@latticexyz/store-sync/zustand";
 
const { tables, useStore, latestBlock$, storedBlockLogs$, waitForTransaction } = await syncToZustand({
  config: mudConfig,
  address: networkConfig.worldAddress as Hex,
  publicClient,
  startBlock: BigInt(networkConfig.initialBlockNumber),
});
```

The most important fields returned by `syncToZustand` are:

- The `tables` variable, which contains the metadata (key schema, value schema, etc.) of the tables.
- The `useStore` [React hook (opens in a new tab)](https://react.dev/reference/react/hooks) you can use to [read information from tables (opens in a new tab)](https://github.com/pmndrs/zustand#selecting-multiple-state-slices). It can also be used [without React (opens in a new tab)](https://github.com/pmndrs/zustand#readingwriting-state-and-reacting-to-changes-outside-of-components).

### Get all records[](#get-all-records)

To get all the records in a specific table you can use this syntax:

```
const records = useStore(state => Object.values(state.getRecords(tables.<table name>)))
```

The result is a list of records. For every field that is part of the key each record contains a `key.<field name>` field. For every field that is part of the value each record contains a `value.<field name>` field.

For example, if you look at [the `Systems` table (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/world/mud.config.ts#L63-L72), you might get these records:

```
[
  {
    "id": "0x7462776f726c6400000000000000000053797374656d73000000000000000000:0x73790000000000000000000000000000636f7265000000000000000000000000",
    "table": {
      "keySchema": {
        "systemId": {
          "type": "bytes32",
          "internalType": "ResourceId"
        }
      },
      "valueSchema": {
        "system": {
          "type": "address",
          "internalType": "address"
        },
        "publicAccess": {
          "type": "bool",
          "internalType": "bool"
        }
      },
      "namespace": "world",
      "name": "Systems",
      "tableId": "0x7462776f726c6400000000000000000053797374656d73000000000000000000"
    },
    "keyTuple": ["0x73790000000000000000000000000000636f7265000000000000000000000000"],
    "key": {
      "systemId": "0x73790000000000000000000000000000636f7265000000000000000000000000"
    },
    "value": {
      "system": "0xD041DF1408B365897dA363b3b2100057514CC725",
      "publicAccess": true
    }
  },
  {
    "id": "0x7462776f726c6400000000000000000053797374656d73000000000000000000:0x737900000000000000000000000000005461736b7353797374656d0000000000",
    "table": {
      "keySchema": {
        "systemId": {
          "type": "bytes32",
          "internalType": "ResourceId"
        }
      },
      "valueSchema": {
        "system": {
          "type": "address",
          "internalType": "address"
        },
        "publicAccess": {
          "type": "bool",
          "internalType": "bool"
        }
      },
      "namespace": "world",
      "name": "Systems",
      "tableId": "0x7462776f726c6400000000000000000053797374656d73000000000000000000"
    },
    "keyTuple": ["0x737900000000000000000000000000005461736b7353797374656d0000000000"],
    "key": {
      "systemId": "0x737900000000000000000000000000005461736b7353797374656d0000000000"
    },
    "value": {
      "system": "0x29E2e167C27caab62666E2E88B4e3BEB579E351F",
      "publicAccess": true
    }
  }
]
```

### Get a specific record[](#get-a-specific-record)

To get a specific record you can use `getRecord`. For example, this code finds the record for the `systemId` that corresponds to `:TasksSystem`.

```
const key = {
  systemId: "0x737900000000000000000000000000005461736b7353797374656d0000000000",
};
const rec = useStore((state) => state.getRecord(tables.Systems, key));
```

For the result:

```
{
  "id": "0x7462776f726c6400000000000000000053797374656d73000000000000000000:0x737900000000000000000000000000005461736b7353797374656d0000000000",
  "table": {
    "keySchema": {
      "systemId": {
        "type": "bytes32",
        "internalType": "ResourceId"
      }
    },
    "valueSchema": {
      "system": {
        "type": "address",
        "internalType": "address"
      },
      "publicAccess": {
        "type": "bool",
        "internalType": "bool"
      }
    },
    "namespace": "world",
    "name": "Systems",
    "tableId": "0x7462776f726c6400000000000000000053797374656d73000000000000000000"
  },
  "keyTuple": ["0x737900000000000000000000000000005461736b7353797374656d0000000000"],
  "key": {
    "systemId": "0x737900000000000000000000000000005461736b7353797374656d0000000000"
  },
  "value": {
    "system": "0x29E2e167C27caab62666E2E88B4e3BEB579E351F",
    "publicAccess": true
  }
}
```

If you just want the value, you can use `getValue`.

```
const key = {
  systemId: "0x737900000000000000000000000000005461736b7353797374656d0000000000",
};
const val = useStore((state) => state.getValue(tables.Systems, key));
```

For the result:

```
{
  "system": "0x29E2e167C27caab62666E2E88B4e3BEB579E351F",
  "publicAccess": true
}
```

## Without React[](#without-react)

If your application does not use React, you can still use Zustand to read MUD data.

### Initialization[](#initialization-1)

```
import { syncToZustand } from "@latticexyz/store-sync/zustand";
 
const { tables, useStore, latestBlock$, storedBlockLogs$, waitForTransaction } = await syncToZustand({
  config: mudConfig,
  address: networkConfig.worldAddress as Hex,
  publicClient,
  startBlock: BigInt(networkConfig.initialBlockNumber),
});
```

### Reading data[](#reading-data)

To read data you use `useStore.getState()`. It supports the same functions as those supported in `useStore(state => ...)` in React.

- `useStore.getState().getRecords(tables.<table name>)` gives you all the records in a table.
- `useStore.getState().getRecord(tables.<table name>, key)` gives you a single record corresponding to that key.
- `useStore.getState().getValue(tables.<table name>, key)` gives you only the value corresponding to that key in the table without the extra information.

Seeing this in action

1. [Install the template](/templates/typescript/getting-started) and select the `vanilla` template.
    
2. Change to the `packages/client/src` directory inside the template.
    
    ```
    cd packages/client/src
    ```
    
3. Edit `mud/setupNetwork.ts` to add the Zustand definitions.
    
    setupNetwork.ts
    
    ```
    /*
     * The MUD client code is built on top of viem
     * (https://viem.sh/docs/getting-started.html).
     * This line imports the functions we need from it.
     */
    import {
      createPublicClient,
      fallback,
      webSocket,
      http,
      createWalletClient,
      Hex,
      parseEther,
      ClientConfig,
    } from "viem";
    import { createFaucetService } from "@latticexyz/services/faucet";
    import { encodeEntity, syncToRecs } from "@latticexyz/store-sync/recs";
     
    import { getNetworkConfig } from "./getNetworkConfig";
    import { world } from "./world";
    import IWorldAbi from "contracts/out/IWorld.sol/IWorld.abi.json";
    import { createBurnerAccount, getContract, transportObserver, ContractWrite } from "@latticexyz/common";
    import { syncToZustand } from "@latticexyz/store-sync/zustand";
     
    import { Subject, share } from "rxjs";
     
    /*
     * Import our MUD config, which includes strong types for
     * our tables and other config options. We use this to generate
     * things like RECS components and get back strong types for them.
     *
     * See https://mud.dev/templates/typescript/contracts#mudconfigts
     * for the source of this information.
     */
    import mudConfig from "contracts/mud.config";
     
    export type SetupNetworkResult = Awaited<ReturnType<typeof setupNetwork>>;
     
    export async function setupNetwork() {
      const networkConfig = await getNetworkConfig();
     
      /*
       * Create a viem public (read only) client
       * (https://viem.sh/docs/clients/public.html)
       */
      const clientOptions = {
        chain: networkConfig.chain,
        transport: transportObserver(fallback([webSocket(), http()])),
        pollingInterval: 1000,
      } as const satisfies ClientConfig;
     
      const publicClient = createPublicClient(clientOptions);
     
      /*
       * Create a temporary wallet and a viem client for it
       * (see https://viem.sh/docs/clients/wallet.html).
       */
      const burnerAccount = createBurnerAccount(networkConfig.privateKey as Hex);
      const burnerWalletClient = createWalletClient({
        ...clientOptions,
        account: burnerAccount,
      });
     
      /*
       * Create an observable for contract writes that we can
       * pass into MUD dev tools for transaction observability.
       */
      const write$ = new Subject<ContractWrite>();
     
      /*
       * Create an object for communicating with the deployed World.
       */
      const worldContract = getContract({
        address: networkConfig.worldAddress as Hex,
        abi: IWorldAbi,
        publicClient,
        walletClient: burnerWalletClient,
        onWrite: (write) => write$.next(write),
      });
     
      /*
       * Sync on-chain state into RECS and keeps our client in sync.
       * Uses the MUD indexer if available, otherwise falls back
       * to the viem publicClient to make RPC calls to fetch MUD
       * events from the chain.
       */
      const { components, latestBlock$, storedBlockLogs$, waitForTransaction } = await syncToRecs({
        world,
        config: mudConfig,
        address: networkConfig.worldAddress as Hex,
        publicClient,
        startBlock: BigInt(networkConfig.initialBlockNumber),
      });
     
      const {
        tables,
        useStore,
        /* latestBlock$, storedBlockLogs$, waitForTransaction */
      } = await syncToZustand({
        config: mudConfig,
        address: networkConfig.worldAddress as Hex,
        publicClient,
        startBlock: BigInt(networkConfig.initialBlockNumber),
      });
     
      /*
       * If there is a faucet, request (test) ETH if you have
       * less than 1 ETH. Repeat every 20 seconds to ensure you don't
       * run out.
       */
      if (networkConfig.faucetServiceUrl) {
        const address = burnerAccount.address;
        console.info("[Dev Faucet]: Player address -> ", address);
     
        const faucet = createFaucetService(networkConfig.faucetServiceUrl);
     
        const requestDrip = async () => {
          const balance = await publicClient.getBalance({ address });
          console.info(`[Dev Faucet]: Player balance -> ${balance}`);
          const lowBalance = balance < parseEther("1");
          if (lowBalance) {
            console.info("[Dev Faucet]: Balance is low, dripping funds to player");
            // Double drip
            await faucet.dripDev({ address });
            await faucet.dripDev({ address });
          }
        };
     
        requestDrip();
        // Request a drip every 20 seconds
        setInterval(requestDrip, 20000);
      }
     
      return {
        tables,
        useStore,
        world,
        components,
        playerEntity: encodeEntity({ address: "address" }, { address: burnerWalletClient.account.address }),
        publicClient,
        walletClient: burnerWalletClient,
        latestBlock$,
        storedBlockLogs$,
        waitForTransaction,
        worldContract,
        write$: write$.asObservable().pipe(share()),
      };
    }
    ```
    
4. Edit `index.ts` to create a function that reads from Zustand.
    
    index.ts
    
    ```
    import { setup } from "./mud/setup";
    import mudConfig from "contracts/mud.config";
     
    const {
      components,
      systemCalls: { increment },
      network,
    } = await setup();
     
    // Components expose a stream that triggers when the component is updated.
    components.Counter.update$.subscribe((update) => {
      const [nextValue, prevValue] = update.value;
      console.log("Counter updated", update, { nextValue, prevValue });
      document.getElementById("counter")!.innerHTML = String(nextValue?.value ?? "unset");
    });
     
    // Just for demonstration purposes: we create a global function that can be
    // called to invoke the Increment system contract via the world. (See IncrementSystem.sol.)
    (window as any).increment = async () => {
      console.log("new counter value:", await increment());
    };
     
    (window as any).zustand = () => {
      console.log("Records:");
      const records = Object.values(network.useStore.getState().getRecords(network.tables.Systems));
      console.log(records);
     
      const key = { systemId: "0x73790000000000000000000000000000636f7265000000000000000000000000" };
      const rec = network.useStore.getState().getRecord(network.tables.Systems, key);
      console.log("\nSingle record:");
      console.log(rec);
     
      const val = network.useStore.getState().getValue(network.tables.Systems, key);
      console.log("\nJust the value from the record:");
      console.log(val);
    };
     
    // https://vitejs.dev/guide/env-and-mode.html
    if (import.meta.env.DEV) {
      const { mount: mountDevTools } = await import("@latticexyz/dev-tools");
      mountDevTools({
        config: mudConfig,
        publicClient: network.publicClient,
        walletClient: network.walletClient,
        latestBlock$: network.latestBlock$,
        storedBlockLogs$: network.storedBlockLogs$,
        worldAddress: network.worldContract.address,
        worldAbi: network.worldContract.abi,
        write$: network.write$,
        recsWorld: network.world,
      });
    }
    ```
    
5. Run the application.
    
    ```
    cd ../../..
    pnpm dev
    ```
    
6. Browse to the application.
    
7. [Open the console (see Chrome instructions) (opens in a new tab)](https://developer.chrome.com/docs/devtools/console/javascript/).
    
8. Run this command to call `zustand` which we defined in `index.ts`:
    
    ```
    window.zustand();
    ```
    
9. See (in the console) the results:
    
    - List of all the records (in `:Systems`).
    - The record for a specific `System`.
    - Just the value for that `System`.

[RECS](/state-query/typescript/recs "RECS")[Indexer](/services/indexer "Indexer")

---

MIT 2023 © MUD