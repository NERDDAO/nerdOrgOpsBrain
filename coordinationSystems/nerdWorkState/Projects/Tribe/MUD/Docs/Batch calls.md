

# Batch calls

Batch calls allow a single call to the `World` to perform multiple `System` calls. Using [`batchCall` (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/world/src/modules/init/implementations/BatchCallSystem.sol#L15-L35) you can issue calls on your own behalf. If you have the proper [delegations](/world/account-delegation), you can also use [`batchCallFrom` (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/world/src/modules/init/implementations/BatchCallSystem.sol#L37-L57) to issue `System` calls on behalf of other addresses.

The calls take place sequentially, and after they all finish successfully you get back the return values from all of them. If any of the calls revert, so does `batchCall` / `batchCallFrom`, so all the state changes created by the previous calls are discarded.

The advantage of using `batchCall` rather than [the standard multicall (opens in a new tab)](https://github.com/mds1/multicall) is that `batchCall` is a native MUD function that lives in the `World` contract, so it does not change `msg.sender`. As a result, when you use `batchCall` MUD can apply [access control](/world/namespaces-access-control), and `System`s can rely on the value of [`_msgSender()`](/world/systems#writing-systems).

## Batch calls from Solidity[](#batch-calls-from-solidity)

BatchCall.s.sol

```
// SPDX-License-Identifier: MIT
pragma solidity >=0.8.24;
 
import { Script } from "forge-std/Script.sol";
import { console } from "forge-std/console.sol";
import { StoreSwitch } from "@latticexyz/store/src/StoreSwitch.sol";
 
import { IWorld } from "../src/codegen/world/IWorld.sol";
import { Tasks, TasksData } from "../src/codegen/index.sol";
 
// We need to create the ResourceID for the System we are calling
import { ResourceId, WorldResourceIdLib, WorldResourceIdInstance } from "@latticexyz/world/src/WorldResourceId.sol";
import { RESOURCE_SYSTEM } from "@latticexyz/world/src/worldResourceTypes.sol";
 
import { SystemCallData } from "@latticexyz/world/src/modules/init/types.sol";
 
contract BatchCall is Script {
  function run() external {
    address worldAddress = 0xC14fBdb7808D9e2a37c1a45b635C8C3fF64a1cc1;
 
    // Load the private key from the `PRIVATE_KEY` environment variable (in .env)
    uint256 deployerPrivateKey = vm.envUint("PRIVATE_KEY");
 
    // Start broadcasting transactions from the deployer account
    vm.startBroadcast(deployerPrivateKey);
 
    ResourceId taskSystemId = WorldResourceIdLib.encode({
      typeId: RESOURCE_SYSTEM,
      namespace: "",
      name: "TasksSystem"
    });
 
    string[5] memory taskDescription = ["First task", "Second task", "Third task", "Fourth task", "Fifth task"];
 
    SystemCallData[] memory calls = new SystemCallData[](taskDescription.length);
    for (uint i = 0; i < taskDescription.length; i++) {
      calls[i].systemId = taskSystemId;
      calls[i].callData = abi.encodeWithSignature("addTask(string)", taskDescription[i]);
    }
 
    bytes[] memory returnData = IWorld(worldAddress).batchCall(calls);
 
    console.log("The return value is:");
    console.logBytes(returnData[0]);
 
    vm.stopBroadcast();
  }
}
```

Explanation

```
import { SystemCallData } from "@latticexyz/world/src/modules/init/types.sol";
```

This structure holds the data for each call.

```
.
.
.
    SystemCallData[] memory calls = new SystemCallData[](taskDescription.length);
    for (uint i=0; i<taskDescription.length; i++) {
      calls[i].systemId = taskSystemId;
      calls[i].callData = abi.encodeWithSignature("addTask(string)", taskDescription[i]);
    }
```

Create an array of `SystemCallData`, one for each call. Every item in this array has two fields:

- `systemId`: The resource ID for the `System` to be called.
- `callData`: The calldata to send this `System`, the [function selector (opens in a new tab)](https://docs.soliditylang.org/en/v0.8.24/abi-spec.html#function-selector) followed by the [function arguments (opens in a new tab)](https://docs.soliditylang.org/en/v0.8.24/abi-spec.html#argument-encoding).

```
    bytes[] memory returnData = IWorld(worldAddress).batchCall(calls);
```

Call [`batchCall` (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/world/src/modules/init/implementations/BatchCallSystem.sol#L15-L35). The results are returned as an array by `bytes`, one for each call.

## Batch calls from TypeScript[](#batch-calls-from-typescript)

The ideal place for these calls is [`createSystemCalls.ts` (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/templates/react/packages/client/src/mud/createSystemCalls.ts), where all the other system calls are located.

createSystemCalls.ts

```
/*
 * Create the system calls that the client can use to ask
 * for changes in the World state (using the System contracts).
 */
 
import { Hex } from "viem";
import { SetupNetworkResult } from "./setupNetwork";
 
import { resourceToHex } from "@latticexyz/common";
import { encodeFunctionData } from "viem";
 
export type SystemCalls = ReturnType<typeof createSystemCalls>;
 
export function createSystemCalls({ tables, useStore, worldContract, waitForTransaction }: SetupNetworkResult) {
  const addTask = async (label: string) => {
    const tx = await worldContract.write.addTask([label]);
    await waitForTransaction(tx);
  };
 
  const toggleTask = async (key: Hex) => {
    const isComplete = (useStore.getState().getValue(tables.Tasks, { key })?.completedAt ?? 0n) > 0n;
    const tx = isComplete ? await worldContract.write.resetTask([key]) : await worldContract.write.completeTask([key]);
    await waitForTransaction(tx);
  };
 
  const deleteTask = async (key: Hex) => {
    const tx = await worldContract.write.deleteTask([key]);
    await waitForTransaction(tx);
  };
 
  const batchCallTasks = async () => {
    const resourceId = resourceToHex({
      type: "system",
      namespace: "",
      name: "TasksSystem",
    });
 
    const addTaskAbi = [
      {
        type: "function",
        name: "addTask",
        inputs: [
          {
            name: "description",
            type: "string",
            internalType: "string",
          },
        ],
        outputs: [
          {
            name: "key",
            type: "bytes32",
            internalType: "bytes32",
          },
        ],
        stateMutability: "nonpayable",
      },
    ];
 
    const getCalldata = (description) =>
      encodeFunctionData({
        abi: addTaskAbi,
        args: [description],
      });
 
    const tasks = ["This is a test", `This task was made at ${Date()}`, "Yet another task"];
 
    const tx = await worldContract.write.batchCall([tasks.map((task) => [resourceId, getCalldata(task)])]);
    await waitForTransaction(tx);
  }; // end of batchCallTasks
 
  return {
    addTask,
    toggleTask,
    deleteTask,
    batchCallTasks,
  };
}
```

Explanation

```
import { resourceToHex } from "@latticexyz/common";
import { encodeFunctionData } from "viem";
```

Definitions we need.

```
  const batchCallTasks = async () => {
    const resourceId = resourceToHex({
      type: "system",
      namespace: "",
      name: "TasksSystem"
    })
```

The input to `batchCall` is an array, in which every entry is a structure with two fields:

- The `resourceId` of the `System` we are calling (in this case, `TasksSystem`)
- The calldata to send that `System`.

Here we generate the `resourceId`.

```
const addTaskAbi = [
  {
    type: "function",
    name: "addTask",
    inputs: [
      {
        name: "description",
        type: "string",
        internalType: "string",
      },
    ],
    outputs: [
      {
        name: "key",
        type: "bytes32",
        internalType: "bytes32",
      },
    ],
    stateMutability: "nonpayable",
  },
];
```

To generate the calldata we need the [ABI (opens in a new tab)](https://docs.soliditylang.org/en/latest/abi-spec.html).

ⓘ

It is not always possible to get the ABI from `worldContract`, because that contract has the functions as they are to be called when accessing the `World`. This means that if a function is _not_ in the root namespace, it will appear in `worldContract` is `<namespace>__<function name>`. However, in `batchCall` the namespace and `System` are already encoded in the `resourceId`. The function name to use is the function name when calling the `System`, without the `<namespace>__` prefix. So it is best to take the ABI from the `packages/contracts/out/<system name>.sol/<system name>.abi.json` file.

```
const getCalldata = (description) =>
  encodeFunctionData({
    abi: addTaskAbi,
    args: [description],
  });
```

With the ABI and the arguments (in this case there is only one, `description`) we use [`encodeFunctionData` (opens in a new tab)](https://viem.sh/docs/contract/encodeFunctionData.html) to generate the calldata.

```
const tasks = ["This is a test", `This task was made at ${Date()}`, "Yet another task"];
 
const tx = await worldContract.write.batchCall([tasks.map((task) => [resourceId, getCalldata(task)])]);
```

The parameter to `batchCall` is:

```
[
  [<resourceId for a System>, <calldata for that System>],
  [<resourceId for a System>, <calldata for that System>],
   .
   .
   .
  [<resourceId for a System>, <calldata for that System>],
]
```

Because of the way [`worldContract.write` (opens in a new tab)](https://viem.sh/docs/contract/getContract.html#with-wallet-client) works, even when there is a single parameter it has to be enclosed in a list.

```
    await waitForTransaction(tx)
  }    // end of batchCallTasks
```

[Account Delegation](/world/account-delegation "Account Delegation")[Upgrading](/world/upgrades "Upgrading")

---

MIT 2023 © MUD

Batch calls – MUD