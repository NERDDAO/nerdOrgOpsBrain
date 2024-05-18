```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import { System } from "@latticexyz/world/src/System.sol";
import { Inventory, Item, ItemData, Quest, QuestData } from "../codegen/index.sol";
import { addressToEntityKey } from "../addressToEntityKey.sol";

contract DynamicQuestSystem is System {
  uint32 public questCounter;

  event QuestCreated(uint questId, string questName, string questDescription, uint8 rewardItemId);
  event QuestCompleted(bytes32 questId, address player);

  function createQuest(string memory questName, string memory questDescription, uint8 rewardItemId) public {
    questCounter++;
    bytes32 playerId = addressToEntityKey(msg.sender);
    Quest.set(
      playerId,
      QuestData({ reward: rewardItemId, isActive: true, questName: questName, description: questDescription })
    );

    emit QuestCreated(questCounter, questName, questDescription, rewardItemId);
  }

  function completeQuest(bytes32 questId) public {
    QuestData memory questData = Quest.get(questId);
    require(questData.isActive, "Quest is not active");

    bytes32 playerId = addressToEntityKey(msg.sender);

    _rewardItemToPlayer(playerId, questData.reward);

    Quest.set(
      playerId,
      QuestData({
        reward: questData.reward,
        isActive: false,
        questName: questData.questName,
        description: questData.description
      })
    );

    emit QuestCompleted(questId, msg.sender);
  }

  function _rewardItemToPlayer(bytes32 playerId, uint8 rewardItemId) internal {
    Inventory.pushItemIds(playerId, rewardItemId);
    Item.set(rewardItemId, ItemData({ itemCount: 1, itemName: "Reward Item" })); // Presume reward item data available
  }
}

```



```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import { System } from "@latticexyz/world/src/System.sol";
import { Inventory, Item, ItemData } from "../codegen/index.sol";
import { addressToEntityKey } from "../addressToEntityKey.sol";

contract InventorySystem is System {
  function addItemToInventory() public {
    // Ensure the item does not already exist
    bytes32 id = addressToEntityKey(msg.sender);
    //ItemData memory existingItem = Item.get(itemId);
    // require(existingItem.itemCount > 0, "Item already exists");

    Item.set(0, ItemData({ itemCount: 1, itemName: "Axe" }));
    // Create the itema

    // Get the inventory item IDs of the owner

    // Add the new item to the inventory
    Inventory.pushItemIds(id, 1);
  }

  function removeItemFromInventory(uint8 itemId) public {
    // Get the inventory item IDs of the owner

    bytes32 id = addressToEntityKey(msg.sender);
    // Update the inventory
    Inventory.popItemIds(id);

    // Remove the item from the Item table
    Item.deleteRecord(itemId);
  }
}

```

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
import { System } from "@latticexyz/world/src/System.sol";
import { Player, Lore, LoreData, PlayerData, Inventory, Item, ItemData } from "../codegen/index.sol";
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
    Inventory.set(id, new uint8[](1));
    Item.set(0, ItemData({ itemCount: 1, itemName: "Axe" }));
  }
}
```

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import { System } from "@latticexyz/world/src/System.sol";
import { Inventory, Item, ItemData } from "../codegen/index.sol";
import { addressToEntityKey } from "../addressToEntityKey.sol";

contract TradingSystem is System {
  event TradeExecuted(address from, address to, uint88 itemId);

  function tradeItem(address to, uint8 itemId) public {
    bytes32 fromId = addressToEntityKey(msg.sender);
    bytes32 toId = addressToEntityKey(to);

    // Check that the sender owns the item
    require(_ownsItem(fromId, itemId), "Sender does not own the item");

    // Transfer the item from sender to the recipient
    _removeItemFromInventory(fromId, itemId);
    _addItemToInventory(toId, itemId);

    emit TradeExecuted(msg.sender, to, itemId);
  }

  function _ownsItem(bytes32 ownerId, uint8 itemId) internal view returns (bool) {
    uint8[] memory itemIds = Inventory.getItemIds(ownerId);
    for (uint i = 0; i < itemIds.length; i++) {
      if (itemIds[i] == itemId) {
        return true;
      }
    }
    return false;
  }

  function _removeItemFromInventory(bytes32 ownerId, uint8 itemId) internal {
    Item.deleteRecord(itemId);
    //Inventory.removeItemId(ownerId, itemId); // Assuming a function that removes specific item ID from the owner's inventory
  }

  function _addItemToInventory(bytes32 ownerId, uint8 itemId) internal {
    Inventory.pushItemIds(ownerId, itemId);

    Item.set(0, ItemData({ itemCount: 1, itemName: "Axe" }));
  }
}
```

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import { System } from "@latticexyz/world/src/System.sol";
import { Character, CharacterData } from "../codegen/index.sol";
import { addressToEntityKey } from "../addressToEntityKey.sol";

contract CharacterSystem is System {
    event CharacterCreated(bytes32 characterId, string name, string description);

    function createCharacter(bytes32 characterId, string memory name, string memory description) public {
        Character.set(characterId, CharacterData({ name: name, description: description }));
        emit CharacterCreated(characterId, name, description);
    }
}
```

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import { System } from "@latticexyz/world/src/System.sol";
import { Item, ItemData } from "../codegen/index.sol";
import { addressToEntityKey } from "../addressToEntityKey.sol";

contract ItemSystem is System {
    event ItemCreated(bytes32 itemId, string name, string description);

    function createItem(bytes32 itemId, string memory name, string memory description, uint256 itemCount) public {
        Item.set(itemId, ItemData({ itemName: name, description: description, itemCount: itemCount }));
        emit ItemCreated(itemId, name, description);
    }
}
```

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import { System } from "@latticexyz/world/src/System.sol";
import { Room, RoomData, Exit, ExitData } from "../codegen/index.sol";
import { addressToEntityKey } from "../addressToEntityKey.sol";

contract RoomSystem is System {
    event RoomCreated(bytes32 roomId, string name, string description);
    event ExitCreated(bytes32 roomId, string direction, bytes32 targetRoomId);

    function createRoom(bytes32 roomId, string memory name, string memory description) public {
        Room.set(roomId, RoomData({ name: name, description: description }));
        emit RoomCreated(roomId, name, description);
    }

    function createExit(bytes32 roomId, string memory direction, bytes32 targetRoomId) public {
        Exit.set(roomId, direction, ExitData({ targetRoomId: targetRoomId }));
        emit ExitCreated(roomId, direction, targetRoomId);
    }
}
```