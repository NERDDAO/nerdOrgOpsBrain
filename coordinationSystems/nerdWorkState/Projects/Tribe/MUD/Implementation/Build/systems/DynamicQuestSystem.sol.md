```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import { System } from "@latticexyz/world/src/System.sol";
import { Inventory, Item, ItemData, Quest, QuestData } from "../codegen/index.sol";
import { addressToEntityKey } from "../addressToEntityKey.sol";

contract DynamicQuestSystem is System {
  uint32 public questCounter;

  event QuestCreated(uint questId, string questName, string questDescription, bytes32 rewardItemId);
  event QuestCompleted(bytes32 questId, address player);

  function createQuest(string memory questName, string memory questDescription, bytes32 rewardItemId) public {
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

  function _rewardItemToPlayer(bytes32 playerId, bytes32 rewardItemId) internal {
    Inventory.pushItemIds(playerId, rewardItemId);
    Item.set(rewardItemId, ItemData({ itemCount: 1, itemName: "Reward Item", description: "you earned it" })); // Presume reward item data available
  }
}

```

