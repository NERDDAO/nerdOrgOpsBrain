## Mud Config 
```
import { defineWorld } from "@latticexyz/world";

// Define enums for better type safety and readability
const EntityType = ["Player"];

export default defineWorld({
  enums: {
    EntityType,
  },
  tables: {
    Player: {
      schema: {
        id: "bytes32",
        x: "int32",
        y: "int32",
        health: "uint32",
        name: "string",
      },
      key: ["id"],
    },
    Lore: {
      schema: {
        id: "bytes32",
        alignment: "string",
        backstory: "string",
        currentQuest: "string",
      },
      key: ["id"],
    },
  },
  deploy: {
    upgradeableWorldImplementation: true,
  },
});
```

## Player System

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
import { System } from "@latticexyz/world/src/System.sol";
import { Player, Lore, LoreData, PlayerData } from "../codegen/index.sol";
import { addressToEntityKey } from "../addressToEntityKey.sol";

contract SpawnPlayerSystem is System {
  function spawnPlayer(PlayerData calldata data, LoreData calldata lore) public {
    // Ensure the player does not already exist
    bytes32 id = addressToEntityKey(msg.sender);
    PlayerData memory existingPlayer = Player.get(id);
    require(existingPlayer.health == 0, "Player already exists"); // Assuming health is 0 for non-existent players

    // Create the player
    Player.set(id, PlayerData({ x: data.x, y: data.y, health: data.health, name: data.name }));
    Lore.set(id, lore.alignment, lore.backstory, lore.currentQuest);
  }
}
```

## System Calls

```
import { ClientComponents } from "./createClientComponents";
import { SetupNetworkResult } from "./setupNetwork";
import { getComponentValue } from "@latticexyz/recs";
import { uuid } from "@latticexyz/utils";
import { toast } from "react-hot-toast";
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

    const lore = usePlayerStore.getState().lore;
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
      const tx = await worldContract.write.spawnPlayer([args, lore]);
      await waitForTransaction(tx);
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

  return {
    spawn,
  };
}
```

## Game Board 

```
import React from "react";
import PlayerInput from "../PlayerInput";
import PlayerList from "../PlayerList";
import { useMUD } from "./MUDContext";

const GameBoard = () => {
  const {
    network: { useStore, tables },
  } = useMUD();

  const records = useStore(state => Object.values(state.getRecords(tables.Player)));

  return (
    <div className="container flex flex-row mx-auto w-full h-full">
      <PlayerInput />
      <PlayerList players={records} />
    </div>
  );
};

export default GameBoard;
```

## Player Input

```
import React, { useState } from "react";
import { useMUD } from "./mud/MUDContext";
import { usePlayerStore } from "~~/services/store/playerStore";

const PlayerInput = () => {
  const playerName = usePlayerStore(state => state.playerName);
  const setPlayerName = usePlayerStore(state => state.setPlayerName);
  const { lore, setLore } = usePlayerStore();

  const {
    systemCalls: { spawn },
  } = useMUD();

  return (
    <div className="p-2 form-control">
      <h2 className="mb-5 text-2xl font-bold text-gray-800">Edit Lore:</h2>
      <div>
        <label className="label">Player Name</label>
        <input
          className="input"
          type="text"
          value={playerName}
          onChange={e => setPlayerName(e.target.value)}
          placeholder="Enter player name"
        />
      </div>
      <div>
        <label className="label">Alignment:</label>
        <input
          className="input"
          value={lore.alignment}
          onChange={e => setLore({ ...lore, alignment: e.target.value })}
        />
      </div>
      <div>
        <label className="label">Backstory:</label>
        <textarea
          className="input"
          value={lore.backstory}
          onChange={e => setLore({ ...lore, backstory: e.target.value })}
        />
      </div>
      <div>
        <label className="label">Current Quest:</label>
        <input
          className="input"
          value={lore.currentQuest}
          onChange={e => setLore({ ...lore, currentQuest: e.target.value })}
        />
      </div>

      <button className="btn" onClick={spawn}>
        spawn
      </button>
    </div>
  );
};

export default PlayerInput;
```

## Player List

```
import React from "react";
import Image from "next/image";
import LoreDisplay from "./LoreDisplay";
import { pixelArt } from "@dicebear/collection";
import { createAvatar } from "@dicebear/core";

const PlayerList = (props: { players: any }) => {
  const { players } = props;
  return (
    <div className="p-5 bg-gray-100 rounded-lg shadow-md overflow-scroll max-h-svh flex justify-items-end">
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
                <LoreDisplay entityId={player.key.id} />
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