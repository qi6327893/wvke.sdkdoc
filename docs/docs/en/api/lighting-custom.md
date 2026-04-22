# Custom Lighting & Streaming

## Overview

This category covers the newer real-time lighting APIs in the SDK. Unlike the base lighting APIs, these methods support both one-time reads and continuous device reports, which makes them suitable for per-key doodle editors, logo previews, and live debug tools.

Current capabilities in this category:

- `ServiceKeyboard.getKeyboardBacklightCustom()`
- `ServiceKeyboard.setKeyboardBacklightCustom(params)`
- `ServiceKeyboard.getLightingLogoCustom()`
- `ServiceKeyboard.setLightingLogoCustom(params)`
- `ServiceKeyboard.on('keyboardBacklightCustomReport', callback)`
- `ServiceKeyboard.on('lightingLogoCustomReport', callback)`
- Matching `off(...)` unsubscribe calls

## Get Custom Keyboard Backlight Matrix

ServiceKeyboard.getKeyboardBacklightCustom()

Brief: Triggers the device to report the current per-key RGB matrix. The device does not return only one packet; it keeps streaming lighting data for a period of time.

### Returns

- Overall type: `Promise<{ code: number, data: Array<[string, string, string, string]>, readKeys: number, packetCount: number }>`

- Field meanings:

- `data`: fixed format `[keyHid, r, g, b]`
- `readKeys`: number of keys successfully read
- `packetCount`: number of HID packets received

### Continuous streaming

Event name: `keyboardBacklightCustomReport`

```javascript
const handleLightingReport = (payload) => {
	console.log('Real-time lighting report:', payload.data);
};

ServiceKeyboard.on('keyboardBacklightCustomReport', handleLightingReport);
await ServiceKeyboard.getKeyboardBacklightCustom();

const heartbeatTimer = setInterval(() => {
	ServiceKeyboard.getKeyboardBacklightCustom().catch(console.error);
}, 5000);

ServiceKeyboard.off('keyboardBacklightCustomReport', handleLightingReport);
clearInterval(heartbeatTimer);
```

## Set Custom Keyboard Backlight Matrix

ServiceKeyboard.setKeyboardBacklightCustom(params)

Brief: Writes the per-key custom backlight matrix. The SDK switches the keyboard into Doodle Mode automatically before writing.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| data | array | Custom lighting data in `[keyHid, r, g, b]` format | Yes |

### Example

```javascript
await ServiceKeyboard.setKeyboardBacklightCustom({
	data: [['04', 'ff', '00', '00'], ['05', '00', 'ff', '00']]
});
```

## Get Custom Logo Lighting

ServiceKeyboard.getLightingLogoCustom()

Brief: Triggers the device to report the current logo RGB value. Devices without logo lighting can ignore this API group.

### Returns

- Overall type: `Promise<{ code: number, data: [string, string, string], packetCount: number }>`

- Field meanings:

- `data`: fixed format `[r, g, b]`
- `packetCount`: number of HID packets received

### Continuous streaming

Event name: `lightingLogoCustomReport`

```javascript
const handleLogoReport = (payload) => {
	console.log('Real-time logo report:', payload.data);
};

ServiceKeyboard.on('lightingLogoCustomReport', handleLogoReport);
await ServiceKeyboard.getLightingLogoCustom();

const heartbeatTimer = setInterval(() => {
	ServiceKeyboard.getLightingLogoCustom().catch(console.error);
}, 5000);

ServiceKeyboard.off('lightingLogoCustomReport', handleLogoReport);
clearInterval(heartbeatTimer);
```

## Set Custom Logo Lighting

ServiceKeyboard.setLightingLogoCustom(params)

Brief: Writes the logo RGB value. The SDK switches the logo lighting into its custom mode automatically before writing.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| data | array | Logo RGB data in `[r, g, b]` format | Yes |

### Example

```javascript
await ServiceKeyboard.setLightingLogoCustom({
	data: ['80', '29', '31']
});
```

## Recommendations

TIP

- For one-time reads, waiting for the Promise result is enough.
- For live UI rendering, always combine the read API with the matching event listener and a `5000ms` heartbeat.
- Always remove listeners with `off(...)` when the page is destroyed.
- Base effect enums, logo effect enums, brightness, and speed ranges remain documented under [Kaleidoscope Lighting](./lighting.md).