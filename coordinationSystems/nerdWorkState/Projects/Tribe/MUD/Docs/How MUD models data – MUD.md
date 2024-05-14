
Data model

# How MUD models data

The MUD framework helps you to model your onchain state in a way that enables building applications with familiar tooling, like relational databases.

In your MUD config, you define data as a set of tables. Like relational databases, these tables have “columns” by way of two MUD concepts: a schema and a key. A schema is set of fields that map to Solidity types and a key is the list of fields that combine to form the table's primary key.

## Defining tables[](#defining-tables)

Let's say we're making a game where players can move around a map. The definition for a `Position` table might look something like:

mud.config.ts

```
import { defineWorld } from "@latticexyz/world";
 
export default defineWorld({
  tables: {
    Position: {
      schema: {
        player: "address",
        x: "int32",
        y: "int32",
      },
      key: ["player"],
    },
  },
});
```

Translating the table definition above would look like this in a relational database:

|player|x|y|
|---|---|---|
|0x1234|-5|1|
|0x5678|3|6|

Because `player` is the key, we would have a unique/primary key constraint on `player`.

Let's add another table for terrain at a given position on the map. MUD allows for multiple fields in the key and, like databases, we call these composite keys.

mud.config.ts

```
import { defineWorld } from "@latticexyz/world";
 
export default defineWorld({
  tables: {
    Position: {
      schema: {
        player: "address",
        x: "int32",
        y: "int32",
      },
      key: ["player"],
    },
    Terrain: {
      schema: {
        x: "int32",
        y: "int32",
        terrainType: "string",
      },
      key: ["x", "y"],
    },
  },
});
```

Similarly, the relational database representation of that table would look like:

|x|y|terrainType|
|---|---|---|
|-1|0|grass|
|0|1|tree|
|1|0|grass|

Because we have a composite key of `x` and `y`, we would have a unique/primary key constraint on the tuple of `(x, y)`.

## Tables on chain[](#tables-on-chain)

Solidity and the EVM have much more limited data structures, so how can we express a relational database onchain? MUD takes the concept of tables and does a few things with them:

- [encodes each table record as a key/value pair](/store/encoding) before storing onchain
- [emits an event for each mutation](/guides/replicating-onchain-state)
- [provides a typed Solidity libraries](/store/table-libraries) to abstract this away

## Field types[](#field-types)

Schema fields can use all of Solidity's static-length primitive types, arrays of those static-length primitive types, as well as `string` and `bytes`.

Key can only be used with fields that are defined as static-length Solidity primitive types.

Enums and user types are also supported and automatically map down to their Solidity primitive types.

ⓘ

The code and the rest of the documentation call some fields _static_ and others _dynamic_. In MUD terminology _static_ means _static length_ (`uint16`, `int32`, `bytes32`, etc.) and _dynamic_ means _dynamic length_ (`uint[]`, `string`, `bytes`, etc.).

More complex types like structs, `string[]`, and `bytes[]` are not yet supported. We'll cover the reasons why in our encoding/decoding guide.

[Introduction](/store/introduction "Introduction")[Tables](/store/tables "Tables")

---

MIT 2023 © MUD

How MUD models data – MUD