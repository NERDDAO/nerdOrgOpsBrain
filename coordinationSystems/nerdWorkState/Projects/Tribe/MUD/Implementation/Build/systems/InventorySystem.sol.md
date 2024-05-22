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