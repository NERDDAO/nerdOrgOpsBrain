```tsx
import React, { useState } from "react";
import { SystemCalls } from "./mud/mud/createSystemCalls";

interface Props {
  systemCalls: SystemCalls;
}

export const RoomComponent: React.FC<Props> = ({ systemCalls }) => {
  const [roomId, setRoomId] = useState("");
  const [roomName, setRoomName] = useState("");
  const [roomDescription, setRoomDescription] = useState("");
  const [exitRoomId, setExitRoomId] = useState("");
  const [direction, setDirection] = useState("");
  const [targetRoomId, setTargetRoomId] = useState("");

  const handleCreateRoom = async () => {
    if (!roomId || !roomName || !roomDescription) {
      alert("All fields are required to create a room");
      return;
    }
    await systemCalls.createRoom(roomId, roomName, roomDescription);
  };

  const handleCreateExit = async () => {
    if (!exitRoomId || !direction || !targetRoomId) {
      alert("All fields are required to create an exit");
      return;
    }
    await systemCalls.createExit(exitRoomId, direction, targetRoomId);
  };

  return (
    <div className="form-control">
      <h2>Create Room</h2>
      <input
        className="input"
        type="text"
        placeholder="Room ID"
        value={roomId}
        onChange={e => setRoomId(e.target.value)}
      />
      <input
        className="input"
        type="text"
        placeholder="Room Name"
        value={roomName}
        onChange={e => setRoomName(e.target.value)}
      />
      <input
        className="input"
        type="text"
        placeholder="Room Description"
        value={roomDescription}
        onChange={e => setRoomDescription(e.target.value)}
      />
      <button className="btn" onClick={handleCreateRoom}>
        Create Room
      </button>

      <h2 className="label">Create Exit</h2>
      <input
        className="input"
        type="text"
        placeholder="Room ID"
        value={exitRoomId}
        onChange={e => setExitRoomId(e.target.value)}
      />
      <input
        className="input"
        type="text"
        placeholder="Direction"
        value={direction}
        onChange={e => setDirection(e.target.value)}
      />
      <input
        className="input"
        type="text"
        placeholder="Target Room ID"
        value={targetRoomId}
        onChange={e => setTargetRoomId(e.target.value)}
      />
      <button className="btn" onClick={handleCreateExit}>
        Create Exit
      </button>
    </div>
  );
};
```