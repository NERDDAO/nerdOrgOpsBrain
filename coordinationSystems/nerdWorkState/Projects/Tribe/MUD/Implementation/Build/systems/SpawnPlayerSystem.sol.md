---
tags:
  - mud
---

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