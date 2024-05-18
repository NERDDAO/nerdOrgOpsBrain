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