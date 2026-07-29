# Key Functions

## Overview

This section describes the current SDK APIs related to key layout and per-key configuration.

## Get Keyboard Default Key Layout

ServiceKeyboard.getDefaultKeyLayout()

Brief: Gets the keyboard default key layout.

### Parameters

This method does not require parameters.

### Returns

- Overall type: `Promise<any>`
- Description: Returns the default key layout data. The exact structure depends on the device layout definition.

### Example

```javascript
const layout = await ServiceKeyboard.getDefaultKeyLayout();
console.log('Default key layout:', layout);
```

## Get Key Custom Layer

ServiceKeyboard.getKeyCustomLayer(layer, options?)

Brief: Gets the custom-function mapping of keys for the specified layer. Currently supports the base layer, FN1, FN2, and FN3 layers.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| layer | number | Layer value. Base layer: `0x00`, FN1 layer: `0x01`, FN2 layer: `0x02`, FN3 layer: `0x03` | Yes |
| options | object | Optional image asset options. Use `includeImages: false` to return empty image fields; use `verifyImages: true` only when the SDK should request image assets to verify accessibility | No |

### Returns

- Overall type: `Promise<{ code: number, layer: number, list: Array<{ hid: string, type: string, name: string, content: string, image: string, fn_code?: string }>, layout: any[] }>`
- Description: Returns the custom-function list for the specified layer together with the current key layout mapping. `image` is the icon resource URL for the custom function; if no icon is available for an item or `{ includeImages: false }` is passed, this field is an empty string. By default, the SDK only returns URLs and does not proactively load images; it requests image assets only when `{ verifyImages: true }` is passed.

### Example

```javascript
const result = await ServiceKeyboard.getKeyCustomLayer(0x00);
console.log('Base layer custom functions:', result);

const resultWithoutImages = await ServiceKeyboard.getKeyCustomLayer(0x00, {
  includeImages: false,
});
```

## Get Custom Hotkey CODE

ServiceKeyboard.getHotKeyCodeCustom(hotKey)

Brief: Gets the protocol CODE from a custom hotkey combination. This API is a client-side helper and supports one or multiple modifier keys.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| hotKey | string | Hotkey combination, such as `Ctrl+C`, `Ctrl+Shift+S`, or `Ctrl+Alt+Delete`. Supported modifiers are `Ctrl`, `Shift`, `Alt`, and `Win` | Yes |

### Returns

- Overall type: `Promise<{ code: number, hotKey: string, keyName: string, modifiers: string[], shortcutCode: string, id: number, commandType: number, key: string, modifier: string, config: { id: string, type: string } }>`
- Description: `shortcutCode` is the displayable protocol CODE such as `0x06,0x61`; `id` is the numeric HID value for the normal key; `commandType` is the hotkey modifier protocol value; `config` can be passed directly as the third parameter of `setKeyConfig`.

### Example

```javascript
const hotKeyCode = await ServiceKeyboard.getHotKeyCodeCustom('Ctrl+Alt+Delete');
console.log('Custom hotkey CODE:', hotKeyCode.shortcutCode);
await ServiceKeyboard.setKeyConfig('A', '0x00', hotKeyCode.config);
```

## Set Key Config

ServiceKeyboard.setKeyConfig(keyName, layer, config)

Brief: Sets the function configuration for a specific key on a specific layer.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| keyName | string | Key name, for example `A`, `B`, `Esc`, or `Space`. The SDK resolves the HID from the key name automatically | Yes |
| layer | number | Layer value. Base layer: `0x00`, FN1 layer: `0x01`, FN2 layer: `0x02`, FN3 layer: `0x03` | Yes |
| config | object | Key configuration object. `config.id` is the concrete function value and `config.type` is the modifier of that value. Refer to the protocol table for specific function description values | Yes |

### Returns

- Overall type: `Promise<any>`
- Description: Returns the applied key configuration result.

### Example

```javascript
const result = await ServiceKeyboard.setKeyConfig('A', 0x00, {
	id: 0x05,
	type: 0x00
});
console.log('Set key result:', result);
```

## Get Key Group

ServiceKeyboard.getKeyGroup()

Brief description: Returns the full key-group mapping (Layout 0xFF). All keys default to group 0.

### Parameters

This method requires no parameters.

### Returns

- Overall type: `Promise<{ code: number, total: number, details: Array<{ row: number, col: number, keyName: string, hid: string, group: number }>, layout: Array<Array<...>> }>`
- Description: `details` is a flat array of key-group entries; `layout` preserves the keyboard row/column structure. Each key includes a `group` field with its group number.

### Example

```javascript
const groupResult = await ServiceKeyboard.getKeyGroup();
console.log('Key groups:', groupResult);
console.log('Keys in group 0:', groupResult.details.filter(k => k.group === 0).length);
```

## Set Key Group

ServiceKeyboard.setKeyGroup(params)

Brief description: Assigns a group number to specified keys (Layout 0xFF). Supports single-key and batch modes.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| keyName | string | Key name. Used with `group` in single-key mode | Yes (single) |
| hid | string | Key HID, e.g. `0x04`. Optional in single-key mode | No |
| group | number | Group number. Default 0 | No |
| keys | array | Batch mode: array of key objects, each with `keyName` and optional `hid`, `group` | Yes (batch) |

### Returns

- Single-key mode: `Promise<{ code: number, keyName: string, hid: string, group: number }>`
- Batch mode: `Promise<{ code: number, batch: true, total: number, details: Array<{ keyName: string, hid: string, group: number }> }>`

### Example

```javascript
// Single key
await ServiceKeyboard.setKeyGroup({ keyName: 'A', group: 1 });

// Batch
await ServiceKeyboard.setKeyGroup({
  keys: [
    { keyName: 'A', group: 1 },
    { keyName: 'B', group: 1 },
    { keyName: 'C', group: 2 }
  ]
});
```


## Get RT Key Group

ServiceKeyboard.getRtKeyGroup()

Brief description: Get the RT group assignment for all keys. Protocol function code `0xFE`, default is group 0 globally.

### Parameters

This method requires no parameters.

### Return Value

- Overall type: `Promise<{ code: number, total: number, details: Array<{ row, col, keyName, hid, group }>, layout: Array<Array<...>> }>`

### Example

```javascript
const result = await ServiceKeyboard.getRtKeyGroup();
console.log('RT group total:', result.total);
```

## Set RT Key Group

ServiceKeyboard.setRtKeyGroup(params)

Brief description: Set the RT group number for keys. Supports single-key and batch modes. Protocol function code `0xFE`.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| keyName | string | Key name (single-key mode) | No |
| group | number | RT group number, default `0` | No |
| keys | `Array<{ keyName?, group? }>` | Batch mode: key array | No |

### Return Value

- Single-key: `Promise<{ code, keyName, hid, group }>`
- Batch: `Promise<{ code, batch: true, total, details }>`

### Example

```javascript
// Single key
await ServiceKeyboard.setRtKeyGroup({ keyName: 'A', group: 1 });

// Batch
await ServiceKeyboard.setRtKeyGroup({
  keys: [
    { keyName: 'A', group: 2 },
    { keyName: 'S', group: 2 },
    { keyName: 'D', group: 2 }
  ]
});
```

## Recover Key Config

ServiceKeyboard.recoverKeyConfig(keyName, layer)

Brief: Restores the factory function for a specific key on the specified layer.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| keyName | string | Key name, for example `A`, `B`, `Esc`, or `Space`. The SDK resolves the HID from the key name automatically | Yes |
| layer | number | Layer value. Base layer: `0x00`, FN1 layer: `0x01`, FN2 layer: `0x02`, FN3 layer: `0x03` | Yes |

### Returns

- Overall type: `Promise<{ code: number, keyName: string, hid: string, layer: number, recoverTarget: number }>`
- Description: Returns the recovery result with the key name, HID, layer, and recovery target type.

### Example

```javascript
// Recover key 'A' on base layer
const result = await ServiceKeyboard.recoverKeyConfig('A', 0x00);
console.log('Recover result:', result);

// Recover key 'A' on FN1 layer
await ServiceKeyboard.recoverKeyConfig('A', 0x01);
```

## Recover All Key Config

ServiceKeyboard.recoverAllKeyConfig(layer)

Brief: Restores the factory function for all keys on the specified layer.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| layer | number | Layer value. Base layer: `0x00`, FN1 layer: `0x01`, FN2 layer: `0x02`, FN3 layer: `0x03` | Yes |

### Returns

- Overall type: `Promise<{ code: number, layer: number, recoverTarget: number }>`
- Description: Returns the recovery result with the layer and recovery target type.

### Example

```javascript
// Recover all keys on base layer
const result = await ServiceKeyboard.recoverAllKeyConfig(0x00);
console.log('Recover all result:', result);

// Recover all keys on FN1 layer
await ServiceKeyboard.recoverAllKeyConfig(0x01);
```