

## IStoreHook[](#istorehook)

|[Usage Sample](/store/store-hooks#store-hook-contract)|[Git Source (opens in a new tab)](https://github.com/latticexyz/mud/blob/main/packages/store/src/IStoreHook.sol)|
|---|---|

**Inherits:** [IERC165](/world/reference/internal/erc165-external#ierc165)

### Functions[](#functions)

#### onBeforeSetRecord[](#onbeforesetrecord)

Called before setting a record in the store.

```
function onBeforeSetRecord(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  bytes memory staticData,
  EncodedLengths encodedLengths,
  bytes memory dynamicData,
  FieldLayout fieldLayout
) external;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table where the record is to be set.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`staticData`|`bytes`|The static data of the record.|
|`encodedLengths`|`EncodedLengths`|The encoded lengths of the dynamic data of the record.|
|`dynamicData`|`bytes`|The dynamic data of the record.|
|`fieldLayout`|`FieldLayout`|The layout of the field, see FieldLayout.sol.|

#### onAfterSetRecord[](#onaftersetrecord)

Called after setting a record in the store.

```
function onAfterSetRecord(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  bytes memory staticData,
  EncodedLengths encodedLengths,
  bytes memory dynamicData,
  FieldLayout fieldLayout
) external;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table where the record was set.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`staticData`|`bytes`|The static data of the record.|
|`encodedLengths`|`EncodedLengths`|The encoded lengths of the dynamic data of the record.|
|`dynamicData`|`bytes`|The dynamic data of the record.|
|`fieldLayout`|`FieldLayout`|The layout of the field, see FieldLayout.sol.|

#### onBeforeSpliceStaticData[](#onbeforesplicestaticdata)

Called before splicing static data in the store.

_Splice operations in static data always overwrite data starting at the start position, so the total length of the data remains the same and no data is shifted._

```
function onBeforeSpliceStaticData(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint48 start,
  bytes memory data
) external;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table where the data is to be spliced.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`start`|`uint48`|The start byte position for splicing.|
|`data`|`bytes`|The data to be written to the static data of the record at the start byte.|

#### onAfterSpliceStaticData[](#onaftersplicestaticdata)

Called after splicing static data in the store.

_Splice operations in static data always overwrite data starting at the start position, so the total length of the data remains the same and no data is shifted._

```
function onAfterSpliceStaticData(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint48 start,
  bytes memory data
) external;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table where the data was spliced.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`start`|`uint48`|The start byte position for splicing.|
|`data`|`bytes`|The data written to the static data of the record at the start byte.|

#### onBeforeSpliceDynamicData[](#onbeforesplicedynamicdata)

Called before splicing dynamic data in the store.

_Splice operations in dynamic data always reach the end of the dynamic data to avoid shifting data after the inserted or deleted data._

```
function onBeforeSpliceDynamicData(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 dynamicFieldIndex,
  uint40 startWithinField,
  uint40 deleteCount,
  EncodedLengths encodedLengths,
  bytes memory data
) external;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table where the data is to be spliced.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`dynamicFieldIndex`|`uint8`|The index of the dynamic field.|
|`startWithinField`|`uint40`|The start byte position within the field for splicing.|
|`deleteCount`|`uint40`|The number of bytes to delete in the dynamic data of the record.|
|`encodedLengths`|`EncodedLengths`|The encoded lengths of the dynamic data of the record.|
|`data`|`bytes`|The data to be inserted into the dynamic data of the record at the start byte.|

#### onAfterSpliceDynamicData[](#onaftersplicedynamicdata)

Called after splicing dynamic data in the store.

_Splice operations in dynamic data always reach the end of the dynamic data to avoid shifting data after the inserted or deleted data._

```
function onAfterSpliceDynamicData(
  ResourceId tableId,
  bytes32[] memory keyTuple,
  uint8 dynamicFieldIndex,
  uint40 startWithinField,
  uint40 deleteCount,
  EncodedLengths encodedLengths,
  bytes memory data
) external;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table where the data was spliced.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`dynamicFieldIndex`|`uint8`|The index of the dynamic field.|
|`startWithinField`|`uint40`|The start byte position within the field for splicing.|
|`deleteCount`|`uint40`|The number of bytes deleted in the dynamic data of the record.|
|`encodedLengths`|`EncodedLengths`|The encoded lengths of the dynamic data of the record.|
|`data`|`bytes`|The data inserted into the dynamic data of the record at the start byte.|

#### onBeforeDeleteRecord[](#onbeforedeleterecord)

Called before deleting a record from the store.

```
function onBeforeDeleteRecord(ResourceId tableId, bytes32[] memory keyTuple, FieldLayout fieldLayout) external;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table where the record is to be deleted.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`fieldLayout`|`FieldLayout`|The layout of the field, see FieldLayout.sol.|

#### onAfterDeleteRecord[](#onafterdeleterecord)

Called after deleting a record from the store.

```
function onAfterDeleteRecord(ResourceId tableId, bytes32[] memory keyTuple, FieldLayout fieldLayout) external;
```

**Parameters**

|Name|Type|Description|
|---|---|---|
|`tableId`|`ResourceId`|The ID of the table where the record was deleted.|
|`keyTuple`|`bytes32[]`|An array representing the composite key for the record.|
|`fieldLayout`|`FieldLayout`|The layout of the field, see FieldLayout.sol.|

### Errors[](#errors)

#### StoreHook_NotImplemented[](#storehook_notimplemented)

Error emitted when a function is not implemented.

```
error StoreHook_NotImplemented();
```

[IStore (external)](/store/reference/store "IStore (external)")[Miscellaneous](/store/reference/misc "Miscellaneous")

---

MIT 2023 © MUD