#### <u>User</u>:
Read these notes and write a haiku about each one:


```ts
import { defineWorld } from "@latticexyz/world";

// Define enums for better type safety and readability
const EntityType = ["Player", "Room", "Item", "Character"];

export default defineWorld({
  enums: {
    EntityType,

    Direction: ["North", "East", "South", "West"],
  },
  tables: {
    TradeRequest: {
      schema: {
        requestId: "bytes32",
        requestedItem: "uint256",
        quantityRequested: "uint32",
        status: "uint8",
        requester: "address",
      },
      key: ["requestId"],
    },
    TradeOffer: {
      schema: {
        offerId: "bytes32",
        requestId: "bytes32",
        offeredItem: "uint256",
        quantityOffered: "uint32",
        offerer: "address",
      },
      key: ["offerId"],
    },
    Quest: {
      schema: {
        questId: "bytes32",
        reward: "bytes32",
        isActive: "bool",
        questName: "string",
        description: "string",
      },
      key: ["questId"],
    },
    QuestStatus: {
      schema: {
        questId: "bytes32",
        participant: "address",
        status: "uint8",
      },
      key: ["questId", "participant"],
    },
    Player: {
      schema: {
        id: "bytes32",
        name: "string",
      },
      key: ["id"],
    },
    Position: {
      schema: {
        id: "bytes32",
        x: "int32",
        y: "int32",
      },
      key: ["id"],
      codegen: {
        dataStruct: false,
      },
    },
    Movable: "bool",
    Lore: {
      schema: {
        id: "bytes32",
        alignment: "string",
        backstory: "string",
        currentQuest: "string",
      },
      key: ["id"],
    },
    Inventory: {
      schema: {
        ownerId: "bytes32",
        itemIds: "bytes32[]",
      },
      key: ["ownerId"],
    },
    Item: {
      schema: {
        itemId: "bytes32",
        itemCount: "uint256",
        itemName: "string",
        description: "string",
      },
      key: ["itemId"],
    },
    Room: {
      schema: {
        roomId: "bytes32",
        name: "string",
        description: "string",
      },
      key: ["roomId"],
    },
    MapConfig: {
      schema: {
        roomId: "bytes32",
        width: "uint32",
        height: "uint32",
        terrain: "bytes",
      },
      key: ["roomId"],
      codegen: {
        dataStruct: false,
      },
    },
    Exit: {
      schema: {
        roomId: "bytes32",
        targetRoomId: "bytes32",
        direction: "string",
      },
      key: ["roomId"],
    },
  },
  deploy: {
    upgradeableWorldImplementation: true,
  },
});

```

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
import { System } from "@latticexyz/world/src/System.sol";
import { Player, Lore, LoreData, Inventory, Item, ItemData } from "../codegen/index.sol";
import { addressToEntityKey } from "../addressToEntityKey.sol";


contract SpawnPlayerSystem is System {
  function spawnPlayer(string calldata data, LoreData calldata lore) public {
    // Ensure the player does not already exist
    bytes32 id = addressToEntityKey(msg.sender);
    string memory existingPlayer = Player.get(id);

    // Create the player
    Player.set(id, data);
    Lore.set(id, lore.alignment, lore.backstory, lore.currentQuest);
    Inventory.set(id, new bytes32[](1));
    Item.set(0, ItemData({ itemCount: 1, itemName: "Axe", description: "a sturdy axe" }));
  }
}

```

```ts
import React from "react";
import Image from "next/image";
import InventoryDisplay from "./InventoryDisplay";
import LoreDisplay from "./LoreDisplay";
import { pixelArt } from "@dicebear/collection";
import { createAvatar } from "@dicebear/core";

const PlayerList = props => {
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
                <InventoryDisplay entityId={player.key.id} />
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


```

#### <u>Assistant</u>:
### Haiku for the TypeScript Configuration

Enums chart the paths clear,  
Trading quests and lore entwined—  
Legends built in code.  

### Haiku for the Solidity Contract

Player not yet born,  
Lore and axe in hand arise—  
New legends embark.  

### Haiku for the React Component

Players on display,  
Pixel avatars in scroll—  
Lores and lives unfold.
#### Config
model: gpt-4o
temperature: 1
provider: openai

