

# Tables

`World` uses the [store tables](/store/tables), but adds [access control](/world/namespaces-access-control). For onchain tables, the data is stored by the `World` contract, [which is also a `StoreData` (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/world/src/World.sol#L43).

When a `System` reads or writes storage via [table libraries](/store/table-libraries), the request goes into [`StoreSwitch` (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/store/src/StoreSwitch.sol). This library decides which approach to use:

- If the `System` is in the root namespace, then it was called with [`delegatecall` (opens in a new tab)](https://docs.soliditylang.org/en/v0.8.22/introduction-to-smart-contracts.html#delegatecall-and-libraries). This means it inherits the `World` storage and can write directly to storage using [`StoreCore` (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/store/src/StoreCore.sol). These calls bypass access control.
    
- If the `System` is in any other namespace, then it was called with [`call` (opens in a new tab)](https://docs.soliditylang.org/en/v0.8.22/introduction-to-smart-contracts.html#message-calls) and has to call back into the `World` using [`IStore` (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/store/src/IStore.sol). These calls go through access control. They are only permitted if the `System` has access to the table in question. By default a `System` has access to its own namespace and therefore to all the tables inside it. Additional [access can be granted](/world/namespaces-access-control) by the namespace owner.
    

![Interaction between the World, a System, and a table](https://mud.dev/world/tables/_next/static/media/world-table.39df342f.svg)

1. An account calls a function called `game__myFunc` on the `World`. [This function was registered](/world/function-selectors) by the owner of the `game` namespace and points to the `myFunc` function in one of the `System`s in the `namespace` namespace.
    
2. The `World` verifies that access is permitted (for example, because `game:System` is publicly accessible) and if so calls `myFunc` on the `game:System` contract with the provided parameters.
    
3. At some point in its execution `myFunc` decides to update the data in the table `game:Items`. As with all other tables, this table is stored in the `World`'s storage. To modify it, `function` calls a function on the `World` contract.
    
4. The `World` verifies that access is permitted (by default it would be, because `game:System` has access to the `game` namespace). If so, it modifies the data in the `game:Items` table.
    

⚠️

Once a table is created its schema is _immutable_. If you need to add fields to an existing table, create a new table with the same key schema and the new fields in the value schema, and retrieve from both tables to get the complete value. If you need to delete fields, just have your code ignore them.

## Code samples[](#code-samples)

### Reading from a table[](#reading-from-a-table)

Anybody connected to the blockchain can run the `view` functions that read table content, provided they know which key to use (by default MUD does not keep a list of keys written to a table onchain, to save on storage operations).

All the functions to [read from a MUD store](/store/table-libraries#reading-data) are available. In this example we use the `Counter` table in the [vanilla](/templates/typescript/vanilla) template, which is a singleton so there is no key to worry about.

ReadCounter.s.sol

```
// SPDX-License-Identifier: MIT
pragma solidity >=0.8.24;
 
import { Script } from "forge-std/Script.sol";
import { console } from "forge-std/console.sol";
import { StoreSwitch } from "@latticexyz/store/src/StoreSwitch.sol";
import { Counter } from "../src/codegen/index.sol";
 
contract ReadCounter is Script {
  function run() external {
    address worldAddress = 0xC14fBdb7808D9e2a37c1a45b635C8C3fF64a1cc1;
    StoreSwitch.setStoreAddress(worldAddress);
    console.log("Counter value:", Counter.get());
  }
}
```

Explanation

```
import { StoreSwitch } from "@latticexyz/store/src/StoreSwitch.sol";
```

We need [the `StoreSwitch` library (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/store/src/StoreSwitch.sol) library to specify the address of the `World` with the data.

```
import { Counter } from "../src/codegen/index.sol";
```

It is easiest if we import the definitions of the table that were generated using [`mud tablegen`](/cli/tablegen).

```
    address worldAddress = 0xC14fBdb7808D9e2a37c1a45b635C8C3fF64a1cc1;
    StoreSwitch.setStoreAddress(worldAddress);
```

Use [`StoreSwitch` (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/store/src/StoreSwitch.sol) with the `World` address.

```
    console.log("Counter value:", Counter.get());
```

Read the information. If this had been a table with a key, we'd need to provide the key as a parameter to `<table name>.get()`.

### Writing to table[](#writing-to-table)

All the functions to [write to a MUD store](/store/table-libraries#writing-data) are available. In this example we reset `Counter` to zero. Note that only [authorized addresses](/world/namespaces-access-control#access) are allowed to write directly to a table.

ResetCounter.s.sol

```
// SPDX-License-Identifier: MIT
pragma solidity >=0.8.24;
 
import { Script } from "forge-std/Script.sol";
import { console } from "forge-std/console.sol";
import { StoreSwitch } from "@latticexyz/store/src/StoreSwitch.sol";
import { Counter } from "../src/codegen/index.sol";
 
contract ResetCounter is Script {
  function run() external {
    // Specify a store so that you can use tables directly
    address worldAddress = 0xC14fBdb7808D9e2a37c1a45b635C8C3fF64a1cc1;
    StoreSwitch.setStoreAddress(worldAddress);
 
    // Load the private key from the `PRIVATE_KEY` environment variable (in .env)
    uint256 deployerPrivateKey = vm.envUint("PRIVATE_KEY");
 
    // Start broadcasting transactions from the deployer account
    vm.startBroadcast(deployerPrivateKey);
    Counter.set(0);
    vm.stopBroadcast();
 
    console.log("Counter value:", Counter.get());
  }
}
```

Explanation

```
    Counter.set(0);
```

This is how you modify a table's value. If there was a key, it would be `<table name>.set(<key fields>,<value fields>)`.

Last updated on May 10, 2024

[Namespaces & Access Control](/world/namespaces-access-control "Namespaces & Access Control")[Systems](/world/systems "Systems")

---

MIT 2023 © MUD

Tables – MUD