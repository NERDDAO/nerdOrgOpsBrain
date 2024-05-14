```ts
import { ReactNode, useEffect, useState } from "react";
import { useMUD } from "./MUDContext";
import { Entity } from "@latticexyz/recs";
import { twMerge } from "tailwind-merge";



export const GameMap = () => {
  const {
    network: { playerEntity },
  } = useMUD();
          return (
            <></>
            );
};
```