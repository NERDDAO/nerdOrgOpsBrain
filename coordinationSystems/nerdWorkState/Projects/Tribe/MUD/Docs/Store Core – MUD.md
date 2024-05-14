

Store

Reference

StoreCore (internal)

## StoreCore[](#storecore)

[Git Source (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/store/src/StoreCore.sol)

This library includes implementations for all IStore methods and events related to the store actions.

### Functions[](#functions)

#### initialize[](#initialize)

Initialize the store address in StoreSwitch.

_Consumers must call this function in their constructor. StoreSwitch uses the storeAddress to decide where to write data to. If StoreSwitch is called in the context of a Store contract (storeAddress == address(this)), StoreSwitch uses internal methods to write data instead of external calls._

```
function initialize() internal;
```

#### registerInternalTables[](#registerinternaltables)

Register Store protocol's internal tables in the store.

_Consumers must call this function in their constructor before setting any table data to allow indexers to decode table events._

```
function registerInternalTables() internal;
```

#### getFieldLayout[](#getfieldlayout)

SCHEMA

Get the field layout for the given table ID.

```
function getFieldLayout(ResourceId tableId) internal view returns (FieldLayout);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table for which to get the field layout.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`FieldLayout`|The field layout for the given table ID.|

#### getKeySchema[](#getkeyschema)

Get the key schema for the given table ID.

_Reverts if the table ID is not registered._

```
function getKeySchema(ResourceId tableId) internal view returns (Schema keySchema);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table for which to get the key schema.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`keySchema`|`Schema`|The key schema for the given table ID.|

#### getValueSchema[](#getvalueschema)

Get the value schema for the given table ID.

_Reverts if the table ID is not registered._

```
function getValueSchema(ResourceId tableId) internal view returns (Schema valueSchema);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table for which to get the value schema.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`valueSchema`|`Schema`|The value schema for the given table ID.|

#### registerTable[](#registertable)

[Usage Sample](/store/tables#manually-register-a-table)

Register a new table with the given configuration.

*This method reverts if

- The table ID is not of type RESOURCE_TABLE or RESOURCE_OFFCHAIN_TABLE.
- The field layout is invalid.
- The key schema is invalid.
- The value schema is invalid.
- The number of key names does not match the number of key schema types.
- The number of field names does not match the number of field layout fields.*

```
function registerTable(
  ResourceId tableId,
  FieldLayout fieldLayout,
  Schema keySchema,
  Schema valueSchema,
  string[] memory keyNames,
  string[] memory fieldNames
) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to register.|
|`fieldLayout`|`FieldLayout`|The field layout of the table.|
|`keySchema`|`Schema`|The key schema of the table.|
|`valueSchema`|`Schema`|The value schema of the table.|
|`keyNames`|`string[]`|The names of the keys in the table.|
|`fieldNames`|`string[]`|The names of the fields in the table.|

#### registerStoreHook[](#registerstorehook)

[Usage Sample](/store/store-hooks)

REGISTER HOOKS

Register hooks to be called when a record or field is set or deleted.

_This method reverts for all resource IDs other than tables. Hooks are not supported for offchain tables._

```
function registerStoreHook(ResourceId tableId, IStoreHook hookAddress, uint8 enabledHooksBitmap) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to register the hook for.|
|`hookAddress`|`IStoreHook`|The address of the hook contract to register.|
|`enabledHooksBitmap`|`uint8`|The bitmap of enabled hooks.|

#### unregisterStoreHook[](#unregisterstorehook)

Unregister a hook from the given table ID.

```
function unregisterStoreHook(ResourceId tableId, IStoreHook hookAddress) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to unregister the hook from.|
|`hookAddress`|`IStoreHook`|The address of the hook to unregister.|

#### setRecord[](#setrecord)

Set a full record for the given table ID and key tuple.

_Calling this method emits a Store_SetRecord event. This method internally calls another overload of setRecord by fetching the field layout for the given table ID. If the field layout is available to the caller, it is recommended to use the other overload to avoid an additional storage read._

```
function setRecord(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  bytes memory staticData,
  EncodedLengths encodedLengths,
  bytes memory dynamicData
) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to set the record for.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`staticData`|`bytes`|The static data of the record.|
|`encodedLengths`|`EncodedLengths`|The encoded lengths of the dynamic data of the record.|
|`dynamicData`|`bytes`|The dynamic data of the record.|

#### setRecord[](#setrecord-1)

Set a full data record for the given table ID, key tuple, and field layout.

_For onchain tables, the method emits a `Store_SetRecord` event, updates the data in storage, calls `onBeforeSetRecord` hooks before actually modifying the state, and calls `onAfterSetRecord` hooks after modifying the state. For offchain tables, the method returns early after emitting the event without calling hooks or modifying the state._

```
function setRecord(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  bytes memory staticData,
  EncodedLengths encodedLengths,
  bytes memory dynamicData,
  FieldLayout fieldLayout
) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to set the record for.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`staticData`|`bytes`|The static data of the record.|
|`encodedLengths`|`EncodedLengths`|The encoded lengths of the dynamic data of the record.|
|`dynamicData`|`bytes`|The dynamic data of the record.|
|`fieldLayout`|`FieldLayout`|The field layout for the record.|

#### spliceStaticData[](#splicestaticdata)

Splice the static data for the given table ID and key tuple.

_This method emits a `Store_SpliceStaticData` event, updates the data in storage, and calls `onBeforeSpliceStaticData` and `onAfterSpliceStaticData` hooks. For offchain tables, it returns early after emitting the event._

```
function spliceStaticData(ResourceId tableId, bytes32[] memory keyTuple, uint48 start, bytes memory data) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to splice the static data for.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`start`|`uint48`|The start position in bytes for the splice operation.|
|`data`|`bytes`|The data to write to the static data of the record at the start byte.|

#### spliceDynamicData[](#splicedynamicdata)

Splice the dynamic data for the given table ID, key tuple, and dynamic field index.

_This method emits a `Store_SpliceDynamicData` event, updates the data in storage, and calls `onBeforeSpliceDynamicData` and `onAfterSpliceDynamicData` hooks. For offchain tables, it returns early after emitting the event._

```
function spliceDynamicData(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 dynamicFieldIndex,
  uint40 startWithinField,
  uint40 deleteCount,
  bytes memory data
) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to splice the dynamic data for.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`dynamicFieldIndex`|`uint8`|The index of the dynamic field to splice. (Dynamic field index = field index - number of static fields)|
|`startWithinField`|`uint40`|The start position within the field for the splice operation.|
|`deleteCount`|`uint40`|The number of bytes to delete in the splice operation.|
|`data`|`bytes`|The data to insert into the dynamic data of the record at the start byte.|

#### setField[](#setfield)

Set data for a field at the given index in a table with the given tableId, key tuple, and value field layout.

_This method internally calls another overload of setField by fetching the field layout for the given table ID. If the field layout is available to the caller, it is recommended to use the other overload to avoid an additional storage read. This function emits a `Store_SpliceStaticData` or `Store_SpliceDynamicData` event and calls the corresponding hooks. For offchain tables, it returns early after emitting the event._

```
function setField(ResourceId tableId, bytes32[] memory keyTuple, uint8 fieldIndex, bytes memory data) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to set the field for.|
|`keyTuple`|`bytes32[]`|An array representing the key for the record.|
|`fieldIndex`|`uint8`|The index of the field to set.|
|`data`|`bytes`|The data to set for the field.|

#### setField[](#setfield-1)

Set data for a field at the given index in a table with the given tableId, key tuple, and value field layout.

_This method internally calls to `setStaticField` or `setDynamicField` based on the field index and layout. Calling `setStaticField` or `setDynamicField` directly is recommended if the caller is aware of the field layout. This function emits a `Store_SpliceStaticData` or `Store_SpliceDynamicData` event, updates the data in storage, and calls the corresponding hooks. For offchain tables, it returns early after emitting the event._

```
function setField(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 fieldIndex,
  bytes memory data,
  FieldLayout fieldLayout
) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to set the field for.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`fieldIndex`|`uint8`|The index of the field to set.|
|`data`|`bytes`|The data to set for the field.|
|`fieldLayout`|`FieldLayout`|The field layout for the record.|

#### setStaticField[](#setstaticfield)

Set a static field for the given table ID, key tuple, field index, and field layout.

_This method emits a `Store_SpliceStaticData` event, updates the data in storage and calls the `onBeforeSpliceStaticData` and `onAfterSpliceStaticData` hooks. For offchain tables, it returns early after emitting the event._

```
function setStaticField(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 fieldIndex,
  bytes memory data,
  FieldLayout fieldLayout
) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to set the static field for.|
|`keyTuple`|`bytes32[]`|An array representing the key for the record.|
|`fieldIndex`|`uint8`|The index of the field to set.|
|`data`|`bytes`|The data to set for the static field.|
|`fieldLayout`|`FieldLayout`|The field layout for the record.|

#### setDynamicField[](#setdynamicfield)

Set a dynamic field for the given table ID, key tuple, and dynamic field index.

_This method emits a `Store_SpliceDynamicData` event, updates the data in storage and calls the `onBeforeSpliceDynamicaData` and `onAfterSpliceDynamicData` hooks. For offchain tables, it returns early after emitting the event._

```
function setDynamicField(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 dynamicFieldIndex,
  bytes memory data
) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to set the dynamic field for.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`dynamicFieldIndex`|`uint8`|The index of the dynamic field to set. (Dynamic field index = field index - number of static fields).|
|`data`|`bytes`|The data to set for the dynamic field.|

#### deleteRecord[](#deleterecord)

Delete a record for the given table ID and key tuple.

_This method internally calls another overload of deleteRecord by fetching the field layout for the given table ID. This method deletes static data and sets the dynamic data length to 0, but does not actually modify the dynamic data. It emits a `Store_DeleteRecord` event and emits the `onBeforeDeleteRecord` and `onAfterDeleteRecord` hooks. For offchain tables, it returns early after emitting the event._

```
function deleteRecord(ResourceId tableId, bytes32[] memory keyTuple) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to delete the record from.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|

#### deleteRecord[](#deleterecord-1)

Delete a record for the given table ID and key tuple.

_This method deletes static data and sets the dynamic data length to 0, but does not actually modify the dynamic data. It emits a `Store_DeleteRecord` event and emits the `onBeforeDeleteRecord` and `onAfterDeleteRecord` hooks. For offchain tables, it returns early after emitting the event._

```
function deleteRecord(ResourceId tableId, bytes32[] memory keyTuple, FieldLayout fieldLayout) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to delete the record from.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`fieldLayout`|`FieldLayout`|The field layout for the record.|

#### pushToDynamicField[](#pushtodynamicfield)

Push data to a field at the dynamic field index in a table with the given table ID and key tuple.

_This method emits a `Store_SpliceDynamicData` event, updates the data in storage and calls the `onBeforeSpliceDynamicData` and `onAfterSpliceDynamicData` hooks. For offchain tables, it returns early after emitting the event._

```
function pushToDynamicField(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 dynamicFieldIndex,
  bytes memory dataToPush
) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to push data to the dynamic field.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`dynamicFieldIndex`|`uint8`|The index of the dynamic field to push data to.|
|`dataToPush`|`bytes`|The data to push to the dynamic field.|

#### popFromDynamicField[](#popfromdynamicfield)

Pop data from a field at the dynamic field index in a table with the given table ID and key tuple.

_This method emits a `Store_SpliceDynamicData` event, updates the data in storage and calls the `onBeforeSpliceDynamicData` and `onAfterSpliceDynamicData` hooks. For offchain tables, it returns early after emitting the event._

```
function popFromDynamicField(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 dynamicFieldIndex,
  uint256 byteLengthToPop
) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to pop data from the dynamic field.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`dynamicFieldIndex`|`uint8`|The index of the dynamic field to pop data from.|
|`byteLengthToPop`|`uint256`|The byte length to pop from the dynamic field.|

#### getRecord[](#getrecord)

Get the full record (all fields, static and dynamic data) for the given table ID and key tuple.

_This function internally calls another overload of `getRecord`, loading the field layout from storage. If the field layout is available to the caller, it is recommended to use the other overload to avoid an additional storage read._

```
function getRecord(
  ResourceId tableId,
  bytes32[] memory keyTuple
) internal view returns (bytes memory staticData, EncodedLengths encodedLengths, bytes memory dynamicData);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to get the record from.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`staticData`|`bytes`|The static data of the record.|
|`encodedLengths`|`EncodedLengths`|The encoded lengths of the dynamic data of the record.|
|`dynamicData`|`bytes`|The dynamic data of the record.|

#### getRecord[](#getrecord-1)

Get the full record (all fields, static and dynamic data) for the given table ID and key tuple, with the given field layout.

```
function getRecord(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  FieldLayout fieldLayout
) internal view returns (bytes memory staticData, EncodedLengths encodedLengths, bytes memory dynamicData);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to get the record from.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`fieldLayout`|`FieldLayout`|The field layout for the record.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`staticData`|`bytes`|The static data of the record.|
|`encodedLengths`|`EncodedLengths`|The encoded lengths of the dynamic data of the record.|
|`dynamicData`|`bytes`|The dynamic data of the record.|

#### getField[](#getfield)

Get a single field from the given table ID and key tuple.

_This function internally calls another overload of `getField`, loading the field layout from storage._

```
function getField(ResourceId tableId, bytes32[] memory keyTuple, uint8 fieldIndex) internal view returns (bytes memory);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to get the field from.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`fieldIndex`|`uint8`|The index of the field to get.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`bytes`|The data of the field.|

#### getField[](#getfield-1)

Get a single field from the given table ID and key tuple, with the given field layout.

```
function getField(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 fieldIndex,
  FieldLayout fieldLayout
) internal view returns (bytes memory);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to get the field from.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`fieldIndex`|`uint8`|The index of the field to get.|
|`fieldLayout`|`FieldLayout`|The field layout for the record.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`bytes`|The data of the field.|

#### getStaticField[](#getstaticfield)

Get a single static field from the given table ID and key tuple, with the given value field layout.

_The field value is left-aligned in the returned bytes32, the rest of the word is not zeroed out. Consumers are expected to truncate the returned value as needed._

```
function getStaticField(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 fieldIndex,
  FieldLayout fieldLayout
) internal view returns (bytes32);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to get the static field from.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`fieldIndex`|`uint8`|The index of the field to get.|
|`fieldLayout`|`FieldLayout`|The field layout for the record.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`bytes32`|The data of the static field.|

#### getDynamicField[](#getdynamicfield)

Get a single dynamic field from the given table ID and key tuple.

```
function getDynamicField(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 dynamicFieldIndex
) internal view returns (bytes memory);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to get the dynamic field from.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`dynamicFieldIndex`|`uint8`|The index of the dynamic field to get, relative to the start of the dynamic fields. (Dynamic field index = field index - number of static fields)|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`bytes`|The data of the dynamic field.|

#### getFieldLength[](#getfieldlength)

Get the byte length of a single field from the given table ID and key tuple.

_This function internally calls another overload of `getFieldLength`, loading the field layout from storage. If the field layout is available to the caller, it is recommended to use the other overload to avoid an additional storage read._

```
function getFieldLength(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 fieldIndex
) internal view returns (uint256);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to get the field length from.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`fieldIndex`|`uint8`|The index of the field to get the length for.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`uint256`|The byte length of the field.|

#### getFieldLength[](#getfieldlength-1)

Get the byte length of a single field from the given table ID and key tuple.

```
function getFieldLength(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 fieldIndex,
  FieldLayout fieldLayout
) internal view returns (uint256);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to get the field length from.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`fieldIndex`|`uint8`|The index of the field to get the length for.|
|`fieldLayout`|`FieldLayout`|The field layout for the record.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`uint256`|The byte length of the field.|

#### getDynamicFieldLength[](#getdynamicfieldlength)

Get the byte length of a single dynamic field from the given table ID and key tuple.

```
function getDynamicFieldLength(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 dynamicFieldIndex
) internal view returns (uint256);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to get the dynamic field length from.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`dynamicFieldIndex`|`uint8`|The index of the dynamic field to get the length for, relative to the start of the dynamic fields. (Dynamic field index = field index - number of static fields)|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`uint256`|The byte length of the dynamic field.|

#### getDynamicFieldSlice[](#getdynamicfieldslice)

Get a byte slice (including start, excluding end) of a single dynamic field from the given table ID and key tuple.

```
function getDynamicFieldSlice(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 dynamicFieldIndex,
  uint256 start,
  uint256 end
) internal view returns (bytes memory);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to get the dynamic field slice from.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`dynamicFieldIndex`|`uint8`|The index of the dynamic field to get the slice from, relative to the start of the dynamic fields. (Dynamic field index = field index - number of static fields)|
|`start`|`uint256`|The start index within the dynamic field for the slice operation (inclusive).|
|`end`|`uint256`|The end index within the dynamic field for the slice operation (exclusive).|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`bytes`|The byte slice of the dynamic field.|

## StoreCoreInternal[](#storecoreinternal)

[Git Source (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/store/src/StoreCore.sol)

_This library contains internal functions used by StoreCore. They are not intended to be used directly by consumers of StoreCore._

### State Variables[](#state-variables)

#### SLOT[](#slot)

```
bytes32 internal constant SLOT = keccak256("mud.store");
```

#### DYNAMIC_DATA_SLOT[](#dynamic_data_slot)

```
bytes32 internal constant DYNAMIC_DATA_SLOT = keccak256("mud.store.dynamicData");
```

#### DYNAMIC_DATA_LENGTH_SLOT[](#dynamic_data_length_slot)

```
bytes32 internal constant DYNAMIC_DATA_LENGTH_SLOT = keccak256("mud.store.dynamicDataLength");
```

### Functions[](#functions-1)

#### _spliceDynamicData[](#_splicedynamicdata)

Splice dynamic data in the store.

_This function checks various conditions to ensure the operation is valid. It emits a `Store_SpliceDynamicData` event, calls `onBeforeSpliceDynamicData` hooks before actually modifying the storage, and calls `onAfterSpliceDynamicData` hooks after modifying the storage. It reverts with `Store_InvalidResourceType` if the table ID is not a table. (Splicing dynamic data is not supported for offchain tables, as it requires reading the previous encoded lengths from storage.) It reverts with `Store_InvalidSplice` if the splice total length of the field is changed but the splice is not at the end of the field. It reverts with `Store_IndexOutOfBounds` if the start index is larger than the previous length of the field._

```
function _spliceDynamicData(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 dynamicFieldIndex,
  uint40 startWithinField,
  uint40 deleteCount,
  bytes memory data,
  EncodedLengths previousEncodedLengths
) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to splice dynamic data.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`dynamicFieldIndex`|`uint8`|The index of the dynamic field to splice data, relative to the start of the dynamic fields. (Dynamic field index = field index - number of static fields)|
|`startWithinField`|`uint40`|The start index within the field for the splice operation.|
|`deleteCount`|`uint40`|The number of bytes to delete in the splice operation.|
|`data`|`bytes`|The data to insert into the dynamic data of the record at the start byte.|
|`previousEncodedLengths`|`EncodedLengths`|The previous encoded lengths of the dynamic data of the record.|

#### _getStaticData[](#_getstaticdata)

Get full static data for the given table ID and key tuple, with the given length in bytes.

```
function _getStaticData(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint256 length
) internal view returns (bytes memory);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to get the static data from.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`length`|`uint256`|The length of the static data to retrieve.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`bytes`|The full static data of the specified length.|

#### _getStaticFieldBytes[](#_getstaticfieldbytes)

Get a single static field from the given table ID and key tuple, with the given value field layout.

```
function _getStaticFieldBytes(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 fieldIndex,
  FieldLayout fieldLayout
) internal view returns (bytes memory);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to get the static field from.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`fieldIndex`|`uint8`|The index of the field to get.|
|`fieldLayout`|`FieldLayout`|The field layout for the record.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`bytes`|The static field data as dynamic bytes in the size of the field.|

#### _getStaticDataLocation[](#_getstaticdatalocation)

Compute the storage location based on table ID and key tuple.

```
function _getStaticDataLocation(ResourceId tableId, bytes32[] memory keyTuple) internal pure returns (uint256);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`uint256`|The computed storage location based on table ID and key tuple.|

#### _getStaticDataLocation[](#_getstaticdatalocation-1)

Compute the storage location based on table ID and a single key.

```
function _getStaticDataLocation(ResourceId tableId, bytes32 key) internal pure returns (uint256);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table.|
|`key`|`bytes32`|The single key for the record.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`uint256`|The computed storage location based on table ID and key.|

#### _getStaticDataOffset[](#_getstaticdataoffset)

Get storage offset for the given value field layout and index.

```
function _getStaticDataOffset(FieldLayout fieldLayout, uint8 fieldIndex) internal pure returns (uint256);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`fieldLayout`|`FieldLayout`|The field layout for the record.|
|`fieldIndex`|`uint8`|The index of the field to get the offset for.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`uint256`|The storage offset for the specified field layout and index.|

#### _getDynamicDataLocation[](#_getdynamicdatalocation)

Compute the storage location based on table ID, key tuple, and dynamic field index.

```
function _getDynamicDataLocation(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 dynamicFieldIndex
) internal pure returns (uint256);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`dynamicFieldIndex`|`uint8`|The index of the dynamic field, relative to the start of the dynamic fields. (Dynamic field index = field index - number of static fields)|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`uint256`|The computed storage location based on table ID, key tuple, and dynamic field index.|

#### _getDynamicDataLengthLocation[](#_getdynamicdatalengthlocation)

Compute the storage location for the length of the dynamic data based on table ID and key tuple.

```
function _getDynamicDataLengthLocation(ResourceId tableId, bytes32[] memory keyTuple) internal pure returns (uint256);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`uint256`|The computed storage location for the length of the dynamic data based on table ID and key tuple.|

#### _loadEncodedDynamicDataLength[](#_loadencodeddynamicdatalength)

Load the encoded dynamic data length from storage for the given table ID and key tuple.

```
function _loadEncodedDynamicDataLength(
  ResourceId tableId,
  bytes32[] memory keyTuple
) internal view returns (EncodedLengths);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`EncodedLengths`|The loaded encoded dynamic data length from storage for the given table ID and key tuple.|

## Store[](#store)

[Git Source (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/store/src/Store.sol)

**Inherits:** [IStore](/src/IStore.sol/interface.IStore), [StoreKernel](/src/StoreKernel.sol/abstract.StoreKernel)

This contract integrates the core storage functionalities and provides an interface for data storage.

_This abstract contract initializes `StoreCore`, implements `storeVersion`, and read methods, but not write methods._

## StoreRead[](#storeread)

[Git Source (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/store/src/StoreRead.sol)

**Inherits:** [IStoreRead](/store/reference/store#istoreread)

_A contract that provides read operations for storage tables using `StoreCore`._

### Functions[](#functions-2)

#### getFieldLayout[](#getfieldlayout-1)

Fetches the field layout for a given table.

```
function getFieldLayout(ResourceId tableId) public view virtual returns (FieldLayout fieldLayout);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table for which to retrieve the field layout.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`fieldLayout`|`FieldLayout`|The layout of fields in the specified table.|

#### getValueSchema[](#getvalueschema-1)

Retrieves the value schema for a given table.

```
function getValueSchema(ResourceId tableId) public view virtual returns (Schema valueSchema);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`valueSchema`|`Schema`|The schema for values in the specified table.|

#### getKeySchema[](#getkeyschema-1)

Retrieves the key schema for a given table.

```
function getKeySchema(ResourceId tableId) public view virtual returns (Schema keySchema);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`keySchema`|`Schema`|The schema for keys in the specified table.|

#### getRecord[](#getrecord-2)

Fetches a record from a specified table using a provided key tuple.

```
function getRecord(
  ResourceId tableId,
  bytes32[] calldata keyTuple
) public view virtual returns (bytes memory staticData, EncodedLengths encodedLengths, bytes memory dynamicData);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table.|
|`keyTuple`|`bytes32[]`|The tuple used as a key to fetch the record.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`staticData`|`bytes`|The static data of the record.|
|`encodedLengths`|`EncodedLengths`|Encoded lengths of dynamic data.|
|`dynamicData`|`bytes`|The dynamic data of the record.|

#### getRecord[](#getrecord-3)

Fetches a record from a specified table using a provided key tuple and field layout.

```
function getRecord(
  ResourceId tableId,
  bytes32[] calldata keyTuple,
  FieldLayout fieldLayout
) public view virtual returns (bytes memory staticData, EncodedLengths encodedLengths, bytes memory dynamicData);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table.|
|`keyTuple`|`bytes32[]`|The tuple used as a key to fetch the record.|
|`fieldLayout`|`FieldLayout`|The layout of fields to retrieve.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`staticData`|`bytes`|The static data of the record.|
|`encodedLengths`|`EncodedLengths`|Encoded lengths of dynamic data.|
|`dynamicData`|`bytes`|The dynamic data of the record.|

#### getField[](#getfield-2)

Retrieves data for a specified field in a record.

_This overload loads the FieldLayout from storage. If the table's FieldLayout is known to the caller, it should be passed in to the other overload to avoid the storage read._

```
function getField(
  ResourceId tableId,
  bytes32[] calldata keyTuple,
  uint8 fieldIndex
) public view virtual returns (bytes memory data);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table.|
|`keyTuple`|`bytes32[]`|The tuple used as a key to fetch the field.|
|`fieldIndex`|`uint8`|Index of the field to retrieve.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`data`|`bytes`|The data of the specified field.|

#### getField[](#getfield-3)

Retrieves data for a specified field in a record.

```
function getField(
  ResourceId tableId,
  bytes32[] calldata keyTuple,
  uint8 fieldIndex,
  FieldLayout fieldLayout
) public view virtual returns (bytes memory data);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table.|
|`keyTuple`|`bytes32[]`|The tuple used as a key to fetch the field.|
|`fieldIndex`|`uint8`|Index of the field to retrieve.|
|`fieldLayout`|`FieldLayout`|The layout of fields for the retrieval.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`data`|`bytes`|The data of the specified field.|

#### getStaticField[](#getstaticfield-1)

Retrieves data for a specific static (fixed length) field in a record.

```
function getStaticField(
  ResourceId tableId,
  bytes32[] calldata keyTuple,
  uint8 fieldIndex,
  FieldLayout fieldLayout
) public view virtual returns (bytes32 data);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table.|
|`keyTuple`|`bytes32[]`|The tuple used as a key to fetch the static field.|
|`fieldIndex`|`uint8`|Index of the static field to retrieve.|
|`fieldLayout`|`FieldLayout`|The layout of fields for the retrieval.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`data`|`bytes32`|The static data of the specified field.|

#### getDynamicField[](#getdynamicfield-1)

Retrieves data for a specific dynamic (variable length) field in a record.

```
function getDynamicField(
  ResourceId tableId,
  bytes32[] calldata keyTuple,
  uint8 dynamicFieldIndex
) public view virtual returns (bytes memory data);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table.|
|`keyTuple`|`bytes32[]`|The tuple used as a key to fetch the dynamic field.|
|`dynamicFieldIndex`|`uint8`|Index of the dynamic field to retrieve.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`data`|`bytes`|The dynamic data of the specified field.|

#### getFieldLength[](#getfieldlength-2)

Calculates the length of a specified field in a record.

_This overload loads the FieldLayout from storage. If the table's FieldLayout is known to the caller, it should be passed in to the other overload to avoid the storage read._

```
function getFieldLength(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 fieldIndex
) public view virtual returns (uint256);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table.|
|`keyTuple`|`bytes32[]`|The tuple used as a key.|
|`fieldIndex`|`uint8`|Index of the field to measure.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`uint256`|The length of the specified field.|

#### getFieldLength[](#getfieldlength-3)

Calculates the length of a specified field in a record.

```
function getFieldLength(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 fieldIndex,
  FieldLayout fieldLayout
) public view virtual returns (uint256);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table.|
|`keyTuple`|`bytes32[]`|The tuple used as a key.|
|`fieldIndex`|`uint8`|Index of the field to measure.|
|`fieldLayout`|`FieldLayout`|The layout of fields for measurement.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`uint256`|The length of the specified field.|

#### getDynamicFieldLength[](#getdynamicfieldlength-1)

Calculates the length of a specified dynamic (variable length) field in a record.

```
function getDynamicFieldLength(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 dynamicFieldIndex
) public view virtual returns (uint256);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table.|
|`keyTuple`|`bytes32[]`|The tuple used as a key.|
|`dynamicFieldIndex`|`uint8`|Index of the dynamic field to measure.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`uint256`|The length of the specified dynamic field.|

#### getDynamicFieldSlice[](#getdynamicfieldslice-1)

Retrieves a slice of a dynamic (variable length) field.

```
function getDynamicFieldSlice(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 dynamicFieldIndex,
  uint256 start,
  uint256 end
) public view virtual returns (bytes memory);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table.|
|`keyTuple`|`bytes32[]`|The tuple used as a key to fetch the dynamic field slice.|
|`dynamicFieldIndex`|`uint8`|Index of the dynamic field to slice.|
|`start`|`uint256`|The starting position of the slice.|
|`end`|`uint256`|The ending position of the slice.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`bytes`|The sliced data from the specified dynamic field.|

## StoreSwitch[](#storeswitch)

|[Usage Sample](/store/table-libraries#storeswitch)|[Git Source (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/store/src/StoreSwitch.sol)|
|---|---|

This library serves as an interface switch to interact with the store, either by directing calls to itself or to a designated external store.

_The primary purpose is to abstract the storage details, such that the calling function doesn't need to know if it's interacting with its own storage or with an external contract's storage._

### State Variables[](#state-variables-1)

#### STORAGE_SLOT[](#storage_slot)

_Internal constant representing the storage slot used by the library._

```
bytes32 private constant STORAGE_SLOT = keccak256("mud.store.storage.StoreSwitch");
```

### Functions[](#functions-3)

#### _layout[](#_layout)

Gets the storage layout.

```
function _layout() private pure returns (StorageSlotLayout storage layout);
```

**Returns**

|Name|Type|Description|
|---|---|---|
|`layout`|`StorageSlotLayout`|The current storage layout.|

#### getStoreAddress[](#getstoreaddress)

Fetch the store address to be used for data operations. If _storeAddress is zero, it means that it's uninitialized and therefore it's the default (msg.sender).

```
function getStoreAddress() internal view returns (address);
```

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`address`|Address of the store, or `msg.sender` if uninitialized.|

#### setStoreAddress[](#setstoreaddress)

[Usage Sample](/store/table-libraries#storeswitch)

Set the store address for subsequent operations.

_If it stays uninitialized, StoreSwitch falls back to calling store methods on msg.sender._

```
function setStoreAddress(address _storeAddress) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`_storeAddress`|`address`|The address of the external store contract.|

#### registerStoreHook[](#registerstorehook-1)

Register a store hook for a particular table.

```
function registerStoreHook(ResourceId tableId, IStoreHook hookAddress, uint8 enabledHooksBitmap) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|Unique identifier of the table.|
|`hookAddress`|`IStoreHook`|Address of the hook contract.|
|`enabledHooksBitmap`|`uint8`|Bitmap representing the hooks which this contract overrides.|

#### unregisterStoreHook[](#unregisterstorehook-1)

Unregister a previously registered store hook.

```
function unregisterStoreHook(ResourceId tableId, IStoreHook hookAddress) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|Unique identifier of the table.|
|`hookAddress`|`IStoreHook`|Address of the hook contract to be unregistered.|

#### getFieldLayout[](#getfieldlayout-2)

_Fetches the field layout for a specified table._

```
function getFieldLayout(ResourceId tableId) internal view returns (FieldLayout fieldLayout);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table for which to retrieve the field layout.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`fieldLayout`|`FieldLayout`|The layout of the fields in the specified table.|

#### getValueSchema[](#getvalueschema-2)

_Retrieves the value schema for a specified table._

```
function getValueSchema(ResourceId tableId) internal view returns (Schema valueSchema);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table for which to retrieve the value schema.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`valueSchema`|`Schema`|The schema for values in the specified table.|

#### getKeySchema[](#getkeyschema-2)

_Retrieves the key schema for a specified table._

```
function getKeySchema(ResourceId tableId) internal view returns (Schema keySchema);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table for which to retrieve the key schema.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`keySchema`|`Schema`|The schema for keys in the specified table.|

#### registerTable[](#registertable-1)

_Registers a table with specified configurations._

```
function registerTable(
  ResourceId tableId,
  FieldLayout fieldLayout,
  Schema keySchema,
  Schema valueSchema,
  string[] memory keyNames,
  string[] memory fieldNames
) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to register.|
|`fieldLayout`|`FieldLayout`|The layout of the fields for the table.|
|`keySchema`|`Schema`|The schema for keys in the table.|
|`valueSchema`|`Schema`|The schema for values in the table.|
|`keyNames`|`string[]`|Names of keys in the table.|
|`fieldNames`|`string[]`|Names of fields in the table.|

#### setRecord[](#setrecord-2)

_Sets a record in the store._

```
function setRecord(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  bytes memory staticData,
  EncodedLengths encodedLengths,
  bytes memory dynamicData
) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The table's ID.|
|`keyTuple`|`bytes32[]`|Array of key values.|
|`staticData`|`bytes`|Fixed-length fields data.|
|`encodedLengths`|`EncodedLengths`|Encoded lengths for dynamic data.|
|`dynamicData`|`bytes`|Dynamic-length fields data.|

#### spliceStaticData[](#splicestaticdata-1)

_Splices the static (fixed length) data for a given table ID and key tuple, starting at a specific point._

```
function spliceStaticData(ResourceId tableId, bytes32[] memory keyTuple, uint48 start, bytes memory data) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the resource table.|
|`keyTuple`|`bytes32[]`|An array of bytes32 keys identifying the data record.|
|`start`|`uint48`|The position to begin splicing.|
|`data`|`bytes`|The data to splice into the record.|

#### spliceDynamicData[](#splicedynamicdata-1)

_Splices the dynamic data for a given table ID, key tuple, and dynamic field index._

```
function spliceDynamicData(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 dynamicFieldIndex,
  uint40 startWithinField,
  uint40 deleteCount,
  bytes memory data
) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the resource table.|
|`keyTuple`|`bytes32[]`|An array of bytes32 keys identifying the data record.|
|`dynamicFieldIndex`|`uint8`|The index of the dynamic field to splice.|
|`startWithinField`|`uint40`|The position within the dynamic field to start splicing.|
|`deleteCount`|`uint40`|The number of bytes to delete starting from the splice point.|
|`data`|`bytes`|The data to splice into the dynamic field.|

#### setField[](#setfield-2)

_Sets the data for a specific field in a record identified by table ID and key tuple._

```
function setField(ResourceId tableId, bytes32[] memory keyTuple, uint8 fieldIndex, bytes memory data) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the resource table.|
|`keyTuple`|`bytes32[]`|An array of bytes32 keys identifying the data record.|
|`fieldIndex`|`uint8`|The index of the field to set.|
|`data`|`bytes`|The data to set for the field.|

#### setField[](#setfield-3)

_Sets the data for a specific field in a record, considering a specific field layout._

```
function setField(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 fieldIndex,
  bytes memory data,
  FieldLayout fieldLayout
) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the resource table.|
|`keyTuple`|`bytes32[]`|An array of bytes32 keys identifying the data record.|
|`fieldIndex`|`uint8`|The index of the field to set.|
|`data`|`bytes`|The data to set for the field.|
|`fieldLayout`|`FieldLayout`|The layout structure of the field.|

#### setStaticField[](#setstaticfield-1)

_Sets the data for a specific static (fixed length) field in a record, considering a specific field layout._

```
function setStaticField(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 fieldIndex,
  bytes memory data,
  FieldLayout fieldLayout
) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the resource table.|
|`keyTuple`|`bytes32[]`|An array of bytes32 keys identifying the data record.|
|`fieldIndex`|`uint8`|The index of the field to set.|
|`data`|`bytes`|The data to set for the field.|
|`fieldLayout`|`FieldLayout`|The layout structure of the field.|

#### setDynamicField[](#setdynamicfield-1)

_Sets the value of a specific dynamic (variable-length) field in a record._

```
function setDynamicField(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 dynamicFieldIndex,
  bytes memory data
) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to which the record belongs.|
|`keyTuple`|`bytes32[]`|An array representing the key for the record.|
|`dynamicFieldIndex`|`uint8`|The index of the dynamic field to set.|
|`data`|`bytes`|The data to set for the field.|

#### pushToDynamicField[](#pushtodynamicfield-1)

_Appends data to a specific dynamic (variable length) field of a record._

```
function pushToDynamicField(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 dynamicFieldIndex,
  bytes memory dataToPush
) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to which the record belongs.|
|`keyTuple`|`bytes32[]`|An array representing the key for the record.|
|`dynamicFieldIndex`|`uint8`|The index of the dynamic field.|
|`dataToPush`|`bytes`|The data to append to the field.|

#### popFromDynamicField[](#popfromdynamicfield-1)

_Removes data from the end of a specific dynamic (variable length) field of a record._

```
function popFromDynamicField(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 dynamicFieldIndex,
  uint256 byteLengthToPop
) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to which the record belongs.|
|`keyTuple`|`bytes32[]`|An array representing the key for the record.|
|`dynamicFieldIndex`|`uint8`|The index of the dynamic field.|
|`byteLengthToPop`|`uint256`|The number of bytes to remove from the end of the field.|

#### deleteRecord[](#deleterecord-2)

_Deletes a record from a table._

```
function deleteRecord(ResourceId tableId, bytes32[] memory keyTuple) internal;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to which the record belongs.|
|`keyTuple`|`bytes32[]`|An array representing the key for the record.|

#### getRecord[](#getrecord-4)

_Retrieves a record from a table._

```
function getRecord(
  ResourceId tableId,
  bytes32[] memory keyTuple
) internal view returns (bytes memory, EncodedLengths, bytes memory);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to which the record belongs.|
|`keyTuple`|`bytes32[]`|An array representing the key for the record.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`bytes`|staticData The static data of the record.|
|`<none>`|`EncodedLengths`|encodedLengths Encoded lengths of dynamic data.|
|`<none>`|`bytes`|dynamicData The dynamic data of the record.|

#### getRecord[](#getrecord-5)

_Retrieves a record from a table with a specific layout._

```
function getRecord(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  FieldLayout fieldLayout
) internal view returns (bytes memory, EncodedLengths, bytes memory);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to which the record belongs.|
|`keyTuple`|`bytes32[]`|An array representing the key for the record.|
|`fieldLayout`|`FieldLayout`|The layout of the fields in the record.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`bytes`|staticData The static data of the record.|
|`<none>`|`EncodedLengths`|encodedLengths Encoded lengths of dynamic data.|
|`<none>`|`bytes`|dynamicData The dynamic data of the record.|

#### getField[](#getfield-4)

_Retrieves a specific field from a record._

```
function getField(ResourceId tableId, bytes32[] memory keyTuple, uint8 fieldIndex) internal view returns (bytes memory);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to which the record belongs.|
|`keyTuple`|`bytes32[]`|An array representing the key for the record.|
|`fieldIndex`|`uint8`|The index of the field to retrieve.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`bytes`|Returns the data of the specified field.|

#### getField[](#getfield-5)

_Retrieves a specific field from a record with a given layout._

```
function getField(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 fieldIndex,
  FieldLayout fieldLayout
) internal view returns (bytes memory);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to which the record belongs.|
|`keyTuple`|`bytes32[]`|An array representing the key for the record.|
|`fieldIndex`|`uint8`|The index of the field to retrieve.|
|`fieldLayout`|`FieldLayout`|The layout of the field being retrieved.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`bytes`|Returns the data of the specified field.|

#### getStaticField[](#getstaticfield-2)

_Retrieves a specific static (fixed length) field from a record with a given layout._

```
function getStaticField(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 fieldIndex,
  FieldLayout fieldLayout
) internal view returns (bytes32);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to which the record belongs.|
|`keyTuple`|`bytes32[]`|An array representing the key for the record.|
|`fieldIndex`|`uint8`|The index of the static field to retrieve.|
|`fieldLayout`|`FieldLayout`|The layout of the static field being retrieved.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`bytes32`|Returns the data of the specified static field.|

#### getDynamicField[](#getdynamicfield-2)

_Retrieves a specific dynamic (variable length) field from a record._

```
function getDynamicField(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 dynamicFieldIndex
) internal view returns (bytes memory);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to which the record belongs.|
|`keyTuple`|`bytes32[]`|An array representing the key for the record.|
|`dynamicFieldIndex`|`uint8`|The index of the dynamic field to retrieve.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`bytes`|Returns the data of the specified dynamic field.|

#### getFieldLength[](#getfieldlength-4)

_Retrieves the length of a specific field in a record._

```
function getFieldLength(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 fieldIndex
) internal view returns (uint256);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to which the record belongs.|
|`keyTuple`|`bytes32[]`|An array representing the key for the record.|
|`fieldIndex`|`uint8`|The index of the field whose length is to be retrieved.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`uint256`|Returns the length of the specified field.|

#### getFieldLength[](#getfieldlength-5)

_Retrieves the length of a specific field in a record with a given layout._

```
function getFieldLength(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 fieldIndex,
  FieldLayout fieldLayout
) internal view returns (uint256);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to which the record belongs.|
|`keyTuple`|`bytes32[]`|An array representing the key for the record.|
|`fieldIndex`|`uint8`|The index of the field whose length is to be retrieved.|
|`fieldLayout`|`FieldLayout`|The layout of the field whose length is to be retrieved.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`uint256`|Returns the length of the specified field.|

#### getDynamicFieldLength[](#getdynamicfieldlength-2)

_Retrieves the length of a specific dynamic (variable length) field in a record._

```
function getDynamicFieldLength(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 dynamicFieldIndex
) internal view returns (uint256);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to which the record belongs.|
|`keyTuple`|`bytes32[]`|An array representing the key for the record.|
|`dynamicFieldIndex`|`uint8`|The index of the dynamic field whose length is to be retrieved.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`uint256`|Returns the length of the specified dynamic field.|

#### getDynamicFieldSlice[](#getdynamicfieldslice-2)

_Retrieves a slice of a dynamic (variable length) field from a record._

```
function getDynamicFieldSlice(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 dynamicFieldIndex,
  uint256 start,
  uint256 end
) internal view returns (bytes memory);
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table to which the record belongs.|
|`keyTuple`|`bytes32[]`|An array representing the key for the record.|
|`dynamicFieldIndex`|`uint8`|The index of the dynamic field from which to get the slice.|
|`start`|`uint256`|The starting index of the slice.|
|`end`|`uint256`|The ending index of the slice.|

**Returns**

|Name|Type|Description|
|---|---|---|
|`<none>`|`bytes`|Returns the sliced data from the specified dynamic field.|

### Structs[](#structs)

#### StorageSlotLayout[](#storageslotlayout)

_Represents the layout of the storage slot (currently just the address)_

```
struct StorageSlotLayout {
  address storeAddress;
}
```

[Store hooks](/store/store-hooks "Store hooks")[IStore (external)](/store/reference/store "IStore (external)")

---

MIT 2023 © MUD

Store Core – MUD