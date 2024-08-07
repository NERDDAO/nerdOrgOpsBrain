

# Encoding

`Store` uses a custom encoding scheme to store data more compactly than Solidity. It is comparable to `abi.encodePacked`, but with some notable differences:

- **Array elements are tightly packed**, without any padding. This might cause some elements to wrap around two storage slots, but saves a lot of space. For example, an `address[3]` array will only use 2 storage slots in MUD instead of 3 in Solidity, leading to a 33% reduction in storage costs.
- **Array lengths are packed into a single slot**. Tables are limited to up to 5 dynamic fields, and the length of each field is limited to 240 (over a million millions), we can pack the lengths of all dynamic length fields of a table into a single storage slot. Compared to Solidity, this saves 1 storage slot per dynamic length element except for the first one.

This encoding scheme greatly reduces gas for storage operations on dynamic length fields and for emitting events. **For events it reduces the payload size by over 80%** compared to the Solidity default of `abi.encode` (depending on the table schema). Note that since the encoding happens at runtime and does not have access to compiler internals, there is some overhead for encoding and decoding that is not present in vanilla Solidity.

## Schema[](#schema)

The schema defines the data type of every field in the record, whether it is part of the key used to look the record up, or the value that the table provides.

To save gas, onchain the schema definition is divided between the key schema and the value schema. Both definitions use the same data structure, a `bytes32` value that encodes the total byte length of all static length fields, the number of static-length fields, the number of dynamic-length fields, and up to 28 column types.

|Byte(s)|Value|Type|Constraint|
|--:|---|---|---|
|0-1|Total length of static fields|`uint16`||
|2|Number of static length fields|`uint8`|≤ (28 - number of dynamic length fields)|
|3|Number of dynamic length fields|`uint8`|For the key schema, 0  <br>For the value schema, ≤5|
|4|Type of first column|`SchemaType`||
|5|Type of second column|`SchemaType`||
|.||||
|n|Type of (n-3)'th column|`SchemaType`||
|.||||
|31|Type of 28th column|`SchemaType`||

Types are represented via the `SchemaType` enum and encoded as one byte per type. The encoding is specified in [`SchemaType.sol` (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/schema-type/src/solidity/SchemaType.sol)

Schema types

|Value (hex)|Value (dec)|Type|
|--:|--:|---|
|0x00≤n≤0x1F|0≤n≤31|`uint<8(n+1)>`|
|0x20≤n≤0x3F|32≤n≤63|`int<8(n-31)>`|
|0x40≤n≤0x5F|64≤n≤95|`bytes<n-63>`|
|0x60|96|`bool`|
|0x61|97|`address`|
|0x62≤n≤0x81|98≤n≤129|`uint<8(n-97)>[]`|
|0x82≤n≤0xA1|130≤n≤161|`int<8(n-129)>[]`|
|0xA2≤n≤0xC1|162≤n≤193|`bytes<8(n-161)>[]`|
|0xC2|194|`bool[]`|
|0xC3|195|`address[]`|
|0xC4|196|`bytes`|
|0xC5|197|`string`|

Note that the dynamic-length types _must_ come after all the static-length types.

For example, let's interpret the schema `0x001c0303180001c5c48300000000000000000000000000000000000000000000`.

|Byte number|0-1|2|3|4|5|6|7|8|9|10-31|
|---|---|---|---|---|---|---|---|---|---|---|
|Schema data|`0x001c`|`03`|`03`|`18`|`00`|`01`|`c5`|`c4`|`83`|`0...0`|
|Meaning|28 bytes of static data|three static length fields|three dynamic length fields|1st field - `18` (`uint200`)|2nd field - `00` (`uint8`)|3rd field - `01` (`uin16`)|4th field - `c5` (`string`)|5th field - `c4` (`bytes`)|6th field - `83` (`int16[]`)|No other fields|

## Events[](#events)

The `Store_` events have two topics:

0. The event signature
    - [`0x8dbb3a9672eebfd3773e72dd9c102393436816d832c7ba9e1e1ac8fcadcac7a9` for `Store_SetRecord` (opens in a new tab)](https://www.4byte.directory/event-signatures/?bytes_signature=0x8dbb3a9672eebfd3773e72dd9c102393436816d832c7ba9e1e1ac8fcadcac7a9)
    - [`0x8c0b5119d4cec7b284c6b1b39252a03d1e2f2d7451a5895562524c113bb952be` for `Store_SpliceStaticData` (opens in a new tab)](https://www.4byte.directory/event-signatures/?bytes_signature=0x8c0b5119d4cec7b284c6b1b39252a03d1e2f2d7451a5895562524c113bb952be)
    - [`0xfe158a7adba34e256807c8a149028d3162918713c3838afc643ce9f96716ebfd` for `Store_SpliceDynamicData` (opens in a new tab)](https://www.4byte.directory/event-signatures/?bytes_signature=0xfe158a7adba34e256807c8a149028d3162918713c3838afc643ce9f96716ebfd)
    - [`0x0e1f72f429eb97e64878619984a91e687ae91610348b9ff4216782cc96e49d07` for `Store_DeleteRecord` (opens in a new tab)](https://www.4byte.directory/event-signatures/?bytes_signature=0x0e1f72f429eb97e64878619984a91e687ae91610348b9ff4216782cc96e49d07)
1. The ResourceId for the table being modified.

### `Store_SetRecord`[](#store_setrecord)

[This event (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/store/src/IStoreEvents.sol#L18-L32) is emitted when a new record is created with all the data fields.

The fields are in the event definition. In Solidity, unindexed event fields are [ABI encoded (opens in a new tab)](https://docs.soliditylang.org/en/latest/abi-spec.html#formal-specification-of-the-encoding).

|Field|Encoding|
|---|---|
|`bytes32[] keyTuple`|Standard ABI encoding (32-byte fields)|
|`bytes staticData`|Packed ABI encoding of a tuple with the value's static fields|
|`EncodedLengths encodedLengths`|See below.|
|`bytes dynamicData`|Modified packed ABI encoding of data, use `encodedLengths` for length delimiters|

`encodedLengths` is a 32-byte value that contains the length in bytes of all the dynamic data fields:

```
EncodedLengths = bytes[32] where:

bytes[0:6]   = len of all dynamic data
bytes[6:11]  = len of 1st dynamic field
bytes[11:16] = len of 2nd dynamic field
bytes[16:21] = len of 3rd dynamic field
bytes[21:26] = len of 4th dynamic field
bytes[26:31] = len of 5th dynamic field
```

Example

For example, we have a table with this definition:

```
    Complicated: {
       schema: {
         key1: "uint200",
         key2: "uint8",
         val1: "uint200",
         val2: "uint8",
         val3: "uint16",
         dyn1: "string",
         dyn2: "bytes",
         dyn3: "int16[]",
       },
       key: ["key1", "key2"],
    },
```

And we want to understand this data:

|Word|Data|
|--:|---|
|0|`0x0000000000000000000000000000000000000000000000000000000000000080`|
|1|`0x00000000000000000000000000000000000000000000000000000000000000e0`|
|2|`0x0000000000000000000000000000060000000005000000000500000000000010`|
|3|`0x0000000000000000000000000000000000000000000000000000000000000120`|
|4|`0x0000000000000000000000000000000000000000000000000000000000000002`|
|5|`0x00000000000000000000000000000000000000000000000000000000000060a7`|
|6|`0x0000000000000000000000000000000000000000000000000000000000000002`|
|7|`0x000000000000000000000000000000000000000000000000000000000000001c`|
|8|`0x00000000000000000000000000000000000000000000000bad04600d00000000`|
|9|`0x0000000000000000000000000000000000000000000000000000000000000010`|
|10|`0x68656c6c6f776f726c6400010002000300000000000000000000000000000000`|

The first four 256-bit words correspond to the four unindexed event fields.

|Word|Event field|Data|
|--:|---|---|
|0|`bytes32[] keyTuple`|`0x0...080`|
|1|`bytes staticData`|`0x0...0e0`|
|2|`EncodedLengths encodedLengths`|`0x0000000000000000000000000000060000000005000000000500000000000010`|
|3|`bytes dynamicData`|`0x0...0120`|

ABI encoding starts with one 32-byte word per field. If the field is static, the word contains that value. If the field is dynamic, the word contains an offset into where the field data starts.

|Event field|Starting byte|Words in the data|
|---|--:|--:|
|`bytes32[] keyTuple`|0x80 = 128 = 4*32|4-6|
|`bytes staticData`|0xE0 = 224 = 7*32|7-8|
|`bytes dynamicData`|0x120 = 288 = 9*32|9-10|

**`bytes32[] keyTuple`**

|Word|Data|
|--:|--:|
|4|`0x0......02`|
|5|`0x0...060a7`|
|6|`0x0......02`|

This is a variable-length array. The first word is the array length, followed by the values. The key fields are treated internally as 32-byte values, so every value takes 32 bytes. This gives us

|Record field|Value|
|---|--:|
|`key1`|0x60A7|
|`key2`|2|

**`bytes staticData`**

This is the static data (the static fields that are not in the key).

|Word|Data|
|--:|---|
|7|`0x0...01c`|
|8|`0x00000000000000000000000000000000000000000000000bad 04 600d 00000000`|

Because the type is an unspecified number of bytes, the first 32-byte word is the length. In this case, the length is `0x1C=28`. This means that only the first (most significant) 28 bytes of word 8 are meaningful. The other four are zero.

Going back to the schema, we see that there are three static data fields:

```
         val1: "uint200",
         val2: "uint8",
         val3: "uint16",
```

The first one is `200 bits = 25 bytes`, the second one is `8 bits = 1 byte`, and the third is `16 bits = 2 bytes`. They are written consecutively, so

|Record field|Value|
|---|--:|
|`val1`|0xBAD|
|`val2`|4|
|`val3`|0x600D|

**`EncodedLengths encodedLengths`**

[This is the definition of the `EncodedLengths` type (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/store/src/EncodedLengths.sol).

|Bytes|0x1F-0x1B|0x1A-0x16|0x15-0x11|0x10-0x0C|0x0B-0x07|0x06-0x00|
|---|---|---|---|---|---|---|
|Value|0x0...0|0x0...0|0x0...06|0x0...05|0x0...05|0x0...010|
|Length (in bytes) of|5th dynamic field|4th dynamic field|3rd dynamic field|2nd dynamic field|1st dynamic field|all dynamic fields together|

This is the dynamic field part of the schema:

```
         dyn1: "string",
         dyn2: "bytes",
         dyn3: "int16[]",
```

Here we see that the first five bytes of the dynamic data are going to be `dyn1`, the next five `dyn2`, and the final six `dyn3`. The total length is `0x10 = 16`.

**`bytes dynamicData`**

|Word|Data|
|--:|---|
|9|`0x0000000000000000000000000000000000000000000000000000000000000010`|
|10|`0x68656c6c6f 776f726c64 000100020003 00000000000000000000000000000000`|

This value is also `bytes`, so the first word is the length of the data, in this case `0x10=16`. Combining this with the encoded lengths information, we get these values:

|Record field|Value|
|---|--:|
|`dyn1`|`0x68656c6c6f`|
|`dyn2`|`0x776f726c64`|
|`dyn3`|`0x00100020003`|

To interpret the values we need to get the types from the schema.

|Record field|Field type|Value|
|---|---|---|
|`dyn1`|`string`|hello|
|`dyn2`|`bytes`|`0x776f726c64` (= world)|
|`dyn3`|`int16[]`|[1,2,3]|

### `Store_SpliceStaticData`[](#store_splicestaticdata)

[This event (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/store/src/IStoreEvents.sol#L34-L43) is emitted when a record's static fields are modified. Note that this event is also emitted if the record did not exist previously. Solidity does not distinguish between a record not existing, and a record existing with all the values set to zero.

|Field|Encoding|
|---|---|
|`bytes32[] keyTuple`|Standard ABI encoding (32-byte fields)|
|`uint48 start`|The starting byte of the segment of static data to change|
|`bytes data`|Packed ABI encoding of a tuple with the value's static fields|

Example

Using the same table definition as with `Store_SetRecord`, we want to understand this data:

|Word|Data|
|--:|---|
|0|`0x0000000000000000000000000000000000000000000000000000000000000060`|
|1|`0x0000000000000000000000000000000000000000000000000000000000000019`|
|2|`0x00000000000000000000000000000000000000000000000000000000000000c0`|
|3|`0x0000000000000000000000000000000000000000000000000000000000000002`|
|4|`0x00000000000000000000000000000000000000000000000000000000000060a7`|
|5|`0x0000000000000000000000000000000000000000000000000000000000000003`|
|6|`0x0000000000000000000000000000000000000000000000000000000000000001`|
|7|`0xff00000000000000000000000000000000000000000000000000000000000000`|

The first three 256-bit words correspond to the three unindexed event fields.

|Word|Event field|Data|
|--:|---|---|
|0|`bytes32[] keyTuple`|`0x00...60`|
|1|`uint48 start`|`0x00...19`|
|2|`bytes data`|`0x00...C0`|

`keyTuple` is interpreted the same way it is in `Store_SetRecord`.

**`uint48 start`**

`start` is `0x19 = 25`.

Going back to the schema, we see that there are three static data fields:

```
         val1: "uint200",
         val2: "uint8",
         val3: "uint16",
```

Starting from zero, this means that `val1` is bytes 0-24, `val2` is byte 25, and `val3` is bytes 26-27. We are changing from byte 25, so `val1`'s value stays the same.

**`bytes data`**

This data starts at word six (byte `0xC0 = 192 = 32*6`).

|Word|Data|
|--:|---|
|6|`0x0...01`|
|7|`0xff 0...0`|

The first word of this field, word 6, tells us that we are only changing one byte of the data, so we aren't changing `val3` either. The first byte of word 7, the only one that matters, is `0xFF`. This is the new value of `val2`.

### `Store_SpliceDynamicData`[](#store_splicedynamicdata)

[This event (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/store/src/IStoreEvents.sol#L45-L64) is emitted when a record's dynamic fields are modified. Note that this event is also emitted if the record did not exist previously. Solidity does not distinguish between a record not existing, and a record existing with all the values set to zero.

|Field|Encoding|
|---|---|
|`bytes32[] keyTuple`|Standard ABI encoding (32-byte fields)|
|`uint8 dynamicFieldIndex`|The sequential number (counting from zero) of the dynamic field to change. 0 ≤ `dynamicFieldIndex` ≤ 4|
|`uint48 start`|The starting byte of the segment of the `dynamicFieldIndex`'th dynamic field to change|
|`uint40 deleteCount`|The number of bytes to delete from the `dynamicFieldIndex`'th dynamic field.|
|`EncodedLengths encodedLengths`|Similar to `encodedLengths` in `Store_SetRecord`, except that only the size of the `dynamicFieldIndex`'th field is included|
|`bytes data`|Bytes to add/change in the `dynamicFieldIndex`'th dynamic field.|

Example: Appending and modifying data

Using the same table definition as with `Store_SetRecord`, we want to understand this data. Note that the log entry by itself does not tell us if we are appending data or modifying an existing value. It only tells us that we are changing the data in bytes six and seven, not whether there is already data there or not.

|Word|Data|
|--:|---|
|0|`0x00000000000000000000000000000000000000000000000000000000000000c0`|
|1|`0x0000000000000000000000000000000000000000000000000000000000000002`|
|2|`0x0000000000000000000000000000000000000000000000000000000000000006`|
|3|`0x0000000000000000000000000000000000000000000000000000000000000000`|
|4|`0x0000000000000000000000000000080000000000000000000000000000000008`|
|5|`0x0000000000000000000000000000000000000000000000000000000000000120`|
|6|`0x0000000000000000000000000000000000000000000000000000000000000002`|
|7|`0x00000000000000000000000000000000000000000000000000000000000060a7`|
|8|`0x0000000000000000000000000000000000000000000000000000000000000005`|
|9|`0x0000000000000000000000000000000000000000000000000000000000000002`|
|10|`0x1234000000000000000000000000000000000000000000000000000000000000`|

The first six 256-bit words correspond to the unindexed event fields.

|Word|Event field|Data|
|--:|---|---|
|0|`bytes32[] keyTuple`|`0x00...c0`|
|1|`uint8 dynamicFieldIndex`|`0x00...02`|
|2|`uint48 start`|`0x00...06`|
|3|`uint40 deleteCount`|`0x00...00`|
|4|`EncodedLengths encodedLengths`|`0x0000000000 0000000000 0000000008 0000000000 0000000000 00000000000008`|
|5|`bytes data`|`0x00...120`|

`keyTuple` is interpreted the same way it is in `Store_SetRecord`.

**`uint8 dynamicFieldIndex`**

The index of the dynamic field we are modifying. The value here is two. Counting from zero it is the third schema field.

```
         dyn3: "int16[]",
```

**`uint48 start`**

The byte at which we start the change. Here is it byte six. The effect on the length of the data depends on what we are doing. If we are appending data, we would have six bytes now (three values) and add to them. If we are modifying data, we should have at least eight bytes now (four values), and we modify the fourth value (`dyn3[3]`).

**`uint40 deleteCount`**

The number of bytes we delete. In this case, zero.

**`EncodedLengths encodedLengths`**

The encoded lengths in the data after the change (if they change). This value tells us that only the third field changes, and that it will be eight bytes long.

**`bytes data`**

This value starts at byte `0x120 = 288 = 9*32`, or word nine.

|Word|Data|
|--:|---|
|9|`0x0000000000000000000000000000000000000000000000000000000000000002`|
|10|`0x1234 000000000000000000000000000000000000000000000000000000000000`|

Again, the first word of the `bytes` value tells us how many bytes we have (two). The second gives us the value we are adding, in this case `0x1234`.

Example: Deleting data

Using the same table definition as with `Store_SetRecord`, we want to understand this data:

|Word|Data|
|--:|---|
|0|`0x00000000000000000000000000000000000000000000000000000000000000c0`|
|1|`0x0000000000000000000000000000000000000000000000000000000000000002`|
|2|`0x0000000000000000000000000000000000000000000000000000000000000006`|
|3|`0x0000000000000000000000000000000000000000000000000000000000000002`|
|4|`0x0000000000000000000000000000060000000000000000000000000000000006`|
|5|`0x0000000000000000000000000000000000000000000000000000000000000120`|
|6|`0x0000000000000000000000000000000000000000000000000000000000000002`|
|7|`0x00000000000000000000000000000000000000000000000000000000000060a7`|
|8|`0x0000000000000000000000000000000000000000000000000000000000000005`|
|9|`0x0000000000000000000000000000000000000000000000000000000000000000`|

The first six 256-bit words correspond to the unindexed event fields.

|Word|Event field|Data|
|--:|---|---|
|0|`bytes32[] keyTuple`|`0x00...c0`|
|1|`uint8 dynamicFieldIndex`|`0x00...02`|
|2|`uint48 start`|`0x00...06`|
|3|`uint40 deleteCount`|`0x00...02`|
|4|`EncodedLengths encodedLengths`|`0x0000000000000000000000000000060000000000000000000000000000000006`|
|5|`bytes data`|`0x00...120`|

This is very similar to appending or modifying data, except that `deleteCount` has a value. This is the number of bytes we are deleting, two (a single array element). Combining this with the `start` value of six, we are deleting bytes six and seven.

The other difference is that word 9 is zero - there is a `bytes data` value, but it is empty.

### `Store_DeleteRecord`[](#store_deleterecord)

[This event (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/store/src/IStoreEvents.sol#L66-L71) is emitted when a record is deleted.

|Field|Encoding|
|---|---|
|`bytes32[] keyTuple`|Standard ABI encoding (32-byte fields)|

The only unindexed event field is `bytes32[] keyTuple`, which contains the key. See the example in [`Store_SetRecord`](/store/encoding#store_setrecord) to interpret it.

## Onchain storage layout[](#onchain-storage-layout)

The data of each record of each table is stored in its own location in storage.

The location of the static-length data is determined by a hash of the table ID and the key tuple. Since data is tightly packed, fields may wrap around two storage slots.

```
function _getStaticDataLocation(ResourceId tableId, bytes32[] memory keyTuple) internal pure returns (uint256) {
  return uint256(SLOT ^ keccak256(abi.encodePacked(tableId, keyTuple)));
}
```

[The `encodedLengths` field](/store/encoding#encoded-lengths) (the length of all dynamic-length fields) is stored in a single storage slot, which is determined by a hash of the table ID and the key tuple.

```
function _getDynamicDataLengthLocation(ResourceId tableId, bytes32[] memory keyTuple) internal pure returns (uint256) {
  return uint256(DYNAMIC_DATA_LENGTH_SLOT ^ keccak256(abi.encodePacked(tableId, keyTuple)));
}
```

The data for each dynamic-length field is stored in its own location in storage, which is determined by a hash of the table ID, the key tuple, and the field index. This allows MUD to change the length of a dynamic-length field without having to move the data of other fields.

```
function _getDynamicDataLocation(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 dynamicFieldIndex
) internal pure returns (uint256) {
  return uint256(DYNAMIC_DATA_SLOT ^ bytes1(dynamicFieldIndex) ^ keccak256(abi.encodePacked(tableId, keyTuple)));
}
```

[Table libraries](/store/table-libraries "Table libraries")[Store hooks](/store/store-hooks "Store hooks")

---

MIT 2023 © MUD

Encoding – MUD