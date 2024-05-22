```ts
import React from "react";
import PlayerInput from "../PlayerInput";
import PlayerList from "../PlayerList";
import { QuestComponent } from "../QuestComponent";
import { TradingComponent } from "../TradingComponent";
import { RoomComponent } from "../RoomComponent";
import { ItemComponent } from "../ItemComponent";
import { CharacterComponent } from "../CharacterComponent";
import { useMUD } from "./MUDContext";

const GameBoard = () => {
  const {
    network: { useStore, tables },
  } = useMUD();

  const systemCalls = useMUD().systemCalls;

  const records = useStore(state => Object.values(state.getRecords(tables.Player)));

  return (
    <div className="container flex flex-row mx-auto w-full h-full">
      <PlayerInput />
      <PlayerList players={records} />
      <QuestComponent systemCalls={systemCalls} />
      <TradingComponent systemCalls={systemCalls} />
      <RoomComponent systemCalls={systemCalls} />
      <ItemComponent systemCalls={systemCalls} />
      <CharacterComponent systemCalls={systemCalls} />
    </div>
  );
};

export default GameBoard;
```

```ts
import React from "react";
import { useMUD } from "./mud/MUDContext";
import { getComponentValue } from "@latticexyz/recs";

const InventoryDisplay = ({ entityId }) => {
  const {
    network: { playerEntity, useStore, tables },
  } = useMUD();

  const inv = useStore(state => Object.values(state.getRecords(tables.Inventory)));
  const itemList = useStore(state => Object.values(state.getRecords(tables.Item)));
  console.log("All Inv:", inv, itemList); // Log all lore objects
  console.log("Entity ID:", entityId); // Log the entityId being searched for

  const myInv = inv.find(item => item.key.ownerId === entityId);

  console.log("Found Inv:", myInv); // Log the found lore object

  if (myInv.value.itemIds.length == 0) return <div>No Items available for this entity.</div>;

  return (
    <div className="p-5 bg-gray-100 rounded-lg shadow-md">
      <h2 className="mb-3 text-xl font-bold text-gray-800">Inventory:</h2>
      <ul>
        {myInv.value.itemIds?.map((item, index) => (
          <li key={index} className="p-2">
            <strong>Name:</strong> {itemList[0]?.value.itemName} (<strong>Count:</strong>{" "}
            {itemList[0]?.value.itemCount.toString()} )
          </li>
        ))}
      </ul>
    </div>
  );
};

export default InventoryDisplay;
```

```ts
import React from "react";
import { useMUD } from "./mud/MUDContext";

const LoreDisplay = ({ entityId }) => {
  const {
    network: { playerEntity, useStore, tables },
  } = useMUD();

  const lore = useStore(state => Object.values(state.getRecords(tables.Lore)));
  console.log("All Lore:", lore); // Log all lore objects
  console.log("Entity ID:", entityId); // Log the entityId being searched for

  const myLore = lore.find(item => item.key.id === entityId);
  console.log("Found Lore:", myLore); // Log the found lore object

  if (!myLore) return <div>No lore available for this entity.</div>;

  return (
    <div className="p-5 bg-gray-100 rounded-lg shadow-md">
      <h2 className="mb-5 text-2xl font-bold text-gray-800">Lore:</h2>
      <p>
        <strong>Alignment:</strong> {myLore.value.alignment}
      </p>
      <p>
        <strong>Backstory:</strong> {myLore.value.backstory}
      </p>
      <p>
        <strong>Current Quest:</strong> {myLore.value.currentQuest}
      </p>
    </div>
  );
};

export default LoreDisplay;
```

```ts
import React, { useState } from "react";
import { useMUD } from "./mud/MUDContext";
import { v4 as uuid } from "uuid";
import { usePlayerStore } from "~~/services/store/playerStore";

const PlayerInput = () => {
  const playerName = usePlayerStore(state => state.playerName);
  const setPlayerName = usePlayerStore(state => state.setPlayerName);
  const { lore, setLore } = usePlayerStore();

  // State for item management
  const [itemName, setItemName] = useState("");
  const [itemCount, setItemCount] = useState(1);

  const { spawn, addItemToInventory, removeItemFromInventory } = useMUD().systemCalls;

  const handleAddItem = () => {
    const itemId = uuid();
    addItemToInventory(itemId);
    setItemName("");
    setItemCount(1);
  };

  return (
    <div className="p-2 form-control">
      <h2 className="mb-5 text-2xl font-bold text-gray-800">Edit Lore:</h2>
      <div>
        <label className="label">Player Name</label>
        <input
          className="input"
          type="text"
          value={playerName}
          onChange={e => setPlayerName(e.target.value)}
          placeholder="Enter player name"
        />
      </div>
      <div>
        <label className="label">Alignment:</label>
        <input
          className="input"
          value={lore.alignment}
          onChange={e => setLore({ ...lore, alignment: e.target.value })}
        />
      </div>
      <div>
        <label className="label">Backstory:</label>
        <textarea
          className="input"
          value={lore.backstory}
          onChange={e => setLore({ ...lore, backstory: e.target.value })}
        />
      </div>
      <div>
        <label className="label">Current Quest:</label>
        <input
          className="input"
          value={lore.currentQuest}
          onChange={e => setLore({ ...lore, currentQuest: e.target.value })}
        />
      </div>

      <button className="btn" onClick={spawn}>
        Spawn
      </button>

      <h2 className="mt-5 mb-5 text-2xl font-bold text-gray-800">Manage Items:</h2>
      <div>
        <label className="label">Item Name:</label>
        <input className="input" type="text" value={itemName} onChange={e => setItemName(e.target.value)} />
      </div>
      <div>
        <label className="label">Item Count:</label>
        <input
          className="input"
          type="number"
          value={itemCount}
          onChange={e => setItemCount(Number(e.target.value))}
          min={1}
        />
      </div>
      <button className="btn" onClick={handleAddItem}>
        Add Item
      </button>
    </div>
  );
};

export default PlayerInput;

```

```ts
import React from "react";
import Image from "next/image";
import InventoryDisplay from "./InventoryDisplay";
import LoreDisplay from "./LoreDisplay";
import { pixelArt } from "@dicebear/collection";
import { createAvatar } from "@dicebear/core";

const PlayerList = props => {
  const { players } = props;
  return (
    <div className="p-5 bg-gray-100 rounded-lg shadow-md overflow-scroll max-h-svh flex justify-items-end">
      <h2 className="mb-5 text-2xl font-bold text-gray-800">Players:</h2>
      <ul>
        {players.map(player => {
          const avatarSvg = createAvatar(pixelArt, {
            seed: player.value.name,
            dataUri: true,
          });
          return (
            <li key={player.id} className="flex p-4 mb-4 bg-white rounded-lg shadow-sm hover:bg-gray-50">
              <div className="flex-1">
                <h3 className="text-xl font-semibold text-blue-600">{player.value.name}</h3>
                <p className="mt-2 text-gray-700">
                  <strong>Coordinates:</strong> (x: {player.value.x}, y: {player.value.y})
                </p>
                <p className="mt-1 text-gray-700">
                  <strong>Health:</strong> {player.value.health}
                </p>
                <LoreDisplay entityId={player.key.id} />
                <InventoryDisplay entityId={player.key.id} />
              </div>
              <div className="ml-4">
                <Image
                  className="w-12 h-12 rounded-full"
                  src={avatarSvg.toDataUriSync()}
                  alt={`${player.value.name}'s avatar`}
                  width={48}
                  height={48}
                />
              </div>
            </li>
          );
        })}
      </ul>
    </div>
  );
};


```

```ts
import React, { useState } from "react";
import { SystemCalls } from "./mud/mud/createSystemCalls";

interface Props {
  systemCalls: SystemCalls;
}

export const QuestComponent: React.FC<Props> = ({ systemCalls }) => {
  const [name, setName] = useState("");
  const [description, setDescription] = useState("");
  const [reward, setReward] = useState<number>();
  const [completeQuestId, setCompleteQuestId] = useState("");

  const handleCreateQuest = async () => {
    if (!name || !reward) {
      alert("Name and reward are required");
      return;
    }
    await systemCalls.createQuest(name, description, reward);
  };

  const handleCompleteQuest = async () => {
    if (!completeQuestId) {
      alert("Quest ID is required");
      return;
    }
    await systemCalls.completeQuest(completeQuestId);
  };

  return (
    <div className="form-control">
      <h2>Create Quest</h2>
      <input
        className="input"
        type="text"
        placeholder="Quest Name"
        value={name}
        onChange={e => setName(e.target.value)}
      />
      <input
        className="input"
        type="text"
        placeholder="Description"
        value={description}
        onChange={e => setDescription(e.target.value)}
      />
      <input
        className="input"
        type="number"
        placeholder="Reward"
        value={reward}
        onChange={e => setReward(Number(e.target.value))}
      />
      <button className="btn" onClick={handleCreateQuest}>
        Create Quest
      </button>

      <h2 className="label">Complete Quest</h2>
      <input
        className="input"
        type="text"
        placeholder="Quest ID"
        value={completeQuestId}
        onChange={e => setCompleteQuestId(e.target.value)}
      />
      <button className="btn" onClick={handleCompleteQuest}>
        Complete Quest
      </button>
    </div>
  );
};

```

```ts
import React, { useState } from "react";
import { SystemCalls } from "./mud/mud/createSystemCalls";

interface Props {
  systemCalls: SystemCalls;
}

export const TradingComponent: React.FC<Props> = ({ systemCalls }) => {
  const [from, setFrom] = useState("");
  const [to, setTo] = useState("");
  const [itemId, setItemId] = useState("");

  const handleTradeItem = async () => {
    if (!from || !to || !itemId) {
      alert("All fields are required");
      return;
    }
    await systemCalls.tradeItem(from, to, itemId);
  };

  return (
    <div>
      <h2>Trade Item</h2>
      <input
        className="input"
        type="text"
        placeholder="From user ID"
        value={from}
        onChange={e => setFrom(e.target.value)}
      />
      <input className="input" type="text" placeholder="To user ID" value={to} onChange={e => setTo(e.target.value)} />
      <input
        className="input"
        type="text"
        placeholder="Item ID"
        value={itemId}
        onChange={e => setItemId(e.target.value)}
      />
      <button className="btn" onClick={handleTradeItem}>
        Trade Item
      </button>
    </div>
  );
};
```

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

```tsx
import React, { useState } from "react";
import { SystemCalls } from "./mud/mud/createSystemCalls";

interface Props {
  systemCalls: SystemCalls;
}

export const ItemComponent: React.FC<Props> = ({ systemCalls }) => {
  const [itemId, setItemId] = useState("");
  const [itemName, setItemName] = useState("");
  const [itemDescription, setItemDescription] = useState("");
  const [itemCount, setItemCount] = useState<number>(1);

  const handleCreateItem = async () => {
    if (!itemId || !itemName || !itemDescription || itemCount <= 0) {
      alert("All fields are required to create an item");
      return;
    }
    await systemCalls.createItem(itemId, itemName, itemDescription, itemCount);
  };

  return (
    <div className="form-control">
      <h2>Create Item</h2>
      <input
        className="input"
        type="text"
        placeholder="Item ID"
        value={itemId}
        onChange={e => setItemId(e.target.value)}
      />
      <input
        className="input"
        type="text"
        placeholder="Item Name"
        value={itemName}
        onChange={e => setItemName(e.target.value)}
      />
      <input
        className="input"
        type="text"
        placeholder="Item Description"
        value={itemDescription}
        onChange={e => setItemDescription(e.target.value)}
      />
      <input
        className="input"
        type="number"
        placeholder="Item Count"
        value={itemCount}
        onChange={e => setItemCount(Number(e.target.value))}
        min={1}
      />
      <button className="btn" onClick={handleCreateItem}>
        Create Item
      </button>
    </div>
  );
};
```

```tsx
import React, { useState } from "react";
import { SystemCalls } from "./mud/mud/createSystemCalls";

interface Props {
  systemCalls: SystemCalls;
}

export const CharacterComponent: React.FC<Props> = ({ systemCalls }) => {
  const [characterId, setCharacterId] = useState("");
  const [characterName, setCharacterName] = useState("");
  const [characterDescription, setCharacterDescription] = useState("");

  const handleCreateCharacter = async () => {
    if (!characterId || !characterName || !characterDescription) {
      alert("All fields are required to create a character");
      return;
    }
    await systemCalls.createCharacter(characterId, characterName, characterDescription);
  };

  return (
    <div className="form-control">
      <h2>Create Character</h2>
      <input
        className="input"
        type="text"
        placeholder="Character ID"
        value={characterId}
        onChange={e => setCharacterId(e.target.value)}
      />
      <input
        className="input"
        type="text"
        placeholder="Character Name"
        value={characterName}
        onChange={e => setCharacterName(e.target.value)}
      />
      <input
        className="input"
        type="text"
        placeholder="Character Description"
        value={characterDescription}
        onChange={e => setCharacterDescription(e.target.value)}
      />
      <button className="btn" onClick={handleCreateCharacter}>
        Create Character
      </button>
    </div>
  );
};
```