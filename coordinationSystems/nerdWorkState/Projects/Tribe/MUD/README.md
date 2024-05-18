NES-MUD World

The current build has the following systems:
## Tables
- Player
- Inventory
- Item
- Quest
- Room
- Exit
## Systems
- Character System
- Quest System
- Inventory System
- Room System
- Trading System

## UI 
-- Every system is managed by a component. Compoents are largely template atm feel free to go places with them.


Based on your notes, here are some ideas to streamline the implementation and expand the usage of the data defined in `mud.config.ts`.

### Streamlined Implementation
#### Consolidate System Contracts
Instead of having separate contracts for each system, you can consolidate them into a single contract with modular functions. This approach reduces redundancy and makes the codebase easier to manage.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import { System } from "@latticexyz/world/src/System.sol";
import { Player, Lore, Inventory, Item, Quest, Room, Exit, Character } from "../codegen/index.sol";
import { addressToEntityKey } from "../addressToEntityKey.sol";

contract GameSystem is System {
    // Player functions
    function spawnPlayer(PlayerData calldata data, LoreData calldata lore) public {
        bytes32 id = addressToEntityKey(msg.sender);
        PlayerData memory existingPlayer = Player.get(id);
        require(existingPlayer.health == 0, "Player already exists");
        Player.set(id, data);
        Lore.set(id, lore);
        Inventory.set(id, new uint8[](1));
        Item.set(0, ItemData({ itemCount: 1, itemName: "Axe" }));
    }

    // Quest functions
    function createQuest(string memory questName, string memory questDescription, uint8 rewardItemId) public {
        bytes32 playerId = addressToEntityKey(msg.sender);
        Quest.set(playerId, QuestData({ reward: rewardItemId, isActive: true, questName: questName, description: questDescription }));
    }

    function completeQuest(bytes32 questId) public {
        QuestData memory questData = Quest.get(questId);
        require(questData.isActive, "Quest is not active");
        bytes32 playerId = addressToEntityKey(msg.sender);
        _rewardItemToPlayer(playerId, questData.reward);
        Quest.set(playerId, QuestData({ reward: questData.reward, isActive: false, questName: questData.questName, description: questData.description }));
    }

    function _rewardItemToPlayer(bytes32 playerId, uint8 rewardItemId) internal {
        Inventory.pushItemIds(playerId, rewardItemId);
        Item.set(rewardItemId, ItemData({ itemCount: 1, itemName: "Reward Item" }));
    }

    // Room functions
    function createRoom(bytes32 roomId, string memory name, string memory description) public {
        Room.set(roomId, RoomData({ name: name, description: description }));
    }

    function createExit(bytes32 roomId, string memory direction, bytes32 targetRoomId) public {
        Exit.set(roomId, direction, ExitData({ targetRoomId: targetRoomId }));
    }

    // Item functions
    function createItem(bytes32 itemId, string memory name, string memory description, uint256 itemCount) public {
        Item.set(itemId, ItemData({ itemName: name, description: description, itemCount: itemCount }));
    }

    // Character functions
    function createCharacter(bytes32 characterId, string memory name, string memory description) public {
        Character.set(characterId, CharacterData({ name: name, description: description }));
    }
}
```

#### Simplify System Calls
You can also simplify the system calls by creating a unified interface for interacting with the game system.

```ts
import { ClientComponents } from "./createClientComponents";
import { SetupNetworkResult } from "./setupNetwork";
import { getComponentValue } from "@latticexyz/recs";
import { uuid } from "@latticexyz/utils";
import { toast } from "react-hot-toast";
import { usePlayerStore } from "~~/services/store/playerStore";

export type SystemCalls = ReturnType<typeof createSystemCalls>;

export function createSystemCalls(
  { playerEntity, worldContract, waitForTransaction }: SetupNetworkResult,
  { Player, Lore, Inventory, Item, Quest, Room, Exit, Character }: ClientComponents,
) {
  const handleTransaction = async (
    transactionFn: () => Promise<any>,
    loadingMessage: string,
    successMessage: string,
    errorMessage: string,
  ) => {
    toast.loading(loadingMessage);

    try {
      const tx = await transactionFn();
      await waitForTransaction(tx);
      toast.success(successMessage);
    } catch (error) {
      console.error("Transaction failed:", error);
      toast.error(errorMessage);
    } finally {
      toast.dismiss();
    }
  };

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

    toast.loading("Spawning player...");

    try {
      const args = {
        x: 0,
        y: 0,
        health: 100,
        name: playerName,
      };
      const tx = await worldContract.write.spawnPlayer([args, lore]);
      await waitForTransaction(tx);
      toast.success(`Player spawned! ${playerName}`);
    } catch (error) {
      console.error("Spawn transaction failed:", error);
      toast.error("Failed to spawn player.");
    } finally {
      setTimeout(() => {
        Player.removeOverride(playerId);
        toast.dismiss();
      }, 1000);
    }
  };

  const createQuest = async (name: string, description: string, reward: number) => {
    await handleTransaction(
      () => worldContract.write.createQuest([name, description, reward]),
      "Creating quest...",
      "Quest created!",
      "Failed to create quest.",
    );
  };

  const completeQuest = async (questId: string) => {
    await handleTransaction(
      () => worldContract.write.completeQuest([questId]),
      "Completing quest...",
      "Quest completed!",
      "Failed to complete quest.",
    );
  };

  const createRoom = async (roomId: string, name: string, description: string) => {
    await handleTransaction(
      () => worldContract.write.createRoom([roomId, name, description]),
      "Creating room...",
      "Room created!",
      "Failed to create room.",
    );
  };

  const createExit = async (roomId: string, direction: string, targetRoomId: string) => {
    await handleTransaction(
      () => worldContract.write.createExit([roomId, direction, targetRoomId]),
      "Creating exit...",
      "Exit created!",
      "Failed to create exit.",
    );
  };

  const createItem = async (itemId: string, name: string, description: string, itemCount: number) => {
    await handleTransaction(
      () => worldContract.write.createItem([itemId, name, description, itemCount]),
      "Creating item...",
      "Item created!",
      "Failed to create item.",
    );
  };

  const createCharacter = async (characterId: string, name: string, description: string) => {
    await handleTransaction(
      () => worldContract.write.createCharacter([characterId, name, description]),
      "Creating character...",
      "Character created!",
      "Failed to create character.",
    );
  };

  return {
    spawn,
    createQuest,
    completeQuest,
    createRoom,
    createExit,
    createItem,
    createCharacter,
  };
}
```

### Expanded Usage of Data
#### Enhanced Game Mechanics
1. **Dynamic Quests**: Use the `Quest` and `QuestStatus` tables to create dynamic quests that can be assigned to players. Track the progress and completion status of each quest.
2. **Trade System**: Leverage the `TradeRequest` and `TradeOffer` tables to implement a trading system where players can request and offer items.
3. **Room Navigation**: Utilize the `Room` and `Exit` tables to create a navigable map where players can move between rooms and interact with the environment.

#### Advanced Features
1. **Player Statistics**: Extend the `Player` table to include additional statistics such as experience points, level, and skills.
2. **Inventory Management**: Enhance the `Inventory` table to support item stacking, categorization, and sorting.
3. **NPC Interactions**: Use the `Character` table to define non-player characters (NPCs) with unique behaviors and dialogues.

#### Visualization and Analytics
1. **Game Dashboard**: Create a dashboard to visualize player statistics, quest progress, and item distribution.
2. **Heatmaps**: Generate heatmaps to analyze player movement and interactions within the game world.
3. **Event Logs**: Maintain event logs to track significant in-game events such as quest completions, trades, and player achievements.

By consolidating the system contracts, simplifying the system calls, and expanding the usage of the data, you can create a more streamlined and feature-rich implementation for your game.