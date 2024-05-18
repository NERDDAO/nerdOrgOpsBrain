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