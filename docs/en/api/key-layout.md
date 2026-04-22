# Key Layout & Shortcuts

## Overview

This category groups the newer client-side SDK capabilities around layout rendering, layer mapping, and shortcut catalogs. Unlike the per-key write APIs, these methods are mainly used to build keyboard editors, render key maps, and populate function pickers on the frontend.

Current APIs in this category:

- `ServiceKeyboard.getKeyCustomLayer(layer)`
- `ServiceKeyboard.getStandard104KeyLayout()`
- `ServiceKeyboard.getShortcutFunctionList(type)`

## Get Key Custom Layer

ServiceKeyboard.getKeyCustomLayer(layer)

Brief: Returns the custom-function mapping for a specific layer. Supported layers are base, FN1, FN2, and FN3.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| layer | number | Layer value. Base: `0x00`, FN1: `0x01`, FN2: `0x02`, FN3: `0x03` | Yes |

### Returns

- Overall type: `Promise<{ code: number, layer: number, list: Array<{ hid: string, type: string, name: string, content: string, image: string, fn_code?: string }>, layout: any[] }>`

- Notes:

- `list` is the flattened function dataset.
- `layout` is ready for keyboard-map rendering.
- `image` is the icon URL for the current custom function, or an empty string if unavailable.

### Example

```javascript
const baseLayer = await ServiceKeyboard.getKeyCustomLayer(0x00);
console.log('Base layer mapping:', baseLayer);
```

## Get Standard 104-Key Layout

ServiceKeyboard.getStandard104KeyLayout()

Brief: Returns the standard 104-key keyboard layout. This is a client helper and does not require a device round trip.

### Returns

- Overall type: `Promise<{ code: number, total: number, rows: Array<Array<{ row: number, col: number, hid: string, name: string, value: string, vkCode?: number, scanCode?: number }>>, list: Array<{ row: number, col: number, hid: string, name: string, value: string, vkCode?: number, scanCode?: number }> }>`

### Example

```javascript
const layout104 = await ServiceKeyboard.getStandard104KeyLayout();
console.log('Standard 104-key rows:', layout104.rows);
```

## Get Shortcut Function List

ServiceKeyboard.getShortcutFunctionList(type)

Brief: Returns the shortcut catalog for a specific type. This method is also implemented as a client-side helper.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| type | string | Only `system`, `mouse`, `media`, and `firmware` are supported | Yes |

### Returns

- Overall type: `Promise<{ code: number, type: string, total: number, list: Array<{ index: number, type: string, name: string, code: string, id: number, commandType: number, image: string }> }>`

- Notes:

- `code` is the original protocol value.
- `id` and `commandType` are parsed numeric fields for frontend usage.
- `image` is the icon URL, or an empty string when no icon exists.

### Example

```javascript
const mediaShortcuts = await ServiceKeyboard.getShortcutFunctionList('media');
console.log('Media shortcuts:', mediaShortcuts.list);
```

## Recommendations

TIP

- Use `getStandard104KeyLayout()` or the real device layout as the rendering base, then merge `getKeyCustomLayer()` data into your editor UI.
- Cache `getShortcutFunctionList()` by type when building selection panels to avoid repeated client-side reconstruction.
- For actual key writes, use this page together with [Key Functions](./key.md).