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