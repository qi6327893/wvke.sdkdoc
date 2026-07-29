# Device Initialization

## Overview

The debug tool shows these setup APIs under `Getting Started / Device Initialization`:

- `keyboard.requestDevices()`
- `keyboard.getDevices()`
- `keyboard.init(id)`

Call `init` successfully before using keyboard APIs that communicate with the device.

## Debug Tool Normal Listener Behavior

Some normal APIs in the debug tool provide `Start Listening` / `Stop Listening`, such as custom keyboard backlight reports, logo lighting reports, ADC realtime sampling, and calibration status preview. A normal listener started from one of these API panels belongs only to the current panel. When you click another API in the sidebar or switch back to `Getting Started`, the debug tool automatically stops the current panel's related listener, polling task, or heartbeat before opening the next API page.

The active global report listener started by `startGlobalReportListener()` is an independent global listener and is not managed as a normal API panel listener. Call `stopGlobalReportListener()` explicitly when global active reports are no longer needed.

## Request Browser Device Authorization

keyboard.requestDevices(configs)

Brief: Open the browser WebHID authorization picker and return the valid keyboard devices authorized by the user. You can pass `configs` as a temporary allowed VID/PID filter array. When omitted, the SDK uses the default `configs` from the `ServiceKeyboard` instance to build WebHID filters.

### Parameters

| Field | Type | Description | Required |
|------|------|-------------|----------|
| configs | Array<{ vendorId: number; productId: number; usagePage?: number; usage?: number }> | Allowed device filter array. The debug SDK shows the built-in valid VID/PID list by default, and you can edit it before passing it to the API. | No |

### Return Value

Overall type: `Promise<{ code: number; devices: Device[] }>`

The returned `devices` structure is the same as `keyboard.getDevices()` and is deduplicated by device identifier.

### Example

```javascript
const { devices } = await keyboard.requestDevices([
	{ vendorId: 0x1CAA, productId: 0x0806, usagePage: 0xFFA0, usage: 0x01 }
]);
console.log('Authorized devices:', devices);
```

## Get Authorized Device List

keyboard.getDevices()

Brief: Get the list of keyboard devices already authorized by the browser. This method does not open the browser authorization picker. Call `keyboard.requestDevices()` first when the user needs to authorize a new device.

### Parameters

This method does not require parameters.

### Return Value

Overall type: `Promise<{ code: number; devices: Device[] }>`

`Device` structure inside `devices`:

| Field | Type | Description | Example |
|------|------|-------------|---------|
| data | object | Device data object | - |
| id | string | Unique device identifier | "7338:2054" |
| usage | number | Device usage value | 1 |
| usagePage | number | Device usage page value | 65440 |
| vendorId | number | Vendor ID | 7338 |
| productId | number | Product ID | 2054 |
| productName | string | Product name | "GT60 PRO" |

### Example

```javascript
import { ServiceKeyboard } from 'wvke-sdk';

const keyboard = new ServiceKeyboard({
	configs: [
		{ vendorId: 0x1caa, productId: 0x0806, usagePage: 0xffa0, usage: 0x01 }
	],
	usage: 0x01,
	usagePage: 0xffa0,
});

const { code, devices } = await keyboard.getDevices();
if (code !== 0 || devices.length === 0) {
	throw new Error('No authorized keyboard devices found. Call requestDevices first.');
}

console.log('Discovered devices:', devices);
```

## Initialize Specified Keyboard Device

keyboard.init(id)

Brief: Initialize the selected keyboard device and open the WebHID connection for subsequent API calls.

### Parameters

| Field | Type | Description | Required |
|------|------|-------------|----------|
| id | string | Unique identifier of the device to initialize, available from the `devices` array returned by `keyboard.requestDevices()` or `keyboard.getDevices()` | Yes |

### Return Value

Overall type: `Promise<{ code: number; info: string }>`

| Field | Type | Description |
|------|------|-------------|
| code | number | `0` means initialization succeeded |
| info | string | Initialization result message |

### Example

```javascript
const { devices } = await keyboard.requestDevices();

if (!devices.length) {
	throw new Error('No device available for initialization');
}

const result = await keyboard.init(devices[0].id);
console.log('Device initialization result:', result);
```

## Debug Tool Location

In the SDK debug page, these APIs are under:

`Getting Started` -> `Device Initialization`

After `requestDevices` / `getDevices` and `init` complete, use the `Linear Keyboard` or `Firmware Upgrade` groups for the remaining APIs.
