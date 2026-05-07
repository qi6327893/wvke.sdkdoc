# Feature Data API

## Overview

This category groups client-side SDK data capabilities around standard keyboard layout data, shortcut catalogs, and switch catalogs that can be cached and rendered directly by the client. Unlike the per-key write APIs, these methods are mainly used to build keyboard editors, render key maps, and populate function pickers on the frontend.

Current APIs in this category:

- `ServiceKeyboard.getStandard104KeyLayout()`
- `ServiceKeyboard.getShortcutFunctionList(type)`
- `ServiceKeyboard.getFineTuneAxisList()`
- `ServiceKeyboard.getKeyboardBacklightEffectList()`
- `ServiceKeyboard.getAmbientLightEffectList()`

## Get Supported Keyboard Backlight Effect List (Client-side)

ServiceKeyboard.getKeyboardBacklightEffectList()

Brief description: Return the built-in keyboard backlight main modes and sub-mode list supported by the current SDK. This is useful for rendering client-side selectors, lighting panels, or mode lookup tables.

### Parameters

This method does not require any parameters.

### Return Details

Returned field meanings:

• `total`: total number of supported backlight main modes
• `list`: supported backlight effect list

Each item in `list` contains:

• `mode`: backlight main mode value
• `label`: backlight main mode label
• `childModes`: supported sub-mode list for the current main mode

Each item in `childModes` contains:

• `value`: sub-mode value, starting from `1`
• `label`: sub-mode label

### Example

```javascript
const backlightEffects = await ServiceKeyboard.getKeyboardBacklightEffectList();
console.log('Supported keyboard backlight effects:', backlightEffects);
```

## Get Supported Ambient Lighting Effect List (Client-side)

ServiceKeyboard.getAmbientLightEffectList()

Brief description: Return the built-in ambient-light / logo-light main mode list supported by the current SDK. This is useful for directly rendering a mode selector on the client side.

### Parameters

This method does not require any parameters.

### Return Details

Returned field meanings:

• `total`: total number of supported ambient-light main modes
• `list`: supported ambient-light effect list

Each item in `list` contains:

• `mode`: ambient-light main mode value
• `label`: ambient-light main mode label
• `alias`: protocol alias, such as `L0` through `L9`

### Example

```javascript
const ambientEffects = await ServiceKeyboard.getAmbientLightEffectList();
console.log('Supported ambient lighting effects:', ambientEffects);
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
| type | string | Only `system`, `mouse`, `media`, `firmware`, `hotkey`, and `gamepad` are supported | Yes |

### Returns

- Overall type: `Promise<{ code: number, type: string, total: number, list: Array<{ index: number, type: string, name: string, code: string, task: string, id: string, commandType: string, image: string }> }>`

- Notes:

- `code` is the original protocol value.
- For `gamepad` items, `code` still uses the existing `id,type` string format. For example, `0xE000` from the protocol sheet is split into `0x00,0xE0`, where the low byte is the function value and the high byte is the controller type value.
- `task` is the hotkey combination and is populated for `hotkey` items.
- `id` and `commandType` are parsed hexadecimal strings in the `0x00` form and can be passed directly to `setKeyConfig()`.
- `image` is the icon URL, or an empty string when no icon exists.

### Example

```javascript
const mediaShortcuts = await ServiceKeyboard.getShortcutFunctionList('media');
console.log('Media shortcuts:', mediaShortcuts.list);
```

## Get Fine-Tunable Switch List

ServiceKeyboard.getFineTuneAxisList()

Brief: Returns the switch catalog currently supported for fine tuning. This is a driver-client data helper and is suitable for switch pickers, switch information panels, and cached frontend datasets.

### Parameters

This method does not require parameters.

### Returns

- Overall type: `Promise<{ code: number, total: number, list: Array<{ axis: number, brand: string, name: string, model: string, axisRangeMax: number, axisCoefficient: number, images: { icon: string, z_b: string, z_y: string } }> }>`

- Notes:

- `axis` is the switch index and can be used as the `axis` field for `setKeyCustomAxis(params)`.
- `brand`, `name`, and `model` are suitable for client-side switch display.
- `axisRangeMax` and `axisCoefficient` are default switch parameters and can be used as reference values for custom switch configuration.
- `images` contains switch icon and structure image URLs.

### Example

```javascript
const fineTuneAxisList = await ServiceKeyboard.getFineTuneAxisList();
console.log('Fine-tunable switches:', fineTuneAxisList.list);
```

## Recommendations

TIP

- Use `getStandard104KeyLayout()` as the base standard-keyboard layout data for editor UI rendering.
- Cache `getShortcutFunctionList()` by type when building selection panels to avoid repeated client-side reconstruction, including `gamepad` catalogs when controller mapping is enabled.
- Cache `getFineTuneAxisList()` when building switch pickers.
- Cache `getKeyboardBacklightEffectList()` and `getAmbientLightEffectList()` when building lighting selectors instead of hardcoding static enums in the page.
- For actual writes, use this page together with [Key Functions](./key.md) and [Switch Related Functions](./axis-calibration.md).