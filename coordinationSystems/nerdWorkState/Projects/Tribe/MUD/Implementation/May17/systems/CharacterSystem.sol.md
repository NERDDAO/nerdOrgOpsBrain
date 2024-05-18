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