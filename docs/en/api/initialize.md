# Device Initialization

## Overview

The debug tool shows these setup APIs under `Getting Started / Device Initialization`:

- `keyboard.getDevices()`
- `keyboard.init(id)`

Call `init` successfully before using keyboard APIs that communicate with the device.

## Get Device List

keyboard.getDevices()

Brief: Get the list of keyboard devices already authorized by the browser or available for selection. The SDK builds WebHID filters from the `ServiceKeyboard` instance `configs`, using `vendorId`, `productId`, `usagePage`, and `usage`.

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
	throw new Error('No keyboard devices found');
}

console.log('Discovered devices:', devices);
```

## Initialize Specified Keyboard Device

keyboard.init(id)

Brief: Initialize the selected keyboard device and open the WebHID connection for subsequent API calls.

### Parameters

| Field | Type | Description | Required |
|------|------|-------------|----------|
| id | string | Unique identifier of the device to initialize, available from the `devices` array returned by `keyboard.getDevices()` | Yes |

### Return Value

Overall type: `Promise<{ code: number; info: string }>`

| Field | Type | Description |
|------|------|-------------|
| code | number | `0` means initialization succeeded |
| info | string | Initialization result message |

### Example

```javascript
const { devices } = await keyboard.getDevices();

if (!devices.length) {
	throw new Error('No device available for initialization');
}

const result = await keyboard.init(devices[0].id);
console.log('Device initialization result:', result);
```

## Debug Tool Location

In the SDK debug page, these APIs are under:

`Getting Started` -> `Device Initialization`

After `getDevices` and `init` complete, use the `Linear Keyboard` or `Firmware Upgrade` groups for the remaining APIs.
