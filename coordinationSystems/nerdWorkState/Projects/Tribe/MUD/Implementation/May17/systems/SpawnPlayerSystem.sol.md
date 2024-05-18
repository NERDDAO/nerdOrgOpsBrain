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