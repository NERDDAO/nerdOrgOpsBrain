
# Tables

Each piece of data in `Store` is stored as a _record_ in a _table_. You can think of tables in two ways, either [as a relational database](/store/data-model) or as a key-value store.

- Each table is identified by a unique `ResourceId tableId`.
- Each record in a table is identified by a unique `bytes32[] keyTuple`. You can think of the key tuple as a composite key in a relational database, or as a nested mapping in a key-value store.
- Each table has a value schema (all the `schema` fields that aren't part of the `key`) that defines the types of data stored in the table. You can think of the value schema as the column types in a table in a relational database, or the type of structs stored in a key-value store.

Tables are registered in the `Store` contract at runtime. When a table is registered, its ID, key and value types, as well as key and field names are stored in the internal `Tables` table. This emits an event that can be used by [offchain indexers](/services/indexer) to start [replicating the state](/guides/replicating-onchain-state) of the new table.

The recommended way of reading from tables and writing to tables is via the [typed table libraries](/store/table-libraries). However, it is also possible to use the low-level [`IStore` (external)](/store/reference/store) or [`StoreCore` (internal)](/store/reference/store-core) API directly.

⚠️

Once a table is created its schema is _immutable_. If you need to add fields to an existing table, create a new table with the same key schema and the new fields in the value schema, and retrieve from both tables to get the complete value. If you need to delete fields, just have your code ignore them.

## Types of tables[](#types-of-tables)

There are two types of tables in `Store`: _Onchain tables_ and _offchain tables_. We often omit the prefix from onchain tables and just call them tables.

As the name suggests, **onchain tables** store their state onchain, in the `Store` contract. In addition, an event is emitted on every write operation, to allow [offchain indexers](/services/indexer) to [replicate the onchain state](/guides/replicating-onchain-state).

**Offchain tables** on the other hand don't store any state onchain, but only emit the events for [offchain indexers](/services/indexer). This makes them suitable for use cases where data doesn't need to be retrieved onchain, but should still be synchronized from the `Store` contract to [offchain indexers](/services/indexer).

Onchain you can write to offchain tables with the same methods as onchain tables, except for reading data and modifying partial data in a dynamic field.

## Advanced[](#advanced)

ⓘ

In practice, everything in this section is automatically handled by the [typed table libraries](/store/table-libraries). Read on if you're curious about the inner workings.

### Table ID[](#table-id)

The table ID encodes the table's type and name in a 32-byte `ResourceId` value and uniquely identifies a table.

```
import { ResourceId, ResourceIdLib } from "@latticexyz/store/src/ResourceId.sol";
import { RESOURCE_TABLE, RESOURCE_OFFCHAIN_TABLE } from "@latticexyz/store/src/storeResourceTypes.sol";
 
...
 
// Using the `RESOURCE_TABLE` type makes it an onchain table
ResourceId tableId = ResourceIdLib.encode({
  typeId: RESOURCE_TABLE,
  name: "Balance"
});
 
// Using the `RESOURCE_OFFCHAIN_TABLE` type makes it an offchain table
ResourceId offchainTableId = ResourceIdLib.encode({
  typeId: RESOURCE_OFFCHAIN_TABLE,
  name: "Balance"
});
```

### The schemas[](#the-schemas)

Tables have two schemas, one for the key and the other for the values.

#### Key schema[](#key-schema)

The key schema represents the data types of the (composite) key of the table. Internally the composite key is always handled as `bytes32[]`, but the key schema allows [offchain indexers](/services/indexer) to create properly typed composite key columns, and allows MUD to generate [typed table libraries](/store/table-libraries).

The key schema can contain up to 28 static (fixed length) data types. Note that no dynamic (variable length) data types (e.g. arrays, `bytes` or `string`) are supported in the key schema.

```
import { Schema, SchemaLib } from "@latticexyz/store/src/Schema.sol";
import { SchemaType } from "@latticexyz/schema-type/src/solidity/SchemaType.sol";
 
// NOTE: the key schema can only include static types
SchemaType[] memory keySchemaTypes = new SchemaType[](1);
keySchemaTypes[0] = SchemaType.ADDRESS;
Schema keySchema = SchemaLib.encode(keySchemaTypes);
```

#### Value schema[](#value-schema)

The value schema represents the data types of the value fields of the table. Similar to key schemas, it allows [offchain indexers](/services/indexer) to create properly typed value columns, and allows MUD to generate [typed table libraries](/store/table-libraries).

The value schema can contain up to 28 fields in total, of which up to 5 can be dynamic (variable length) data types (e.g. arrays, `bytes`, `string`). All static (fixed length) data types must come before the dynamic data types.

```
import { Schema, SchemaLib } from "@latticexyz/store/src/Schema.sol";
import { SchemaType } from "@latticexyz/schema-type/src/solidity/SchemaType.sol";
 
// NOTE: static types must come before dynamic types
SchemaType[] memory valueSchemaTypes = new SchemaType[](1);
valueSchemaTypes[0] = SchemaType.UINT256;
Schema valueSchema = SchemaLib.encode(valueSchemaTypes);
```

### Key and field names[](#key-and-field-names)

Key and (value) field names are simple string arrays, and must have the same length as the key and value schemas respectively.

```
string[] memory keyNames = new string[](1);
keyNames[0] = "owner";
 
string[] memory fieldNames = new string[](1);
fieldNames[0] = "balance";
```

### Field layout[](#field-layout)

The field layout encodes the byte length of each static data type in the table. This is mostly a way to save gas in onchain operations that require the length of static fields, as it avoids additional steps to translate the schema into the corresponding byte length.

```
import { FieldLayout, FieldLayoutLib } from "@latticexyz/store/src/FieldLayout.sol";
 
uint256[] memory staticFields = new uint256[](1);
staticFields[0] = 32; // The byte length of the first field
FieldLayout fieldLayout = FieldLayoutLib.encode(staticFields, 0);
```

### Manually register a table[](#manually-register-a-table)

Registering a table is a prerequisite for writing to the table, and allows [offchain indexers](/services/indexer) to [replicate the onchain state](/guides/replicating-onchain-state).

```
import { IStore } from "@latticexyz/store/src/IStore.sol";
 
IStore store = IStore(STORE_ADDRESS);
store.registerTable({
  tableId: tableId,
  fieldLayout: fieldLayout,
  keySchema: keySchema,
  valueSchema: valueSchema,
  keyNames: keyNames,
  fieldNames: fieldNames
});
```

[Data model](/store/data-model "Data model")[Table libraries](/store/table-libraries "Table libraries")

---

MIT 2023 © MUD

Tables – MUD