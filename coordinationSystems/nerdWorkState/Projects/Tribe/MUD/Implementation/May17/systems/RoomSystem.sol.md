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