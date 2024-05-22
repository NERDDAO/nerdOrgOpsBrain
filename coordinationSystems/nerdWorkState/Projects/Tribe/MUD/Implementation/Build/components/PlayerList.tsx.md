---
tags:
  - mud
---

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