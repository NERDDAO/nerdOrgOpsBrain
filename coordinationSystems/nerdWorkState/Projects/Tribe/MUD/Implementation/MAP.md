Based on your notes, here is an action plan for implementing new features using the framework described in your [[CodeReport]]:
### Action Plan for New Features
#### 1. **Feature: Enhanced Inventory Management**
- **Objective:** Improve the inventory system to support item categorization, stackable items, and inventory limits.
- **Steps:**
1. **Define New Schemas:**
- Update `mud.config.ts` to include new fields for item categories and stack limits.
```typescript
Inventory: {
schema: {
ownerId: "bytes32",
items: "Item[]",
maxCapacity: "uint256",
},
key: ["ownerId"],
},
Item: {
schema: {
itemId: "uint256",
itemCount: "uint256",
itemName: "string",
category: "string",
stackLimit: "uint256",
},
key: ["itemId"],
},
```
2. **Update Solidity Contract:**
- Modify `InventorySystem.sol` to handle item stacking and enforce inventory limits.
```solidity
function addItemToInventory(bytes32 ownerId, uint256 itemId, uint256 count) public {
InventoryData memory inventory = Inventory.get(ownerId);
// Check if the item already exists and stack it
uint256 index = findIndex(inventory.items, itemId);
if (index < inventory.items.length) {
require(inventory.items[index].itemCount + count <= inventory.items[index].stackLimit, "Exceeds stack limit");
inventory.items[index].itemCount += count;
} else {
require(inventory.items.length < inventory.maxCapacity, "Inventory full");
inventory.items.push(Item({itemId: itemId, itemCount: count, itemName: "", category: "", stackLimit: 0}));
}
Inventory.set(ownerId, inventory);
}
```
3. **Implement Client-Side Logic:**
- Update the client-side code to handle new inventory features, such as displaying item categories and stack counts.
#### 2. **Feature: Player Trading System**
- **Objective:** Enable players to trade items with each other.
- **Steps:**
1. **Define New Schemas:**
- Add a new table for trade offers in `mud.config.ts`.
```typescript
TradeOffer: {
schema: {
offerId: "bytes32",
fromPlayer: "bytes32",
toPlayer: "bytes32",
offeredItems: "Item[]",
requestedItems: "Item[]",
status: "string", // "pending", "accepted", "rejected"
},
key: ["offerId"],
},
```
2. **Create Trade System Contract:**
- Implement a new contract `TradeSystem.sol` to handle trade offers.
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
import { System } from "@latticexyz/world/src/System.sol";
import { TradeOffer, TradeOfferData, Inventory, InventoryData } from "../codegen/index.sol";
import { addressToEntityKey } from "../addressToEntityKey.sol";
contract TradeSystem is System {
function createTradeOffer(bytes32 toPlayer, Item[] memory offeredItems, Item[] memory requestedItems) public {
bytes32 fromPlayer = addressToEntityKey(_msgSender());
bytes32 offerId = keccak256(abi.encodePacked(fromPlayer, toPlayer, block.timestamp));
TradeOffer.set(offerId, TradeOfferData({
offerId: offerId,
fromPlayer: fromPlayer,
toPlayer: toPlayer,
offeredItems: offeredItems,
requestedItems: requestedItems,
status: "pending"
}));
}
function acceptTradeOffer(bytes32 offerId) public {
TradeOfferData memory offer = TradeOffer.get(offerId);
require(offer.toPlayer == addressToEntityKey(_msgSender()), "Not the recipient");
require(keccak256(abi.encodePacked(offer.status)) == keccak256("pending"), "Offer not pending");
// Transfer items between players
// Update inventories and offer status
offer.status = "accepted";
TradeOffer.set(offerId, offer);
}
function rejectTradeOffer(bytes32 offerId) public {
TradeOfferData memory offer = TradeOffer.get(offerId);
require(offer.toPlayer == addressToEntityKey(_msgSender()), "Not the recipient");
require(keccak256(abi.encodePacked(offer.status)) == keccak256("pending"), "Offer not pending");
offer.status = "rejected";
TradeOffer.set(offerId, offer);
}
}
```
3. **Implement Client-Side Logic:**
- Add UI components for creating, viewing, and managing trade offers.
- Ensure that the client-side code interacts correctly with the `TradeSystem` contract.
#### 3. **Feature: Dynamic Quests System**
- **Objective:** Introduce a dynamic quests system where players can receive and complete quests.
- **Steps:**
1. **Define New Schemas:**
- Add tables for quests and player quest progress in `mud.config.ts`.
```typescript
Quest: {
schema: {
questId: "bytes32",
title: "string",
description: "string",
reward: "Item[]",
status: "string", // "available", "completed"
},
key: ["questId"],
},
PlayerQuest: {
schema: {
playerId: "bytes32",
questId: "bytes32",
progress: "uint256",
status: "string", // "in-progress", "completed"
},
key: ["playerId", "questId"],
},
```
2. **Create Quest System Contract:**
- Implement a new contract `QuestSystem.sol` to manage quests.
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
import { System } from "@latticexyz/world/src/System.sol";
import { Quest, QuestData, PlayerQuest, PlayerQuestData, Inventory, InventoryData } from "../codegen/index.sol";
import { addressToEntityKey } from "../addressToEntityKey.sol";
contract QuestSystem is System {
function startQuest(bytes32 questId) public {
bytes32 playerId = addressToEntityKey(_msgSender());
PlayerQuest.set(playerId, questId, PlayerQuestData({
playerId: playerId,
questId: questId,
progress: 0,
status: "in-progress"
}));
}
function completeQuest(bytes32 questId) public {
bytes32 playerId = addressToEntityKey(_msgSender());
PlayerQuestData memory playerQuest = PlayerQuest.get(playerId, questId);
require(keccak256(abi.encodePacked(playerQuest.status)) == keccak256("in-progress"), "Quest not in progress");
// Update player quest progress and status
playerQuest.status = "completed";
PlayerQuest.set(playerId, questId, playerQuest);
// Grant rewards to player
QuestData memory quest = Quest.get(questId);
InventoryData memory inventory = Inventory.get(playerId);
for (uint i = 0; i < quest.reward.length; i++) {
inventory.items.push(quest.reward[i]);
}
Inventory.set(playerId, inventory);
}
}
```
3. **Implement Client-Side Logic:**
- Add UI components for displaying available quests, tracking quest progress, and claiming rewards.
- Ensure that the client-side code interacts correctly with the `QuestSystem` contract.
### Summary
By following this action plan, you will be able to implement enhanced inventory management, a player trading system, and a dynamic quests system using the framework described in your notes. Each feature involves updating the schema definitions, implementing new or modified Solidity contracts, and adding corresponding client-side logic to ensure a seamless user experience.