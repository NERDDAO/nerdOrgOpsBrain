Based on your notes, here's how you can use `syncToZustand` to fetch all existing players and display them on the UI:

### Step 1: Initialize Zustand with MUD
First, ensure you have initialized Zustand with MUD using the `syncToZustand` function.

#### Update `setupNetwork.ts`
```typescript
/*
The MUD client code is built on top of viem
(https://viem.sh/docs/getting-started.html).
This line imports the functions we need from it.
*/
import { getNetworkConfig } from "./getNetworkConfig";
import { world } from "./world";
import { ContractWrite, createBurnerAccount, transportObserver } from "@latticexyz/common";
import { transactionQueue, writeObserver } from "@latticexyz/common/actions";
import { createFaucetService } from "@latticexyz/services/faucet";
import { encodeEntity, syncToRecs } from "@latticexyz/store-sync/recs";
import { syncToZustand } from "@latticexyz/store-sync/zustand";

/*
Import our MUD config, which includes strong types for
our tables and other config options. We use this to generate
things like RECS components and get back strong types for them.
See https://mud.dev/templates/typescript/contracts#mudconfigts
for the source of this information.
*/
import mudConfig from "contracts/mud.config";
import IWorldAbi from "contracts/out/IWorld.sol/IWorld.abi.json";
import { Subject, share } from "rxjs";
import {
  ClientConfig,
  Hex,
  createPublicClient,
  createWalletClient,
  fallback,
  getContract,
  http,
  parseEther,
  webSocket,
} from "viem";

export type SetupNetworkResult = Awaited<ReturnType<typeof setupNetwork>>;

export async function setupNetwork() {
  const networkConfig = await getNetworkConfig();
  /*
  Create a viem public (read only) client
  (https://viem.sh/docs/clients/public.html)
  */
  const clientOptions = {
    chain: networkConfig.chain,
    transport: transportObserver(fallback([webSocket(), http()])),
    pollingInterval: 1000,
  } as const satisfies ClientConfig;
  const publicClient = createPublicClient(clientOptions);
  /*
  Create an observable for contract writes that we can
  pass into MUD dev tools for transaction observability.
  */
  const write$ = new Subject<ContractWrite>();
  /*
  Create a temporary wallet and a viem client for it
  (see https://viem.sh/docs/clients/wallet.html).
  */
  const burnerAccount = createBurnerAccount(networkConfig.privateKey as Hex);
  const burnerWalletClient = createWalletClient({
    ...clientOptions,
    account: burnerAccount,
  })
    .extend(transactionQueue())
    .extend(writeObserver({ onWrite: write => write$.next(write) }));
  /*
  Create an object for communicating with the deployed World.
  */
  const worldContract = getContract({
    address: networkConfig.worldAddress as Hex,
    abi: IWorldAbi,
    client: { public: publicClient, wallet: burnerWalletClient },
  });
  /*
  Sync on-chain state into RECS and keeps our client in sync.
  Uses the MUD indexer if available, otherwise falls back
  to the viem publicClient to make RPC calls to fetch MUD
  events from the chain.
  */
  const { components, latestBlock$, storedBlockLogs$, waitForTransaction } = await syncToRecs({
    world,
    config: mudConfig,
    address: networkConfig.worldAddress as Hex,
    publicClient,
    startBlock: BigInt(networkConfig.initialBlockNumber),
  });

  const { tables, useStore } = await syncToZustand({
    config: mudConfig,
    address: networkConfig.worldAddress as Hex,
    publicClient,
    startBlock: BigInt(networkConfig.initialBlockNumber),
  });

  /*
  If there is a faucet, request (test) ETH if you have
  less than 1 ETH. Repeat every 20 seconds to ensure you don't
  run out.
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

### Step 2: Update Zustand Store
Modify the Zustand store to include methods for setting and fetching players.

#### Update `usePlayerStore.ts`
```typescript
import create from 'zustand';

interface Player {
  id: string;
  name: string;
  x: number;
  y: number;
  health: number;
}

interface PlayerStore {
  playerName: string;
  players: Player[];
  setPlayerName: (name: string) => void;
  setPlayers: (players: Player[]) => void;
  addPlayer: (player: Player) => void;
}

export const usePlayerStore = create<PlayerStore>((set) => ({
  playerName: '',
  players: [],
  setPlayerName: (name) => set({ playerName: name }),
  setPlayers: (players) => set({ players }),
  addPlayer: (player) => set((state) => ({ players: [...state.players, player] })),
}));
```

### Step 3: Fetch Existing Players
Create a function to fetch existing players and update the Zustand store.

#### Update `createSystemCalls.ts`
```typescript
import { ClientComponents } from "./createClientComponents";
import { SetupNetworkResult } from "./setupNetwork";
import { Has, HasValue, getComponentValue, runQuery } from "@latticexyz/recs";
import { singletonEntity } from "@latticexyz/store-sync/recs";
import { uuid } from "@latticexyz/utils";
import { toast } from "react-hot-toast";
import { hexToString, isHex, stringToHex } from "viem";
import { usePlayerStore } from "~~/services/store/playerStore";

export type SystemCalls = ReturnType<typeof createSystemCalls>;

export function createSystemCalls(
  { playerEntity, worldContract, waitForTransaction }: SetupNetworkResult,
  { Player }: ClientComponents,
) {
  const spawn = async () => {
    if (!playerEntity) {
      throw new Error("no player");
    }

    const canSpawn = getComponentValue(Player, playerEntity)?.value !== true;
    const playerName = usePlayerStore.getState().playerName;
    if (!canSpawn) {
      throw new Error("already spawned");
    }

    const playerId = uuid();
    Player.addOverride(playerId, {
      entity: playerEntity,
      value: { value: true },
    });

    const args: PlayerData = {
      x: 0,
      y: 0,
      health: 100,
      name: playerName,
    };
    toast.loading("Spawning player...");

    try {
      const tx = await worldContract.write.spawnPlayer([stringToHex(playerId.slice(4), { size: 32 }), args]);
      await waitForTransaction(tx);
      usePlayerStore.getState().addPlayer({ id: playerId, name: playerName, x: 0, y: 0, health: 100 });
    } catch (error) {
      console.error("Spawn transaction failed:", error);
    } finally {
      setTimeout(() => {
        Player.removeOverride(playerId);
        toast.dismiss();
        toast.success(`Player spawned! ${playerName}`);
      }, 1000);
    }
  };

  const fetchPlayers = async () => {
    try {
      const players = await worldContract.read.getPlayers();
      const formattedPlayers = players.map((player: any) => ({
        id: hexToString(player.id),
        name: hexToString(player.name),
        x: player.x,
        y: player.y,
        health: player.health,
      }));
      usePlayerStore.getState().setPlayers(formattedPlayers);
    } catch (error) {
      console.error("Failed to fetch players:", error);
    }
  };

  return {
    spawn,
    fetchPlayers,
  };
}

```

### Step 4: Fetch Players on Component Mount
Fetch the list of players when the main component mounts.

#### Update `GameBoard.tsx`
```tsx
import React, { useEffect } from "react";
import PlayerInput from "../PlayerInput";
import PlayerList from "../PlayerList";
import { useMUD } from "./MUDContext";

const GameBoard = () => {
  const {
    components: { Player },
    network: { playerEntity, useStore, tables },
    systemCalls: { spawn },
  } = useMUD();

  const records = useStore(state => Object.values(state.getRecords(tables.Player)));
  useEffect(() => {
    if (playerEntity) {
      // const players = useStore.getState();
      console.log("Players", records);

      //return players;
    }
  }, []);

  return (
    <div>
      <PlayerInput />
      <button onClick={spawn}>Spawn</button>
      <PlayerList players={records} />
    </div>
  );
};

export default GameBoard;
```

### Step 5: Update the Display Component
Ensure the display component shows the list of players.

#### Update `PlayerList.tsx`
```tsx
import React from "react";
import Image from "next/image";
import { pixelArt } from "@dicebear/collection";
import { createAvatar } from "@dicebear/core";

const PlayerList = (props: { players: any }) => {
  const { players } = props;

  return (
    <div className="p-5 bg-gray-100 rounded-lg shadow-md overflow-scroll max-h-svh  flex justify-items-end">
      <h2 className="mb-5 text-2xl font-bold text-gray-800">Players:</h2>
      <ul>
        {players.map(player => {
          const avatarSvg = createAvatar(pixelArt, {
            seed: player.value.name,
            dataUri: true,
          });

          return (
            <li key={player.id} className="flex p-4 mb-4 bg-white rounded-lg shadow-sm hover:bg-gray-50">
              <div className="flex-1">
                <h3 className="text-xl font-semibold text-blue-600">{player.value.name}</h3>
                <p className="mt-2 text-gray-700">
                  <strong>Coordinates:</strong> (x: {player.value.x}, y: {player.value.y})
                </p>
                <p className="mt-1 text-gray-700">
                  <strong>Health:</strong> {player.value.health}
                </p>
              </div>
              <div className="ml-4">
                <Image
                  className="w-12 h-12 rounded-full"
                  src={avatarSvg.toDataUriSync()}
                  alt={`${player.value.name}'s avatar`}
                  width={48}
                  height={48}
                />
              </div>
            </li>
          );
        })}
      </ul>
    </div>
  );
};

export default PlayerList;

```

By following these steps, you should be able to use `syncToZustand` to fetch all existing players from the contract and display them on the UI.