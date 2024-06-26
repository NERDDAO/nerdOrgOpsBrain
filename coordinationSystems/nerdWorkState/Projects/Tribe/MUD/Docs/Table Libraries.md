

# Table Libraries

Methods on [`StoreCore`](/references/store-core) and [`IStore`](/references/store) use raw bytes as input and output types. This is to allow using the same methods for different types of tables types without native support for generics in Solidity, but it can be inconvenient to work with. For this reason MUD automatically generates a library for each table that provides typed methods corresponding to the table's key and value types.

## Config[](#config)

Table libraries are generated by the [`tablegen`](/cli/tablegen) CLI. The [CLI config](/cli/config) section goes into more detail on the available configuration options.

To illustrate table library functionality on this page, we'll use two example tables with the following configurations:

Position

```
schema: {
  entity: "address",
 
  // Two static length fields
  x: "uint32",
  y: "uint32",
},
key: ["entity"],
```

Inventory

```
schema: {
  entity: "address",
 
  // One dynamic length field
  slots: "uint8[]",
},
key: ["entity"],
```

### Singleton tables[](#singleton-tables)

When you just need to store a single value (for example, the world map or the name of an in-game currency), you use a singleton table, a table whose key schema is empty. With an empty key schema there is just one record.

Counter

```
schema: {
  value: "uint32",
},
key: [],
```

## Usage[](#usage)

### Importing the library[](#importing-the-library)

By default table libraries are generated into a `codegen/tables` directory in the contract directory. This can be configured with the `codegenDirectory` option in the [config](/cli/config). The library name corresponds to the table's key in the table config.

```
import { Position } from "./codegen/tables/Position.sol";
import { Inventory } from "./codegen/tables/Inventory.sol";
```

For convenience, the [`tablegen`](/cli/tablegen) CLI also generates an `index.sol` file that re-exports all the generated table libraries.

```
import { Position, Inventory } from "./codegen/tables/index.sol";
```

### Registering the table[](#registering-the-table)

Before using the table library, it must be registered once in the Store. This happens automatically as part of the [deploy CLI](/cli/deploy), but if you're using the table library in a [Module](/world/modules) or a contract that's not deployed by MUD, you'll need to register it manually.

```
Position.register();
Inventory.register();
```

The table library will detect the `Store` context automatically (via [`StoreSwitch`](/store/table-libraries#storeswitch)).

### Reading data[](#reading-data)

Table libraries provide typed methods for reading data from the `Store`.

#### Read full record[](#read-full-record)

To read the entire record, use the `get` method. If the table's value schema contains more than one field, the method returns a struct with the corresponding fields.

```
import { Position, PositionData } from "./codegen/tables/Position.sol";
 
address entity = address(0x1234);
PositionData memory position = Position.get(entity);
```

#### Read individual fields[](#read-individual-fields)

To read a single field, use the `get` method with the field name as a suffix.

```
import { Position } from "./codegen/tables/Position.sol";
 
address entity = address(0x1234);
uint32 x = Position.getX(entity);
```

#### Read length of dynamic length field[](#read-length-of-dynamic-length-field)

To read the length of a dynamic length field, use the `length` method with the field name as a suffix.

```
import { Inventory } from "./codegen/tables/Inventory.sol";
 
address entity = address(0x1234);
uint256 length = Inventory.lengthSlots(entity);
```

#### Read individual elements of dynamic length field[](#read-individual-elements-of-dynamic-length-field)

To read individual fields of a dynamic length field, use the `getItem` method with the field name as a suffix and the index as an argument.

```
import { Inventory } from "./codegen/tables/Inventory.sol";
 
address entity = address(0x1234);
uint8 item = Inventory.getItemSlots(entity, 0);
```

### Writing data[](#writing-data)

Table libraries provide typed methods for writing data to the `Store`.

#### Write full record[](#write-full-record)

To write the entire record, use the `set` method. There is a variant of this method that accepts a struct, and a variant that accepts the individual fields.

```
import { Position, PositionData } from "./codegen/tables/Position.sol";
 
address entity = address(0x1234);
 
// set with individual fields
Position.set(entity, 1, 2);
 
// or set with struct
Position.set(entity, PositionData(1,2));
```

#### Write individual fields[](#write-individual-fields)

To write a single field, use the `set` method with the field name as a suffix.

```
import { Position } from "./codegen/tables/Position.sol";
 
address entity = address(0x1234);
Position.setX(entity, 1);
```

#### Write individual elements of dynamic length field[](#write-individual-elements-of-dynamic-length-field)

To write individual fields of a dynamic length field, use the `update` method with the field name as a suffix and the index as an argument.

```
import { Inventory } from "./codegen/tables/Inventory.sol";
 
address entity = address(0x1234);
Inventory.updateSlots(entity, 0, 1);
```

#### Push to dynamic length field[](#push-to-dynamic-length-field)

To push a new element to a dynamic length field, use the `push` method with the field name as a suffix.

```
import { Inventory } from "./codegen/tables/Inventory.sol";
 
address entity = address(0x1234);
Inventory.pushSlots(entity, 1);
```

#### Pop from dynamic length field[](#pop-from-dynamic-length-field)

To remove the last element from a dynamic length field, use the `pop` method with the field name as a suffix.

```
import { Inventory } from "./codegen/tables/Inventory.sol";
 
address entity = address(0x1234);
Inventory.popSlots(entity);
```

Note that this method does not return the popped element. If you need to access the popped element, use the `getItem` method before calling `pop`.

```
import { Inventory } from "./codegen/tables/Inventory.sol";
 
address entity = address(0x1234);
uint256 length = Inventory.lengthSlots(entity);
uint8 lastItem = Inventory.getItemSlots(entity,  length - 1);
Inventory.popSlots(entity);
```

## Advanced[](#advanced)

### StoreSwitch[](#storeswitch)

Table libraries internally use the `StoreSwitch` library to write data to the Store. `StoreSwitch` detects whether the library is called from within a `Store` contract or from an external contract that has a reference to the `Store` contract.

1. If the library is called from within a `Store` contract, `StoreSwitch` accesses the store directly via the [internal `StoreCore` library](/store/reference/store-core).
2. If the library is called from a contract that has a fixed reference to a specific external `Store` contract, `StoreSwitch` accesses this `Store` via the [external `IStore` interface](/store/reference/store).
3. If `msg.sender` is a `Store` contract, `StoreSwitch` accesses the `Store` via the caller's address via the [external `IStore` interface](/store/reference/store).

To fix a reference to a specific `Store` contract (2), use the `StoreSwitch.setStoreAddress` method in your contract.

```
import { StoreSwitch } from "@latticexyz/store/src/StoreSwitch.sol";
 
StoreSwitch.setStoreAddress(STORE_ADDRESS);
```

This can be useful to test libraries using table libraries in isolation, by setting the `Store` address in the test contract to a mock `Store` contract. This happens automatically in the [`MudTest`](/cli/test) setup.

[Tables](/store/tables "Tables")[Encoding](/store/encoding "Encoding")

---

MIT 2023 © MUD

Table Libraries – MUD