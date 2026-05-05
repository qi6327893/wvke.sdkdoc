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

ServiceKeyboard.getKeyCustomLayer(layer)

Brief: Gets the custom-function mapping of keys for the specified layer. Currently supports the base layer, FN1, FN2, and FN3 layers.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| layer | number | Layer value. Base layer: `0x00`, FN1 layer: `0x01`, FN2 layer: `0x02`, FN3 layer: `0x03` | Yes |

### Returns

- Overall type: `Promise<{ code: number, layer: number, list: Array<{ hid: string, type: string, name: string, content: string, image: string, fn_code?: string }>, layout: any[] }>`
- Description: Returns the custom-function list for the specified layer together with the current key layout mapping. `image` is the icon resource URL for the custom function; if no icon is available for an item, this field is an empty string.

### Example

```javascript
const result = await ServiceKeyboard.getKeyCustomLayer(0x00);
console.log('Base layer custom functions:', result);
```

## Get Standard 104-Key Keyboard Layout

ServiceKeyboard.getStandard104KeyLayout()

Brief: Gets the standard 104-key keyboard layout. This API is a client-side helper and does not depend on a device round trip.

### Parameters

This method does not require parameters.

### Returns

- Overall type: `Promise<{ total: number, rows: Array<Array<{ row: number, col: number, hid: string, name: string, value: string, vkCode?: number, scanCode?: number }>>, list: Array<{ row: number, col: number, hid: string, name: string, value: string, vkCode?: number, scanCode?: number }> }>`
- Description: Returns both row-based layout data and a flat list for the standard 104-key keyboard, which is useful for client-side key pickers and layout rendering.

### Example

```javascript
const layout104 = await ServiceKeyboard.getStandard104KeyLayout();
console.log('Standard 104-key layout:', layout104);
```

## Get Shortcut Function List

ServiceKeyboard.getShortcutFunctionList(type)

Brief: Gets the shortcut-function list for the specified type. This API is a client-side helper.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| type | string | Shortcut type. Only `system`, `mouse`, `media`, `firmware`, and `hotkey` are supported | Yes |

### Returns

- Overall type: `Promise<{ type: string, total: number, list: Array<{ index: number, type: string, name: string, code: string, task: string, id: number, commandType: number, image: string }> }>`
- Description: Returns the shortcut-function list for the given type. `code` is the original protocol code, `task` is the hotkey combination for `hotkey` items, `id` and `commandType` are parsed numeric fields, and `image` is the icon resource URL. If no icon is available for an item, this field is an empty string.

### Example

```javascript
const shortcuts = await ServiceKeyboard.getShortcutFunctionList('system');
console.log('System shortcut function list:', shortcuts);
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