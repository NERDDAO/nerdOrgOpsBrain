Based on your notes, let's extend the MUD system to include the lore system and update the `mud.config` file accordingly.
### Step 1: Define Lore in `mud.config.ts`
Update your `mud.config.ts` to include the lore attributes for players.
#### `mud.config.ts`
```typescript
const mudConfig = {
tables: {
Player: {
schema: {
id: "string",
name: "string",
x: "number",
y: "number",
health: "number",
},
},
Lore: {
schema: {
playerId: "string",
alignment: "string",
backstory: "string",
currentQuest: "string",
},
},
},
};
export default mudConfig;
```
### Step 2: Update `setupNetwork.ts` to Sync Lore Data
Ensure that the lore data is synced to Zustand.
#### `setupNetwork.ts`
```typescript
import { getNetworkConfig } from "./getNetworkConfig";
import { world } from "./world";
import { ContractWrite, createBurnerAccount, transportObserver } from "@latticexyz/common";
import { transactionQueue, writeObserver } from "@latticexyz/common/actions";
import { createFaucetService } from "@latticexyz/services/faucet";
import { encodeEntity, syncToRecs } from "@latticexyz/store-sync/recs";
import { syncToZustand } from "@latticexyz/store-sync/zustand";
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
const clientOptions = {
chain: networkConfig.chain,
transport: transportObserver(fallback([webSocket(), http()])),
pollingInterval: 1000,
} as const satisfies ClientConfig;
const publicClient = createPublicClient(clientOptions);
const write$ = new Subject<ContractWrite>();
const burnerAccount = createBurnerAccount(networkConfig.privateKey as Hex);
const burnerWalletClient = createWalletClient({
...clientOptions,
account: burnerAccount,
})
.extend(transactionQueue())
.extend(writeObserver({ onWrite: write => write$.next(write) }));
const worldContract = getContract({
address: networkConfig.worldAddress as Hex,
abi: IWorldAbi,
client: { public: publicClient, wallet: burnerWalletClient },
});
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
await faucet.dripDev({ address });
await faucet.dripDev({ address });
}
};
requestDrip();
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
### Step 3: Update System Calls to Handle Lore
Modify the system calls to include fetching and updating lore for players.
#### Update `createSystemCalls.ts`
```typescript
import { ClientComponents } from "./createClientComponents";
import { SetupNetworkResult } from "./setupNetwork";
import { getComponentValue } from "@latticexyz/recs";
import { uuid } from "@latticexyz/utils";
import { toast } from "react-hot-toast";
import { hexToString, stringToHex } from "viem";
import { usePlayerStore } from "~~/services/store/playerStore";
export type SystemCalls = ReturnType<typeof createSystemCalls>;
export function createSystemCalls(
{ playerEntity, worldContract, waitForTransaction }: SetupNetworkResult,
{ Player, Lore }: ClientComponents,
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
const args = {
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
const updateLore = (playerId: string, lore: Partial<Lore>) => {
usePlayerStore.getState().setLore(playerId, lore);
};
const fetchLore = (playerId: string): Lore | undefined => {
return usePlayerStore.getState().getLore(playerId);
};
return {
spawn,
fetchPlayers,
updateLore,
fetchLore,
};
}
```
### Step 4: Update Lore Components
Ensure the lore components can display and edit lore information.
#### `LoreDisplay.tsx`
```tsx
import React from 'react';
import { fetchLore } from './loreSystem';
const LoreDisplay = ({ entityId }: { entityId: string }) => {
const lore = fetchLore(entityId);
if (!lore) {
return <div>No lore available for this entity.</div>;
}
return (
<div className="p-5 bg-gray-100 rounded-lg shadow-md">
<h2 className="mb-5 text-2xl font-bold text-gray-800">Lore:</h2>
<p><strong>Alignment:</strong> {lore.alignment}</p>
<p><strong>Backstory:</strong> {lore.backstory}</p>
<p><strong>Current Quest:</strong> {lore.currentQuest}</p>
</div>
);
};
export default LoreDisplay;
```
#### `LoreEditor.tsx`
```tsx
import React, { useState } from 'react';
import { updateLore } from './loreSystem';
const LoreEditor = ({ entityId }: { entityId: string }) => {
const [alignment, setAlignment] = useState('');
const [backstory, setBackstory] = useState('');
const [currentQuest, setCurrentQuest] = useState('');
const handleSave = () => {
updateLore(entityId, { alignment, backstory, currentQuest });
};
return (
<div className="p-5 bg-gray-100 rounded-lg shadow-md">
<h2 className="mb-5 text-2xl font-bold text-gray-800">Edit Lore:</h2>
<div>
<label>Alignment:</label>
<input value={alignment} onChange={(e) => setAlignment(e.target.value)} />
</div>
<div>
<label>Backstory:</label>
<textarea value={backstory} onChange={(e) => setBackstory(e.target.value)} />
</div>
<div>
<label>Current Quest:</label>
<input value={currentQuest} onChange={(e) => setCurrentQuest(e.target.value)} />
</div>
<button onClick={handleSave}>Save</button>
</div>
);
};
export default LoreEditor;
```
### Step 5: Update the GameBoard Component to Include Lore
Fetch the lore for players when the main component mounts and display it.
#### Update `GameBoard.tsx`
```tsx
import React, { useEffect } from "react";
import PlayerInput from "../PlayerInput";
import PlayerList from "../PlayerList";
import LoreDisplay from "../LoreDisplay";
import LoreEditor from "../LoreEditor";
import { useMUD } from "./MUDContext";
const GameBoard = () => {
const {
components: { Player },
network: { playerEntity, useStore, tables },
systemCalls: { spawn, fetchPlayers, fetchLore },
} = useMUD();
const records = useStore(state => Object.values(state.getRecords(tables.Player)));
useEffect(() => {
if (playerEntity) {
fetchPlayers();
}
}, [playerEntity]);
return (
<div>
<PlayerInput />
<button onClick={spawn}>Spawn</button>
<PlayerList players={records} />
{records.map(player => (
<div key={player.id}>
<LoreDisplay entityId={player.id} />
<LoreEditor entityId={player.id} />
</div>
))}
</div>
);
};
export default GameBoard;
```
By following these steps, you can integrate the lore component into the MUD system, allowing you to track and display players' alignment, backstory, and current quest. This integration ensures that the lore data is synced with the MUD system and can be easily managed using Zustand.