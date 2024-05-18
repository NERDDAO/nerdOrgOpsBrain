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