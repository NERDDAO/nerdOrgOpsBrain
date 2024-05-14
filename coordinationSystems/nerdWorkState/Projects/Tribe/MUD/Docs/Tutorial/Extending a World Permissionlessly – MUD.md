

# Extending a World Permissionlessly

On this page you learn how to modify a `World` that is already deployed to the blockchain, without using a privileged address, such as the one that initially deployed it. If you want to learn how to modify a `World` before it is deployed, [see the hello world page](/hello-world).

## The sample program[](#the-sample-program)

To learn how to extend a `World`, we will extend the `Counter` example to allow users to leave a message while incrementing the counter.

To extend the `World` with the message functionality requires adding several resources:

- **Namespace**. A [namescape](/world/namespaces-access-control) can contain tables and systems. In most cases, the only way you would be able to extend a `World` that somebody else owns is to create your own namespace within that world.
    
- **Table**. A [table](/world/tables) to store the messages that have been posted.
    
- **System**. A [`System`](/world/systems) that updates the messages table and then calls `increment` to update the counter.
    

## Create the `World` to extend[](#create-the-world-to-extend)

To have a `World` with the `Counter` example to modify, go to a separate command line window and create a blockchain with a `World` using [the TypeScript template](/quickstart) and start the execution.

```
pnpm create mud extendMe --template vanilla
cd extendMe
pnpm dev
```

## Create the Solidity code[](#create-the-solidity-code)

The easiest way to create the Solidity code is to use the MUD template:

1. Create a new MUD application.
    
    ```
    pnpm create mud extension --template vanilla
    cd extension/packages/contracts
    ```
    
2. Edit `mud.config.ts` to include the definitions we need.
    
    mud.config.ts
    
    ```
    import { defineWorld } from "@latticexyz/world";
     
    export default defineWorld({
      namespace: "messaging",
      tables: {
        Messages: {
          schema: {
            counterValue: "uint32",
            message: "string",
          },
          key: ["counterValue"],
        },
      },
      systems: {
        MessageSystem: {
          name: "MessageSystem",
          openAccess: true,
        },
      },
    });
    ```
    
3. Create `src/systems/MessageSystem.sol`.
    
    MessageSystem.sol
    
    ```
    // SPDX-License-Identifier: MIT
    pragma solidity >=0.8.21;
     
    import { System } from "@latticexyz/world/src/System.sol";
    import { Messages } from "../codegen/index.sol";
     
    interface WorldWithIncrement {
      function increment() external returns (uint32);
    }
     
    contract MessageSystem is System {
      function incrementMessage(string memory message) public returns (uint32) {
        uint32 newVal = WorldWithIncrement(_world()).increment();
        Messages.set(newVal, message);
        return newVal;
      }
    }
    ```
    
    Explanation
    
    ```
    import { System } from "@latticexyz/world/src/System.sol";
    import { Messages } from "../codegen/index.sol";
    ```
    
    These are the two main things the `System` needs to know: how to be a `System` and how to access the `Messages` table.
    
    ```
    interface WorldWithIncrement {
      function increment() external returns (uint32);
    }
    ```
    
    This `System` needs to call `increment` on the `World` where it is implemented. However, as an extension author you might not have access to the source code of any `System` that isn't part of your extension.
    
    If you define your own interface for `World` you can add whatever function signatures are supported. Note that [an Ethereum function selector (opens in a new tab)](https://docs.soliditylang.org/en/latest/abi-spec.html#function-selector) is based on the function name and its parameter types, it does not include the return type. So if you are unsure of the return type that is not a huge problem.
    
    ```
    contract MessageSystem is System {
      function incrementMessage(string memory message) public returns (uint32) {
        uint32 newVal = WorldWithIncrement(_world()).increment();
    ```
    
    This is how we use the `WorldWithIncrement` interface we created. The [`_world()` (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/world/src/WorldContext.sol#L38-L44) call gives us the address of the `World` that called us. When we specify `WorldWithIncrement(<address>)`, we are telling Solidity that there is already a `WorldWithIncrement` at that address, and therefore we can use functions that are supported by `WorldWithIncrement`, such as `increment()`.
    
    ```
        Messages.set(newVal, message);
    ```
    
    This is one way to create a record with the key `newVal` and the value `message`.
    
    ```
        return newVal;
      }
    }
    ```
    
    When we are called by a user, an externally owned account, the return value is meaningless. However, just as we call `increment()` from a contract and use the return value (instead of having to read `Counter` ourselves), some future onchain code might call `incrementMessage` and use the returned value.
    
4. Remove files that are part of the template, but not useful in an extension.
    
    ```
    rm src/systems/IncrementSystem.sol test/*.t.sol script/PostDeploy.s.sol
    ```
    
5. Build and compile the Solidity code.
    
    ```
    pnpm build
    ```
    

## Deploy to the blockchain[](#deploy-to-the-blockchain)

1. Create a `.env` file with:
    
    - `PRIVATE_KEY` - the private key of an account that has ETH on the blockchain. Preferably, _not_ the one that deployed the `World`.
    - `WORLD_ADDRESS` - the address of the `World` to which you add the namespace.
    
    If you are using the template with a fresh `pnpm dev`, then you can use this `.env`:
    
    .env
    
    ```
    # Anvil default private key for the second account
    # (NOT the account that deployed the World)
    PRIVATE_KEY=0x59c6995e998f97a5a0044966f0945389dc9e86dae88c7a8412f4603b6b78690d
     
    # Address for the world we are extending
    WORLD_ADDRESS=0xc14fbdb7808d9e2a37c1a45b635c8c3ff64a1cc1
    ```
    
2. Create this script in `script/MessagingExtension.s.sol`.
    
    MessagingExtension.s.sol
    
    ```
    // SPDX-License-Identifier: MIT
    pragma solidity >=0.8.21;
     
    import { Script } from "forge-std/Script.sol";
    import { console } from "forge-std/console.sol";
    import { IBaseWorld } from "@latticexyz/world-modules/src/interfaces/IBaseWorld.sol";
     
    import { WorldRegistrationSystem } from "@latticexyz/world/src/modules/init/implementations/WorldRegistrationSystem.sol";
     
    // Create resource identifiers (for the namespace and system)
    import { ResourceId } from "@latticexyz/store/src/ResourceId.sol";
    import { WorldResourceIdLib } from "@latticexyz/world/src/WorldResourceId.sol";
    import { RESOURCE_SYSTEM } from "@latticexyz/world/src/worldResourceTypes.sol";
     
    // For registering the table
    import { Messages } from "../src/codegen/index.sol";
    import { IStore } from "@latticexyz/store/src/IStore.sol";
    import { StoreSwitch } from "@latticexyz/store/src/StoreSwitch.sol";
     
    // For deploying MessageSystem
    import { MessageSystem } from "../src/systems/MessageSystem.sol";
     
    contract MessagingExtension is Script {
      function run() external {
        uint256 deployerPrivateKey = vm.envUint("PRIVATE_KEY");
        address worldAddress = vm.envAddress("WORLD_ADDRESS");
     
        WorldRegistrationSystem world = WorldRegistrationSystem(worldAddress);
        ResourceId namespaceResource = WorldResourceIdLib.encodeNamespace(bytes14("messaging"));
        ResourceId systemResource = WorldResourceIdLib.encode(RESOURCE_SYSTEM, "messaging", "MessageSystem");
     
        vm.startBroadcast(deployerPrivateKey);
        world.registerNamespace(namespaceResource);
     
        StoreSwitch.setStoreAddress(worldAddress);
        Messages.register();
     
        MessageSystem messageSystem = new MessageSystem();
        console.log("MessageSystem address: ", address(messageSystem));
     
        world.registerSystem(systemResource, messageSystem, true);
        world.registerFunctionSelector(systemResource, "incrementMessage(string)");
     
        vm.stopBroadcast();
      }
    }
    ```
    
    Explanation
    
    ```
    // SPDX-License-Identifier: MIT
    pragma solidity >=0.8.21;
    ```
    
    Standard Solidity boilerplate.
    
    ```
    import { Script } from "forge-std/Script.sol";
    import { console } from "forge-std/console.sol";
    ```
    
    The definitions for [forge scripts (opens in a new tab)](https://book.getfoundry.sh/reference/forge/forge-script) and [the console (opens in a new tab)](https://book.getfoundry.sh/reference/forge-std/console-log).
    
    ```
    import { IBaseWorld } from "@latticexyz/world-modules/src/interfaces/IBaseWorld.sol";
    ```
    
    Use [IBaseWorld.sol (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/world-modules/src/interfaces/IBaseWorld.sol) to get definitions that are common to all `World` contracts.
    
    ```
    import { WorldRegistrationSystem } from "@latticexyz/world/src/modules/init/implementations/WorldRegistrationSystem.sol";
    ```
    
    [WorldRegistartionSystem.sol (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/world/src/modules/init/implementations/WorldRegistrationSystem.sol) contains the function definitions necessary to register new namespaces and systems with an existing `World`.
    
    ```
    // Create resource identifiers (for the namespace and system)
    import { ResourceId } from "@latticexyz/store/src/ResourceId.sol";
    import { WorldResourceIdLib } from "@latticexyz/world/src/WorldResourceId.sol";
    import { RESOURCE_SYSTEM } from "@latticexyz/world/src/worldResourceTypes.sol";
    ```
    
    These definitions make it easy to manage resource identifiers. We need them for the resource IDs we need to create: the namespace and the system.
    
    ```
    // For registering the table
    import { Messages, MessagesTableId } from "../src/codegen/index.sol";
    import { IStore } from "@latticexyz/store/src/IStore.sol";
    import { StoreSwitch } from "@latticexyz/store/src/StoreSwitch.sol";
    ```
    
    These are the definitions we need to register the `Messages` table.
    
    ```
    // For deploying MessageSystem
    import { MessageSystem } from "../src/systems/MessageSystem.sol";
    ```
    
    These are the definitions we need to deploy the `MessageSystem` contract so we'll then be able to register it as a `System` in the `World`.
    
    ```
    contract MessagingExtension is Script {
        function run() external {
    ```
    
    This is the function that implements the script.
    
    ```
            uint256 deployerPrivateKey = vm.envUint("PRIVATE_KEY");
            address worldAddress = vm.envAddress("WORLD_ADDRESS");
    ```
    
    Read the private key and the address of the `World` from the environment (which includes the content of the `.env` file).
    
    ```
            WorldRegistrationSystem world = WorldRegistrationSystem(worldAddress);
            ResourceId namespaceResource = WorldResourceIdLib.encodeNamespace(bytes14("messaging"));
            ResourceId systemResource = WorldResourceIdLib.encode(RESOURCE_SYSTEM, "messaging", "MessageSystem");
    ```
    
    We don't _have_ to name the system the same as the contract name, but it makes writing the client code later easier.
    
    Among other things, a MUD `World` is a `WorldRegistrationSystem`, so it has the appropriate functions. A `ResourceId` is a 32 byte value that uniquely identifies a resource in a MUD `World`. It is two bytes of resource type followed by 14 bytes of namespace and then 16 bytes of the name of the actual resource.
    
    Here we create two `ResourceId` values:
    
    |Name|Type|Namespace|Resource name|
    |---|---|---|---|
    |namespaceResource|`ns` (namespace)|messaging|Empty|
    |systemResource|`sy` (system)|messaging|MessageSystem|
    
    If you want to see these values, add these two lines to the script:
    
    ```
            console.log("Namespace ID: %x", uint256(ResourceId.unwrap(namespaceResource)));
            console.log("System ID:    %x", uint256(ResourceId.unwrap(systemResource)));
    ```
    
    Note that `console.log` requires a `uint256` value, and we can't get that directly from a `ResourceId`. Instead, we have to [unwrap (opens in a new tab)](https://docs.soliditylang.org/en/v0.8.20/types.html#user-defined-value-types) our `ResourceId` to get the original type (`bytes32`) and then [cast (opens in a new tab)](https://docs.soliditylang.org/en/v0.8.20/types.html#explicit-conversions) it to `uint256`.
    
    The expected values are:
    
    |Name|Expected value|
    |---|---|
    |Namespace ID|0x6e736d6573736167696e67000000000000000000000000000000000000000000|
    |System ID|0x73796d6573736167696e6700000000004d65737361676553797374656d000000|
    
    You can use [an online calculator (opens in a new tab)](https://www.duplichecker.com/hex-to-text.php) to verify the values are correct.
    
    |Hex value|ASCII|
    |--:|---|
    |6e736d6573736167696e67|nsmessaging|
    |73796d6573736167696e67|symessaging|
    |4d65737361676553797374656d|MessageSystem|
    
    ```
            vm.startBroadcast(deployerPrivateKey);
    ```
    
    Use the private key to submit transactions.
    
    ```
            world.registerNamespace(namespaceResource);
    ```
    
    [Register the namespace (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/world/src/modules/init/implementations/WorldRegistrationSystem.sol#L39-L63).
    
    ```
            StoreSwitch.setStoreAddress(worldAddress);
            Messages.register();
    ```
    
    Register the `Messages` table to `worldAddress`.
    
    ```
            MessageSystem messageSystem = new MessageSystem();
            world.registerSystem(systemResource, messageSystem, true);
    ```
    
    Deploy the new system and then register it with the `World` we are extending. The last parameter is whether or not we allow everybody to access this `System`.
    
    ```
            world.registerFunctionSelector(systemResource, "incrementMessage(string)");
    ```
    
    Register `MessageSystem.incrementMessage(string)`. This step is necessary to make the function accessible through the `World`. The function's name when accessed through the world is `<namespace>__<function>`, so this function will be available as `messaging__incrementMessage(string)`.
    
    **Note:** This is the case for all namespaces except for the root namespace, where we just use the name of the function (such as `increment()`).
    
    ```
            vm.stopBroadcast();
        }
    }
    ```
    
    Stop using the private key. Here this call is not necessary because we immediately leave the script, but it is a good idea to include it in case `MessagingExtension.run()` ever becomes part of a larger script.
    
3. Run the script. Note that you need to provide the URL in the command line, you can't rely on the `ETH_RPC_URL` environment variable.
    
    ```
    forge script script/MessagingExtension.s.sol --rpc-url http://localhost:8545 --broadcast
    ```
    
4. Increment and write a message.
    
    ```
    source .env
    cast send $WORLD_ADDRESS --rpc-url http://localhost:8545 --private-key $PRIVATE_KEY "messaging__incrementMessage(string)" "hello"
    ```
    
    When a function is _not_ in the root namespace, it is accessible as `<namespace>__<function name>` (as long as it is [registered](/world/function-selectors)).
    
    Here we call our `incrementMessage(string)` with the parameter `hello`.
    
5. You can see in the user interface of `extendMe` that the counter has been incremented. To see the message we sent, use these commands:
    
    ```
    TABLE_ID=0x74626d6573736167696e6700000000004d657373616765730000000000000000
    KEY=[`cast to-int256 2`]
    RAW_RESULT=`cast call $WORLD_ADDRESS --rpc-url http://localhost:8545 "getRecord(bytes32,bytes32[])" $TABLE_ID $KEY`
    cast --to-ascii ${RAW_RESULT:322:-2}
    ```
    
    Explanation
    
    ```
    TABLE_ID=0x74626d6573736167696e6700000000004d657373616765730000000000000000
    ```
    
    `TABLE_ID` is the `ResourceId` for the table, taken from `src/codegen/tables/Messages.sol` You can verify the interpretation [with the online calculator (opens in a new tab)](https://www.duplichecker.com/hex-to-text.php).
    
    |Type|Namespace|Resource name|
    |---|---|---|
    |`tb` (table)|messaging|Messages|
    
    ```
    KEY=[`cast to-int256 2`]
    ```
    
    The key to a MUD table is always an array of `byte32` values. To create that value, we convert our key (`2`, because that's the first user call to `increment()`, the 0->1 increment is done by the post deploy script) to a 256 bit value using [`cast` (opens in a new tab)](https://book.getfoundry.sh/reference/cast/cast-to-int256) and then envelop it in an array.
    
    ```
    RAW_RESULT=`cast call $WORLD_ADDRESS "getRecord(bytes32,bytes32[],bytes32)" $TABLE_ID $KEY $FIELD_LAYOUT`
    ```
    
    To call [`getRecord` (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/store/src/StoreRead.sol#L44-L57) we need the tableID, the key array, and the field layout. The call result is the entire value, which may have multiple static (fixed length) and dynamic (variable length) fields.
    
    Here is the raw result divided into 32 byte words.
    
    |Word|Value|
    |--:|---|
    |0|`0000000000000000000000000000000000000000000000000000000000000060`|
    |1|`0000000000000000000000000000000000000000000000000500000000000005`|
    |2|`0000000000000000000000000000000000000000000000000000000000000080`|
    |3|`0000000000000000000000000000000000000000000000000000000000000000`|
    |4|`0000000000000000000000000000000000000000000000000000000000000005`|
    |5|`68656c6c6f000000000000000000000000000000000000000000000000000000`|
    
    When there is a variable-length field (a field whose length is not known at compile-time) in a Solidity function's return value, it is represented by a word that tells us at what offset into the return data that field starts. This function is used for different tables with different lengths of static fields, so the static fields are a variable length field as far as Solidity is concerned.
    
    Word 0 shows us that this field's value starts at `0x60`, which is word 3. Because there are no static fields, word 3 is all zeros.
    
    Word 1 is a [`EncodedLengths` (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/store/src/EncodedLengths.sol) with the lengths of the dynamic fields. Here is the interpretation.
    
    |Bytes|Value|Meaning|
    |--:|--:|---|
    |6-0|`00000000000005`|The total length of all dynamic fields is five bytes.|
    |11-7|`0000000005`|The length of the first dynamic field is five bytes.|
    |16-12|`0000000000`|The length of the (non-existent in `Messages`) second dynamic field is zero|
    |21-17|`0000000000`|The length of the (non-existent in `Messages`) third dynamic field is zero|
    |26-22|`0000000000`|The length of the (non-existent in `Messages`) fourth dynamic field is zero|
    |31-27|`0000000000`|The length of the (non-existent in `Messages`) fifth dynamic field is zero|
    
    MUD tables can only have up to five dynamic fields because `EncodedLengths` needs to fit in a 32 byte word.
    
    Word 2 shows us that the field with the dynamic lengths starts at byte 0x80, which is word 4. Word 4 gives us the length of the string.
    
    Finally, word 5 gives us the actual message, "hello".
    
    ```
    cast --to-ascii ${RAW_RESULT:322:-2}
    ```
    
    Based on the above we don't need the first 322 characters of `$RAW_RESULT`. The first two characters are the `0x` that tells us this is a hexadecimal number. The next 320 characters are words 0-4, which are not the actual message (each word is 32 bytes, which is 64 hexadecimal digits). We also don't need the trailing newline. `${RAW_RESULT:322:-2}` removes those characters so we can use [`cast` (opens in a new tab)](https://book.getfoundry.sh/reference/cast/cast-to-ascii) to get the ASCII.
    

## Create an updated user interface[](#create-an-updated-user-interface)

Finally, we create a user interface that supports the messaging system.

### Run the user interface[](#run-the-user-interface)

For the user interface to run permissionlessly, it cannot require resources that that are not available because of the changes we made in `packages/contracts`. Here we modify the template UI.

1. Change to the client package of the extension.
    
    ```
    cd ../client
    ```
    
2. Port 3000 is already in use by the `extendMe` application, so edit `vite.config.ts` to use a different port.
    
    vite.config.ts
    
    ```
    import { defineConfig } from "vite";
     
    export default defineConfig({
      server: {
        port: 3001,
        fs: {
          strict: false,
        },
      },
      build: {
        target: "es2022",
        minify: true,
        sourcemap: true,
      },
    });
    ```
    
3. Start the user interface.
    
    ```
    pnpm vite
    ```
    
4. Open the application at [http://localhost:3001 (opens in a new tab)](http://localhost:3001). The client will throw errors because we didn't update the client code to work with the new contracts yet - we'll fix that in the next steps.
    
5. The network configuration does not have the correct `World` address. Get the `World` address from the application being extended. By default, you can see this value on the application at URL [http://localhost:3000 (opens in a new tab)](http://localhost:3000). Edit `../contracts/worlds.json` to set the `World` address for the chain ID (`31337`).
    
    Note: If you followed the directions above to create `extendMe`, the `World` address is `0xc14fbdb7808d9e2a37c1a45b635c8c3ff64a1cc1`.
    
    ```
    {
      "31337": {
        "address": "0xc14fbdb7808d9e2a37c1a45b635c8c3ff64a1cc1"
      }
    }
    ```
    

Note that while at this point you can access the user interface, the counter value is inaccurate and if you click **Increment** you get an error. We will fix these problems now.

### Increment and send a message[](#increment-and-send-a-message)

1. Edit `src/mud/createSystemCalls.ts` to use the new `messaging__incrementMessage` function instead of `increment`.
    
    createSystemCalls.ts
    
    ```
    /*
     * Create the system calls that the client can use to ask
     * for changes in the World state (using the System contracts).
     */
     
    import { getComponentValue } from "@latticexyz/recs";
    import { ClientComponents } from "./createClientComponents";
    import { SetupNetworkResult } from "./setupNetwork";
    import { singletonEntity } from "@latticexyz/store-sync/recs";
     
    export type SystemCalls = ReturnType<typeof createSystemCalls>;
     
    export function createSystemCalls(
      /*
       * The parameter list informs TypeScript that:
       *
       * - The first parameter is expected to be a
       *   SetupNetworkResult, as defined in setupNetwork.ts
       *
       *   Out of this parameter, we only care about two fields:
       *   - worldContract (which comes from getContract, see
       *     https://github.com/latticexyz/mud/blob/main/templates/vanilla/packages/client/src/mud/setupNetwork.ts#L63-L69).
       *
       *   - waitForTransaction (which comes from syncToRecs, see
       *     https://github.com/latticexyz/mud/blob/main/templates/vanilla/packages/client/src/mud/setupNetwork.ts#L77-L83).
       *
       * - From the second parameter, which is a ClientComponent,
       *   we only care about Counter. This parameter comes to use
       *   through createClientComponents.ts, but it originates in
       *   syncToRecs
       *   (https://github.com/latticexyz/mud/blob/main/templates/vanilla/packages/client/src/mud/setupNetwork.ts#L77-L83).
       */
      { worldContract, waitForTransaction }: SetupNetworkResult,
      { Counter }: ClientComponents,
    ) {
      const increment = async () => {
        /*
         * Because IncrementSystem
         * (https://mud.dev/templates/typescript/contracts#incrementsystemsol)
         * is in the root namespace, `.increment` can be called directly
         * on the World contract.
         */
        const tx = await worldContract.write.messaging__incrementMessage([`message at ${Date()}`]);
        await waitForTransaction(tx);
        return getComponentValue(Counter, singletonEntity);
      };
     
      return {
        increment,
      };
    }
    ```
    
2. We no longer have a `Counter` component. Modify `src/index.ts` to be able to run without it.
    
    index.ts
    
    ```
    import { setup } from "./mud/setup";
    import mudConfig from "contracts/mud.config";
     
    const {
      components,
      systemCalls: { increment },
      network,
    } = await setup();
     
    // Components expose a stream that triggers when the component is updated.
    components.Counter && components.Counter.update$.subscribe((update) => {
      const [nextValue, prevValue] = update.value;
      console.log("Counter updated", update, { nextValue, prevValue });
      document.getElementById("counter")!.innerHTML = String(nextValue?.value ?? "unset"$
    });
     
    // Attach the increment function to the html element with ID `incrementButton` (if i$
    document.querySelector("#incrementButton")?.addEventListener("click", increment);
     
    // https://vitejs.dev/guide/env-and-mode.html
    if (import.meta.env.DEV) {
      const { mount: mountDevTools } = await import("@latticexyz/dev-tools");
      mountDevTools({
        config: mudConfig,
        publicClient: network.publicClient,
        walletClient: network.walletClient,
        latestBlock$: network.latestBlock$,
        storedBlockLogs$: network.storedBlockLogs$,
        worldAddress: network.worldContract.address,
        worldAbi: network.worldContract.abi,
        write$: network.write$,
        recsWorld: network.world,
      });
    }
    ```
    
3. Now you can click **Increment** in the user interface and create messages. The user interface does not display those messages, but you can open the **MUD Dev Tools**, click the **Components** tab, and select **messagings__Messages** to view them.
    
    ![The extended UI with the messages shown in the MUD Dev Tools](https://mud.dev/guides/extending-a-world/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fextend-ui-with-msgs.27b00a60.png&w=3840&q=75)
    

### Read the counter[](#read-the-counter)

We still don't have the `Counter` value available, because the definition in `mud.config.ts` is in the `messaging` namespace, not the root one. We will now fix this.

1. Add the `@latticexyz/store` package.
    
    ```
    pnpm add @latticexyz/store
    ```
    
2. Copy the `mud.config.ts` from the application being extended (`extendMe`) to `src/mud/counter.config.ts`.
    
    counter.config.ts
    
    ```
    import { defineWorld } from "@latticexyz/world";
     
    export default defineWorld({
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
    
    Here we are using the definition from the original application for convenience, but we could have used [the schema](/store/encoding#schema) which is available in `store__Tables` to get all the required information to rebuild this file.
    
3. Edit `src/mud/setupNetwork.ts` to add a `tables` parameter with the `Counter` component's tables to the `syncToRecs` call.
    
    setupNetwork.ts
    
    ```
    /*
     * The MUD client code is built on top of viem
     * (https://viem.sh/docs/getting-started.html).
     * This line imports the functions we need from it.
     */
    import {
      createPublicClient,
      fallback,
      webSocket,
      http,
      createWalletClient,
      Hex,
      parseEther,
      ClientConfig,
      getContract,
    } from "viem";
    import { createFaucetService } from "@latticexyz/services/faucet";
    import { encodeEntity, syncToRecs } from "@latticexyz/store-sync/recs";
     
    import { getNetworkConfig } from "./getNetworkConfig";
    import { world } from "./world";
    import IWorldAbi from "contracts/out/IWorld.sol/IWorld.abi.json";
    import { createBurnerAccount, transportObserver, ContractWrite } from "@latticexyz/common";
    import { transactionQueue, writeObserver } from "@latticexyz/common/actions";
     
    import { resolveConfig } from "@latticexyz/store/internal";
    import { storeToV1 } from "@latticexyz/store/config/v2";
     
    import { Subject, share } from "rxjs";
     
    /*
     * Import our MUD config, which includes strong types for
     * our tables and other config options. We use this to generate
     * things like RECS components and get back strong types for them.
     *
     * See https://mud.dev/templates/typescript/contracts#mudconfigts
     * for the source of this information.
     */
    import mudConfig from "contracts/mud.config";
    import counterConfig from "./counter.config";
     
    export type SetupNetworkResult = Awaited<ReturnType<typeof setupNetwork>>;
     
    export async function setupNetwork() {
      const networkConfig = await getNetworkConfig();
     
      /*
       * Create a viem public (read only) client
       * (https://viem.sh/docs/clients/public.html)
       */
      const clientOptions = {
        chain: networkConfig.chain,
        transport: transportObserver(fallback([webSocket(), http()])),
        pollingInterval: 1000,
      } as const satisfies ClientConfig;
     
      const publicClient = createPublicClient(clientOptions);
     
      /*
       * Create an observable for contract writes that we can
       * pass into MUD dev tools for transaction observability.
       */
      const write$ = new Subject<ContractWrite>();
     
      /*
       * Create a temporary wallet and a viem client for it
       * (see https://viem.sh/docs/clients/wallet.html).
       */
      const burnerAccount = createBurnerAccount(networkConfig.privateKey as Hex);
      const burnerWalletClient = createWalletClient({
        ...clientOptions,
        account: burnerAccount,
      })
        .extend(transactionQueue())
        .extend(writeObserver({ onWrite: (write) => write$.next(write) }));
     
      /*
       * Create an object for communicating with the deployed World.
       */
      const worldContract = getContract({
        address: networkConfig.worldAddress as Hex,
        abi: IWorldAbi,
        client: { public: publicClient, wallet: burnerWalletClient },
      });
     
      /*
       * Sync on-chain state into RECS and keeps our client in sync.
       * Uses the MUD indexer if available, otherwise falls back
       * to the viem publicClient to make RPC calls to fetch MUD
       * events from the chain.
       */
      const { components, latestBlock$, storedBlockLogs$, waitForTransaction } = await syncToRecs({
        world,
        config: mudConfig,
        address: networkConfig.worldAddress as Hex,
        publicClient,
        tables: resolveConfig(storeToV1(counterConfig)).tables,
        startBlock: BigInt(networkConfig.initialBlockNumber),
      });
     
      /*
       * If there is a faucet, request (test) ETH if you have
       * less than 1 ETH. Repeat every 20 seconds to ensure you don't
       * run out.
       */
      if (networkConfig.faucetServiceUrl) {
        const address = burnerAccount.address;
        console.info("[Dev Faucet]: Player address -> ", address);
     
        const faucet = createFaucetService(networkConfig.faucetServiceUrl);
     
        const requestDrip = async () => {
          const balance = await publicClient.getBalance({ address });
          console.info(`[Dev Faucet]: Player balance -> ${balance}`);
          const lowBalance = balance < parseEther("1");
          if (lowBalance) {
            console.info("[Dev Faucet]: Balance is low, dripping funds to player");
            // Double drip
            await faucet.dripDev({ address });
            await faucet.dripDev({ address });
          }
        };
     
        requestDrip();
        // Request a drip every 20 seconds
        setInterval(requestDrip, 20000);
      }
     
      return {
        world,
        components,
        playerEntity: encodeEntity({ address: "address" }, { address: burnerWalletClient.account.address }),
        publicClient,
        walletClient: burnerWalletClient,
        latestBlock$,
        storedBlockLogs$,
        waitForTransaction,
        worldContract,
        write$: write$.asObservable().pipe(share()),
      };
    }
    ```
    

Now you can click **Increment** and see the update. Also, you can look in **Components > Counter** and see the counter value.

[Deploy to a blockchain](/guides/extending-a-world# "Deploy to a blockchain")[Adding Delegation](/guides/adding-delegation "Adding Delegation")

---

MIT 2023 © MUD

Extending a World Permissionlessly – MUD