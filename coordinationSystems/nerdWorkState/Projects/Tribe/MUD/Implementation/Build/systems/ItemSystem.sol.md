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