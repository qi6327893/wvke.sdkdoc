# RT Key Group

## Overview

Set and get RT (Rapid Trigger) key groups. Protocol function code `0xFE`, format is `HID + group number` (e.g., `04,00` means key with HID=0x04 belongs to group 0). Default is group 0 for all keys globally. Supports single-key and batch modes.

## Get RT Key Group

keyboard.getRtKeyGroup()

Brief description: Get the RT group assignment for all keys on the keyboard.

### Parameters

This method requires no parameters.

### Return Value

| Field | Type | Description |
|------|------|------|
| code | number | Status code, `0` indicates success |
| total | number | Total valid key count |
| details | `Array<{ row, col, keyName, hid, group }>` | RT group details per key |
| layout | `Array<Array<{ row, col, keyName, hid, group }>>` | RT group info in keyboard matrix layout |

### Example

```javascript
const result = await keyboard.getRtKeyGroup();
console.log('RT group total:', result.total);
result.details.forEach(key => {
  console.log(`${key.keyName} → Group ${key.group}`);
});
```

## Set RT Key Group

keyboard.setRtKeyGroup(params)

Brief description: Set the RT group number for keys. Supports single-key and batch modes.

### Parameters

| Parameter | Type | Description |
|------|------|------|
| keyName | `string` | Key name, e.g., `'A'` (single-key mode) |
| hid | `string` | Key HID, e.g., `'0x04'` |
| group | `number` | RT group number, default `0` |
| keys | `Array<{ keyName?, hid?, group? }>` | Batch mode: array of keys, each with optional group |

### Return Value

Single-key mode:

| Field | Type | Description |
|------|------|------|
| code | number | Status code |
| keyName | string | Key name |
| hid | string | Key HID |
| group | number | Assigned group number |

Batch mode:

| Field | Type | Description |
|------|------|------|
| code | number | Status code |
| batch | boolean | `true` |
| total | number | Total keys set |
| details | `Array<{ keyName, hid, group }>` | Result per key |

### Example

```javascript
// Single-key mode: assign key A to RT group 1
await keyboard.setRtKeyGroup({ keyName: 'A', group: 1 });

// Batch mode: assign A, S, D to RT group 2
await keyboard.setRtKeyGroup({
  keys: [
    { keyName: 'A', group: 2 },
    { keyName: 'S', group: 2 },
    { keyName: 'D', group: 2 }
  ]
});

// Batch mode: uniform group
await keyboard.setRtKeyGroup({
  keys: [{ keyName: 'A' }, { keyName: 'D' }],
  group: 1
});
```
