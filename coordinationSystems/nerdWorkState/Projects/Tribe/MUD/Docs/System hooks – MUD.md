
# System hooks

ⓘ

This page is about hooks that are called before or after a `System` call. You can also use [store hooks](/store/store-hooks) that are called whenever information in a table is modified, regardless of the source of the change.

The [namespace owner](/world/namespaces-access-control#ownership) can [register (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/world/src/modules/init/implementations/WorldRegistrationSystem.sol#L68-L99) hooks that are called before and/or after calls to a specific `System`.

## System hook contracts[](#system-hook-contracts)

A system hook contract is a contract that implements the [`ISystemHook` (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/world/src/ISystemHook.sol) interface. This interface requires the implementation of two functions.

- `onBeforeCallSystem`, which is called before the actual call.
- `onAfterCallSystem`, which is called afterward.
- `supportsInterface`, which is part of [IEP-165 (opens in a new tab)](https://eips.ethereum.org/EIPS/eip-165) used to specify which interfaces are supported by a contract.

To have the correct `supportsInterface` you can inherit from [`SystemHook` (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/world/src/SystemHook.sol).

[Systems](/world/systems "Systems")[Function Selectors](/world/function-selectors "Function Selectors")

---

MIT 2023 © MUD

System hooks – MUD