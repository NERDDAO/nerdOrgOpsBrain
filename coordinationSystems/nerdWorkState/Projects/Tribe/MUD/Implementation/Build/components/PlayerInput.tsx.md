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