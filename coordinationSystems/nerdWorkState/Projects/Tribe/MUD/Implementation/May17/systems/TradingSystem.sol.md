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