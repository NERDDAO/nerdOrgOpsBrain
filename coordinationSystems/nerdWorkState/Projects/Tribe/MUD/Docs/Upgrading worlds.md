

Upgrading

# Upgrading worlds

The [`System`s](/world/systems) can be upgraded without changing the underlying `World` code, using [`mud deploy --worldAddress`](/cli/deploy#upgrading-a-world). However, you can also upgrade the `World` contract itself if the `World` was deployed [behind a proxy](/config#upgradeableWorldImplementation). This allows you to upgrade to a future version of MUD, but adds some gas overhead for all calls (due to one more level of indirection).

## Making an upgradeable `World`[](#making-an-upgradeable-world)

To make a `World` upgradeable, edit the [`mud.config.ts`](/config) file and set `deploy.upgradeableWorldImplementation` to `true`.

mud.config.ts

```
import { defineWorld } from "@latticexyz/world";
 
export default defineWorld({
  deploy: {
    upgradeableWorldImplementation: true,
  },
  tables: {
    Counter: {
      schema: {
        value: "uint32",
      },
      key: [],
    },
  },
});
```

## Testing if upgrades are possible[](#testing-if-upgrades-are-possible)

As per [ERC-1967 (opens in a new tab)](https://eips.ethereum.org/EIPS/eip-1967#logic-contract-address), the storage slot `0x360894a13ba1a3210667c828492db98dca3e2076cc3735a920a3ca505d382bbc` stores the address of the contract implementation in a proxy. So to identify if a `World` is deployed behind a proxy run these commands:

```
WORLD_ADDRESS=0xbe6b85dc88f969e45d8d8ae128c5a9c9744d6464
cast implementation $WORLD_ADDRESS
```

If the answer is anything other than zero, the `World` is behind a proxy and can be upgraded.

## Performing an upgrade[](#performing-an-upgrade)

To upgrade the `World` use these steps in a `package/contracts` directory:

1. Edit `.env` to add `WORLD_ADDRESS` with the address displayed by MUD Dev Tools. For example, you might want to use this file:
    
    .env
    
    ```
    # This .env file is for demonstration purposes only.
    #
    # This should usually be excluded via .gitignore and the env vars attached to
    # your deployment environment, but we're including this here for ease of local
    # development. Please do not commit changes to this file!
    #
    # Enable debug logs for MUD CLI
    DEBUG=mud:*
    #
    # Anvil default private key:
    PRIVATE_KEY=0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80
    WORLD_ADDRESS=0xbe6b85dc88f969e45d8d8ae128c5a9c9744d6464
    ```
    
2. Create this script in `scripts/UpgradeWorld.s.sol`.
    
    UpgradeWorld.s.sol
    
    ```
    // SPDX-License-Identifier: MIT
    pragma solidity >=0.8.24;
     
    import { Script } from "forge-std/Script.sol";
    import { console } from "forge-std/console.sol";
     
    import { World } from "@latticexyz/world/src/World.sol";
    import { WorldProxy } from "@latticexyz/world/src/WorldProxy.sol";
     
    contract SetImplementation is Script {
      function run() external {
        uint256 deployerPrivateKey = vm.envUint("PRIVATE_KEY");
        vm.startBroadcast(deployerPrivateKey);
     
        address proxyAddress = vm.envAddress("WORLD_ADDRESS");
     
        // Deploy new world implementation
        World newWorld = new World();
     
        console.log("proxy:", proxyAddress);
        console.log("new World:", address(newWorld));
     
        WorldProxy(payable(proxyAddress)).setImplementation(address(newWorld));
     
        vm.stopBroadcast();
      }
    }
    ```
    
3. Run the script.
    
    ```
    forge script script/UpgradeWorld.s.sol --rpc-url http://localhost:8545 --broadcast
    ```
    
4. See the new address.
    
    ```
    source .env
    cast implementation $WORLD_ADDRESS
    ```
    

[Batch Calls](/world/batch-calls "Batch Calls")[Modules](/world/modules "Modules")

---

MIT 2023 © MUD

Upgrading worlds – MUD