
# Modules

Modules are onchain installation scripts that create resources and their associated configuration when called by a `World`. This is somewhat similar to one of the use cases for [foundry scripts (opens in a new tab)](https://book.getfoundry.sh/tutorials/solidity-scripting), except that modules are deployed onchain and can be used by any `World` on the same chain.

## Module installation[](#module-installation)

Modules can be installed using [`World.installModule(address moduleAddress, bytes memory initData)` (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/world/src/modules/init/implementations/ModuleInstallationSystem.sol#L17-L37). When you do this, the `World` calls the module's `install` function with `initData` as the argument(s). Because this is a call, the module does not have any administrative permissions on the `World`.

Alternatively, the owner of the root namespace can install modules using [`World.installRootModule(address moduleAddress, bytes memory initData)` (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/world/src/World.sol#L90-L119). In this case, the `World` uses delegatecall and module has all the permissions of a `System` in the root namespace.

## Writing modules[](#writing-modules)

The common use for a module is to add functionality to a `World`. In most cases we expect that a module would:

1. Create a [namespace](/world/namespaces-access-control) for the new functionality.
2. Create the [tables](/world/tables) and [`System`s](/world/systems) for the new functionality.
3. Create any access permissions required (beyond the default, which is that a `System` has access to its own namespace).
4. Either assign the ownership of the new namespace to an entity that would administer it (a user, a multisig, etc.) or burn it by assigning the namespace ownership to `address(0)`.

Root modules run with the same permission as `System`s in the root namespace, so they do not need to create a namespace for themselves. They can just create their own tables and `System`s as needed.

## Sample modules[](#sample-modules)

MUD comes with several modules you can use or inspect to help you write your own:

- [`KeysInTable` (opens in a new tab)](https://github.com/latticexyz/mud/tree/main/packages/world-modules/src/modules/keysintable) - automatically tracks the keys in a table to make them enumerable.
- [`KeysWithValue` (opens in a new tab)](https://github.com/latticexyz/mud/tree/main/packages/world-modules/src/modules/keyswithvalue) - automatically tracks a reverse mapping for a table that maps a value hash to a list of keys with this value.
- [`Puppet` (opens in a new tab)](https://github.com/latticexyz/mud/tree/main/packages/world-modules/src/modules/puppet) - installs the PuppetDelegationControl to allow creating Puppet contracts, eg used by the ERC20 and ERC721 modules.
- [`ERC20` (opens in a new tab)](https://github.com/latticexyz/mud/tree/main/packages/world-modules/src/modules/erc20-puppet) - installs an ERC20 token into a namespace in a World.
- [`ERC721` (opens in a new tab)](https://github.com/latticexyz/mud/tree/main/packages/world-modules/src/modules/erc721-puppet) - installs an ERC721 token into a namespace in a World.
- [`UniqueEntity` (opens in a new tab)](https://github.com/latticexyz/mud/tree/main/packages/world-modules/src/modules/uniqueentity) - add methods to get a unique entity ID.
- [`Standard Delegations` (opens in a new tab)](https://github.com/latticexyz/mud/tree/main/packages/world-modules/src/modules/std-delegations) - add delegations limited by time or number of calls.

Last updated on May 10, 2024

[Upgrading](/world/upgrades "Upgrading")[Keys with Value](/world/modules/keyswithvalue "Keys with Value")

---

MIT 2023 © MUD

Modules – MUD