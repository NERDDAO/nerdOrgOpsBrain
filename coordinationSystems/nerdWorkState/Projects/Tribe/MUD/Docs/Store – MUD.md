
Store

Reference

IStore (external)

## IStore[](#istore)

[Git Source (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/store/src/IStore.sol)

**Inherits:** [IStoreKernel](/src/IStoreKernel.sol/interface.IStoreKernel), [IStoreRegistration](/store/reference/store#istoreregistration)

## IStoreEvents[](#istoreevents)

[Git Source (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/store/src/IStoreEvents.sol)

### Events[](#events)

#### HelloStore[](#hellostore)

Emitted when the Store is created.

```
event HelloStore(bytes32 indexed storeVersion);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`storeVersion`|`bytes32`|The protocol version of the Store.|

#### Store_SetRecord[](#store_setrecord)

Emitted when a new record is set in the store.

```
event Store_SetRecord(
  ResourceId indexed tableId,
  bytes32[] keyTuple,
  bytes staticData,
  EncodedLengths encodedLengths,
  bytes dynamicData
);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table where the record is set.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`staticData`|`bytes`|The static data of the record.|
|`encodedLengths`|`EncodedLengths`|The encoded lengths of the dynamic data of the record.|
|`dynamicData`|`bytes`|The dynamic data of the record.|

#### Store_SpliceStaticData[](#store_splicestaticdata)

Emitted when static data in the store is spliced.

_In static data, data is always overwritten starting at the start position, so the total length of the data remains the same and no data is shifted._

```
event Store_SpliceStaticData(ResourceId indexed tableId, bytes32[] keyTuple, uint48 start, bytes data);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table where the data is spliced.|
|`keyTuple`|`bytes32[]`|An array representing the key for the record.|
|`start`|`uint48`|The start position in bytes for the splice operation.|
|`data`|`bytes`|The data to write to the static data of the record at the start byte.|

#### Store_SpliceDynamicData[](#store_splicedynamicdata)

Emitted when dynamic data in the store is spliced.

```
event Store_SpliceDynamicData(
  ResourceId indexed tableId,
  bytes32[] keyTuple,
  uint8 dynamicFieldIndex,
  uint48 start,
  uint40 deleteCount,
  EncodedLengths encodedLengths,
  bytes data
);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table where the data is spliced.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`dynamicFieldIndex`|`uint8`|The index of the dynamic field to splice data, relative to the start of the dynamic fields. (Dynamic field index = field index - number of static fields)|
|`start`|`uint48`|The start position in bytes for the splice operation.|
|`deleteCount`|`uint40`|The number of bytes to delete in the splice operation.|
|`encodedLengths`|`EncodedLengths`|The encoded lengths of the dynamic data of the record.|
|`data`|`bytes`|The data to insert into the dynamic data of the record at the start byte.|

#### Store_DeleteRecord[](#store_deleterecord)

Emitted when a record is deleted from the store.

```
event Store_DeleteRecord(ResourceId indexed tableId, bytes32[] keyTuple);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table where the record is deleted.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|

## IStoreErrors[](#istoreerrors)

[Git Source (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/store/src/IStoreErrors.sol)

This interface includes errors for Store.

_We bundle these errors in an interface (instead of at the file-level or in their corresponding library) so they can be inherited by IStore. This ensures that all possible errors are included in the IStore ABI for proper decoding in the frontend._

### Errors[](#errors)

#### Store_TableAlreadyExists[](#store_tablealreadyexists)

Error raised if the provided table already exists.

```
error Store_TableAlreadyExists(ResourceId tableId, string tableIdString);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table.|
|`tableIdString`|`string`|The stringified ID of the table (for easier debugging if cleartext tableIds are used).|

#### Store_TableNotFound[](#store_tablenotfound)

Error raised if the provided table cannot be found.

```
error Store_TableNotFound(ResourceId tableId, string tableIdString);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table.|
|`tableIdString`|`string`|The stringified ID of the table (for easier debugging if cleartext tableIds are used).|

#### Store_InvalidResourceType[](#store_invalidresourcetype)

Error raised if the provided resource ID cannot be found.

```
error Store_InvalidResourceType(bytes2 expected, ResourceId resourceId, string resourceIdString);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`expected`|`bytes2`|The expected resource type.|
|`resourceId`|`ResourceId`|The resource ID.|
|`resourceIdString`|`string`|The stringified resource ID (for easier debugging).|

#### Store_InvalidBounds[](#store_invalidbounds)

Error raised if the provided slice bounds are invalid.

```
error Store_InvalidBounds(uint256 start, uint256 end);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`start`|`uint256`|The start index within the dynamic field for the slice operation (inclusive).|
|`end`|`uint256`|The end index within the dynamic field for the slice operation (exclusive).|

#### Store_IndexOutOfBounds[](#store_indexoutofbounds)

Error raised if the provided index is out of bounds.

_Raised if the start index is larger than the previous length of the field._

```
error Store_IndexOutOfBounds(uint256 length, uint256 accessedIndex);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`length`|`uint256`|FIXME|
|`accessedIndex`|`uint256`|FIXME|

#### Store_InvalidStaticDataLength[](#store_invalidstaticdatalength)

Error raised if the provided static data length is invalid.

```
error Store_InvalidStaticDataLength(uint256 expected, uint256 received);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`expected`|`uint256`|The expected length.|
|`received`|`uint256`|The provided length.|

#### Store_InvalidKeyNamesLength[](#store_invalidkeynameslength)

Error raised if the provided key names length is invalid.

```
error Store_InvalidKeyNamesLength(uint256 expected, uint256 received);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`expected`|`uint256`|The expected length.|
|`received`|`uint256`|The provided length.|

#### Store_InvalidFieldNamesLength[](#store_invalidfieldnameslength)

Error raised if the provided field names length is invalid.

```
error Store_InvalidFieldNamesLength(uint256 expected, uint256 received);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`expected`|`uint256`|The expected length.|
|`received`|`uint256`|The provided length.|

#### Store_InvalidValueSchemaLength[](#store_invalidvalueschemalength)

Error raised if the provided value schema length is invalid.

```
error Store_InvalidValueSchemaLength(uint256 expected, uint256 received);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`expected`|`uint256`|The expected length.|
|`received`|`uint256`|The provided length.|

#### Store_InvalidValueSchemaStaticLength[](#store_invalidvalueschemastaticlength)

Error raised if the provided schema static length is invalid.

```
error Store_InvalidValueSchemaStaticLength(uint256 expected, uint256 received);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`expected`|`uint256`|The expected length.|
|`received`|`uint256`|The provided length.|

#### Store_InvalidValueSchemaDynamicLength[](#store_invalidvalueschemadynamiclength)

Error raised if the provided schema dynamic length is invalid.

```
error Store_InvalidValueSchemaDynamicLength(uint256 expected, uint256 received);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`expected`|`uint256`|The expected length.|
|`received`|`uint256`|The provided length.|

#### Store_InvalidSplice[](#store_invalidsplice)

Error raised if the provided splice is invalid.

_Raised if the splice total length of the field is changed but the splice is not at the end of the field._

```
error Store_InvalidSplice(uint40 startWithinField, uint40 deleteCount, uint40 fieldLength);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`startWithinField`|`uint40`|The start index within the field for the splice operation.|
|`deleteCount`|`uint40`|The number of bytes to delete in the splice operation.|
|`fieldLength`|`uint40`|The field length for the splice operation.|

## IStoreRead[](#istoreread)

[Git Source (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/store/src/IStoreRead.sol)

### Functions[](#functions)

#### getFieldLayout[](#getfieldlayout)

```
function getFieldLayout(ResourceId tableId) external view returns (FieldLayout fieldLayout);
```

#### getValueSchema[](#getvalueschema)

```
function getValueSchema(ResourceId tableId) external view returns (Schema valueSchema);
```

#### getKeySchema[](#getkeyschema)

```
function getKeySchema(ResourceId tableId) external view returns (Schema keySchema);
```

#### getRecord[](#getrecord)

Get full record (all fields, static and dynamic data) for the given tableId and key tuple, loading the field layout from storage

```
function getRecord(
  ResourceId tableId,
  bytes32[] calldata keyTuple
) external view returns (bytes memory staticData, EncodedLengths encodedLengths, bytes memory dynamicData);
```

#### getRecord[](#getrecord-1)

Get full record (all fields, static and dynamic data) for the given tableId and key tuple, with the given field layout

```
function getRecord(
  ResourceId tableId,
  bytes32[] calldata keyTuple,
  FieldLayout fieldLayout
) external view returns (bytes memory staticData, EncodedLengths encodedLengths, bytes memory dynamicData);
```

#### getField[](#getfield)

Get a single field from the given tableId and key tuple, loading the field layout from storage

```
function getField(
  ResourceId tableId,
  bytes32[] calldata keyTuple,
  uint8 fieldIndex
) external view returns (bytes memory data);
```

#### getField[](#getfield-1)

Get a single field from the given tableId and key tuple, with the given field layout

```
function getField(
  ResourceId tableId,
  bytes32[] calldata keyTuple,
  uint8 fieldIndex,
  FieldLayout fieldLayout
) external view returns (bytes memory data);
```

#### getStaticField[](#getstaticfield)

Get a single static field from the given tableId and key tuple, with the given value field layout. Note: the field value is left-aligned in the returned bytes32, the rest of the word is not zeroed out. Consumers are expected to truncate the returned value as needed.

```
function getStaticField(
  ResourceId tableId,
  bytes32[] calldata keyTuple,
  uint8 fieldIndex,
  FieldLayout fieldLayout
) external view returns (bytes32);
```

#### getDynamicField[](#getdynamicfield)

Get a single dynamic field from the given tableId and key tuple at the given dynamic field index. (Dynamic field index = field index - number of static fields)

```
function getDynamicField(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 dynamicFieldIndex
) external view returns (bytes memory);
```

#### getFieldLength[](#getfieldlength)

Get the byte length of a single field from the given tableId and key tuple, loading the field layout from storage

```
function getFieldLength(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 fieldIndex
) external view returns (uint256);
```

#### getFieldLength[](#getfieldlength-1)

Get the byte length of a single field from the given tableId and key tuple, with the given value field layout

```
function getFieldLength(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 fieldIndex,
  FieldLayout fieldLayout
) external view returns (uint256);
```

#### getDynamicFieldLength[](#getdynamicfieldlength)

Get the byte length of a single dynamic field from the given tableId and key tuple

```
function getDynamicFieldLength(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 dynamicFieldIndex
) external view returns (uint256);
```

#### getDynamicFieldSlice[](#getdynamicfieldslice)

Get a byte slice (including start, excluding end) of a single dynamic field from the given tableId and key tuple, with the given value field layout. The slice is unchecked and will return invalid data if `start`:`end` overflow.

```
function getDynamicFieldSlice(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 dynamicFieldIndex,
  uint256 start,
  uint256 end
) external view returns (bytes memory data);
```

## IStoreWrite[](#istorewrite)

[Git Source (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/store/src/IStoreWrite.sol)

**Inherits:** [IStoreEvents](/store/reference/store#istoreevents)

### Functions[](#functions-1)

#### setRecord[](#setrecord)

```
function setRecord(
  ResourceId tableId,
  bytes32[] calldata keyTuple,
  bytes calldata staticData,
  EncodedLengths encodedLengths,
  bytes calldata dynamicData
) external;
```

#### spliceStaticData[](#splicestaticdata)

```
function spliceStaticData(ResourceId tableId, bytes32[] calldata keyTuple, uint48 start, bytes calldata data) external;
```

#### spliceDynamicData[](#splicedynamicdata)

```
function spliceDynamicData(
  ResourceId tableId,
  bytes32[] calldata keyTuple,
  uint8 dynamicFieldIndex,
  uint40 startWithinField,
  uint40 deleteCount,
  bytes calldata data
) external;
```

#### setField[](#setfield)

```
function setField(ResourceId tableId, bytes32[] calldata keyTuple, uint8 fieldIndex, bytes calldata data) external;
```

#### setField[](#setfield-1)

```
function setField(
  ResourceId tableId,
  bytes32[] calldata keyTuple,
  uint8 fieldIndex,
  bytes calldata data,
  FieldLayout fieldLayout
) external;
```

#### setStaticField[](#setstaticfield)

```
function setStaticField(
  ResourceId tableId,
  bytes32[] calldata keyTuple,
  uint8 fieldIndex,
  bytes calldata data,
  FieldLayout fieldLayout
) external;
```

#### setDynamicField[](#setdynamicfield)

```
function setDynamicField(
  ResourceId tableId,
  bytes32[] calldata keyTuple,
  uint8 dynamicFieldIndex,
  bytes calldata data
) external;
```

#### pushToDynamicField[](#pushtodynamicfield)

```
function pushToDynamicField(
  ResourceId tableId,
  bytes32[] calldata keyTuple,
  uint8 dynamicFieldIndex,
  bytes calldata dataToPush
) external;
```

#### popFromDynamicField[](#popfromdynamicfield)

```
function popFromDynamicField(
  ResourceId tableId,
  bytes32[] calldata keyTuple,
  uint8 dynamicFieldIndex,
  uint256 byteLengthToPop
) external;
```

#### deleteRecord[](#deleterecord)

```
function deleteRecord(ResourceId tableId, bytes32[] memory keyTuple) external;
```

## IStoreRegistration[](#istoreregistration)

[Git Source (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/store/src/IStoreRegistration.sol)

_This interface includes methods for managing table field layouts, metadata, and hooks, which are usually called once in the setup phase of an application, making them less performance critical than the methods._

### Functions[](#functions-2)

#### registerTable[](#registertable)

[Usage Sample](/store/tables#manually-register-a-table)

```
function registerTable(
  ResourceId tableId,
  FieldLayout fieldLayout,
  Schema keySchema,
  Schema valueSchema,
  string[] calldata keyNames,
  string[] calldata fieldNames
) external;
```

#### registerStoreHook[](#registerstorehook)

[Usage Sample](/store/store-hooks)

```
function registerStoreHook(ResourceId tableId, IStoreHook hookAddress, uint8 enabledHooksBitmap) external;
```

#### unregisterStoreHook[](#unregisterstorehook)

```
function unregisterStoreHook(ResourceId tableId, IStoreHook hookAddress) external;
```

[StoreCore (internal)](/store/reference/store-core "StoreCore (internal)")[StoreHook](/store/reference/store-hook "StoreHook")

---

MIT 2023 © MUD

Store – MUD