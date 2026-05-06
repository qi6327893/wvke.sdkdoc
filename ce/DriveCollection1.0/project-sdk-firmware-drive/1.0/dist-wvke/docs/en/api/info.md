# Global Features

This page documents keyboard global APIs used after device initialization. Device discovery and initialization have moved to [Device Initialization](./initialize.md).

## Get Device Information

keyboard.getDevicesInfo()

Brief description: Gets information for the currently initialized device.

### Parameters

This method does not require parameters and is usually used after a successful call to `keyboard.init()`.

### Return Value

• Overall type: `Promise<DeviceInfo>`
• Description: Returns a `Promise` that resolves to a `DeviceInfo` object containing device information.
• Resolved object structure (`DeviceInfo`):

| Field | Type | Description | Example |
|------|------|------|------|
| BoardID | number | Board ID | 1174538260 |
| FWsize | number | Firmware size marker | 2 |
| RunMode | number | Running mode | 0 |
| SN | string | Device serial number | "2493C45353E0A336" |
| Version | string | Firmware version | "004" |
| ConnectMode | number | Connection mode | 16 |
| CheckNum | string | Check value | "00000000" |

Example return structure:

```json
{
	"BoardID": 1174538260,
	"FWsize": 2,
	"RunMode": 0,
	"SN": "2493C45353E0A336",
	"Version": "004",
	"ConnectMode": 16,
	"CheckNum": "00000000"
}
```

### Example

```javascript
import { ServiceKeyboard } from 'wvke-sdk';

async function fetchDeviceInfo() {
	const keyboard = new ServiceKeyboard();

	try {
		// Make sure the device is initialized first
		// await keyboard.init(deviceId);
		const deviceInfo = await keyboard.getDevicesInfo();
		# Global Features

		This page contains keyboard global APIs used after device initialization. For discovery and initialization, see [Device Initialization](./initialize.md).

This method does not require any parameters.

### Return Value

• Overall type: `Promise<{ code: number, key: string }>`
• Description: Returns a `Promise` that resolves to the current polling rate information.
• Resolved object structure:

| Field | Type | Description | Example |
|------|------|------|------|
| code | number | API status code. `0` means success. | 0 |
| key | string | Current polling rate display value. | "1000Hz" |

Protocol value mapping:

| Protocol Value | Polling Rate |
|------|------|
| 1 | 125Hz |
| 2 | 250Hz |
| 3 | 500Hz |
| 4 | 1000Hz |
| 5 | 2000Hz |
| 6 | 4000Hz |
| 7 | 8000Hz |

### Example

```javascript
async function fetchRateOfReturn() {
	try {
		const rateInfo = await ServiceKeyboard.getRateOfReturn();
		console.log('Current polling rate:', rateInfo);
		// Example output: { code: 0, key: '1000Hz' }
	} catch (error) {
		console.error('Failed to get current polling rate:', error);
	}
}

// fetchRateOfReturn();
```

## Set Polling Rate

ServiceKeyboard.setRateOfReturn()

Brief description: Sets the current keyboard polling rate.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| value | string \| number | Polling rate value. Supports protocol values `1` to `7`, display values such as `125Hz`, `250Hz`, `500Hz`, `1000Hz`, `2000Hz`, `4000Hz`, `8000Hz`, and KHz-style inputs such as `1KHz`, `2KHz`, `4KHz`, `8KHz`. | Yes |

### Return Value

• Overall type: `Promise<{ code: number, key: string, value: number }>`
• Description: Returns a `Promise` that resolves to the applied polling-rate result, where `key` is the display value and `value` is the protocol value.

### Example

```javascript
async function updateRateOfReturn(value) {
	try {
		const result = await ServiceKeyboard.setRateOfReturn(value);
		console.log('Set polling rate result:', result);
		// Example output: { code: 0, key: '1000Hz', value: 4 }
	} catch (error) {
		console.error('Failed to set polling rate:', error);
	}
}

// Using protocol value
// updateRateOfReturn(4);

// Using display value
// updateRateOfReturn('1000Hz');

// Using KHz style
// updateRateOfReturn('8KHz');
```

### Notes

TIP

• In the keyboard firmware protocol, polling rates are represented by values `1` through `7`.
• The SDK automatically converts common string inputs to the corresponding protocol value.
• After setting the polling rate, it is recommended to call `ServiceKeyboard.getRateOfReturn()` again to confirm the result.

## Start Global Active Report Listener

ServiceKeyboard.startGlobalReportListener(options)

Brief description: Listens for keyboard active reports for profile switching, keyboard backlight base changes, and logo lighting base changes.

The return value of `startGlobalReportListener()` only indicates whether the listener is enabled. Real-time report data is delivered through `on('keyboardGlobalReport', callback)` or the specific event callbacks below.

### Parameters

| Parameter | Type | Description | Required |
|------|------|------|------|
| options | object | Optional settings. Set `includeCommandResponses` to `true` to also emit responses produced by SDK commands. Defaults to `false`. | No |

### Events

| Event | Description |
|------|------|
| keyboardGlobalReport | All recognized global active reports. |
| keyboardConfigChange | Profile slot change report, including `configIndex`, `configName`, and `value`. |
| keyboardLightingChange | Keyboard backlight base report, including `mode`, `childMode`, `luminance`, `speed`, and `sleep`. |
| keyboardLogoLightingChange | Logo lighting base report, including `mode`, `luminance`, `speed`, and `sleep`. |

### Example

```javascript
const handleGlobalReport = (report) => {
	console.log('Keyboard global active report:', report);
};

ServiceKeyboard.on('keyboardGlobalReport', handleGlobalReport);

ServiceKeyboard.on('keyboardConfigChange', (report) => {
	console.log('Current profile:', report.configIndex);
});

ServiceKeyboard.on('keyboardLightingChange', (report) => {
	console.log('Lighting mode/brightness/speed:', report.mode, report.luminance, report.speed);
});

ServiceKeyboard.startGlobalReportListener();

// Stop listening and remove the business callback when real-time data is no longer needed.
ServiceKeyboard.stopGlobalReportListener();
ServiceKeyboard.off('keyboardGlobalReport', handleGlobalReport);
```

### Report Examples

Profile change report:

```json
{
	"code": 0,
	"type": "config",
	"configIndex": 2,
	"configName": "Config2",
	"value": 2
}
```

Keyboard backlight base report:

```json
{
	"code": 0,
	"type": "lighting",
	"mode": 1,
	"modeLabel": "Static Mode",
	"childMode": 0,
	"luminance": 3,
	"speed": 4,
	"sleep": 15
}
```

## Stop Global Active Report Listener

ServiceKeyboard.stopGlobalReportListener()

Brief description: Stops the keyboard active report listener started by `startGlobalReportListener()`.

### Example

```javascript
ServiceKeyboard.stopGlobalReportListener();
```

## Get Sleep Timeout

ServiceKeyboard.getLightingSleepTime()

Brief description: Gets the current keyboard sleep timeout setting.

### Parameters

This method does not require any parameters.

### Return Value

• Overall type: `Promise<{ code: number, key: string, value: number }>`
• Description: Returns a `Promise` that resolves to the current sleep-time result, where `value` is the protocol minute value and `key` is the human-readable label.
• Resolved object structure:

| Field | Type | Description | Example |
|------|------|------|------|
| code | number | API status code. `0` means success. | 0 |
| key | string | Human-readable sleep-time label. | "15 minutes" |
| value | number | Protocol sleep-time value. `0` means never sleep. | 15 |

Common supported values:

| Protocol Value | Meaning |
|------|------|
| 0 | Never sleep |
| 1 | 1 minute |
| 2 | 2 minutes |
| 3 | 3 minutes |
| 5 | 5 minutes |
| 10 | 10 minutes |
| 15 | 15 minutes |
| 20 | 20 minutes |
| 30 | 30 minutes |
| 45 | 45 minutes |
| 60 | 60 minutes |

### Example

```javascript
async function fetchLightingSleepTime() {
	try {
		const result = await ServiceKeyboard.getLightingSleepTime();
		console.log('Current sleep timeout:', result);
		// Example output: { code: 0, key: '15 minutes', value: 15 }
	} catch (error) {
		console.error('Failed to get sleep timeout:', error);
	}
}

// fetchLightingSleepTime();
```

## Set Sleep Timeout

ServiceKeyboard.setLightingSleepTime(time)

Brief description: Sets the current keyboard sleep timeout. `0` means never sleep.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| time | string \| number | Sleep timeout value. Supports `0`, `1`, `2`, `3`, `5`, `10`, `15`, `20`, `30`, `45`, `60`, and string forms such as `15 minutes`. | Yes |

### Return Value

• Overall type: `Promise<{ code: number, key: string, value: number }>`
• Description: Returns a `Promise` that resolves to the applied sleep-time result.

### Example

```javascript
async function updateLightingSleepTime(time) {
	try {
		const result = await ServiceKeyboard.setLightingSleepTime(time);
		console.log('Set sleep timeout result:', result);
		// Example output: { code: 0, key: 'Never sleep', value: 0 }
	} catch (error) {
		console.error('Failed to set sleep timeout:', error);
	}
}

// Set to never sleep
// updateLightingSleepTime(0);

// Set to 15 minutes
// updateLightingSleepTime(15);
```

### Notes

TIP

• `time` only supports fixed enumerated values: `0`, `1`, `2`, `3`, `5`, `10`, `15`, `20`, `30`, `45`, `60`
• `0` means never sleep
• The `key` field in the return value can be used directly in UI display

## Restore Factory Settings

ServiceKeyboard.GFSRestore()

Brief description: Restores the keyboard to factory default settings.

### Parameters

This method does not require any parameters.

### Return Value

• Overall type: `Promise<{ code: number, success: boolean, message: string }>`
• Description: Returns a `Promise` that resolves to the factory-reset result object when the operation succeeds.

| Field | Type | Description | Example |
|------|------|------|------|
| code | number | API status code. `0` means success. | 0 |
| success | boolean | Whether the reset succeeded. | true |
| message | string | Result message. | "Factory settings restored successfully" |

### Example

```javascript
async function restoreFactorySettings() {
	try {
		const result = await ServiceKeyboard.GFSRestore();
		console.log('Factory reset result:', result);
		// Example output: { code: 0, success: true, message: 'Factory settings restored successfully' }
	} catch (error) {
		console.error('Failed to restore factory settings:', error);
	}
}

// restoreFactorySettings();
```

### Notes

TIP

• After a factory reset, config slots, lighting parameters, system type, sleep timeout, and other global settings may return to their defaults
• After the reset, it is recommended to call `getConfig()`, `getSystemType()`, `getRateOfReturn()`, and `getLightingSleepTime()` again to refresh the current state
• If your app caches key layouts, DPI, or other device-side data, refresh those as well

## Get System Type

ServiceKeyboard.getSystemType()

Brief description: Gets the current system type configured on the keyboard, either Windows or Mac.

### Parameters

This method does not require any parameters.

### Return Value

• Overall type: `Promise<{ key: string, value: number }>`
• Description: Returns a `Promise` that resolves to an object containing system type information.
• Resolved object structure:

| Field | Type | Description | Example |
|------|------|------|------|
| key | string | System type name, possible values are "WIN" or "MAC" | "MAC" |
| value | number | Numeric value of the system type, 0 means Windows and 1 means Mac | 1 |

### Example

```javascript
async function fetchSystemType() {
	try {
		const systemType = await ServiceKeyboard.getSystemType();
		console.log('Current system type:', systemType);
		// Example output: { "key": "MAC", "value": 1 }
	} catch (error) {
		console.error('Failed to get system type:', error);
	}
}

// fetchSystemType();
```

## Set System Type

ServiceKeyboard.setSystemType()

Brief description: Sets the keyboard system type, either Windows or Mac.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| systemType | string | System type to set, only "WIN" or "MAC" is allowed | Yes |

### Return Value

• Overall type: `Promise<{ code: number, key: string, value: number }>`
• Description: Returns a `Promise` that resolves to the applied system-type result, where `key` is the system-type name and `value` is the protocol value.

### Example

```javascript
async function setSystemType(systemType) {
	try {
		// systemType can only be "WIN" or "MAC"
		const result = await ServiceKeyboard.setSystemType(systemType);
		console.log('Set system type result:', result);
		// Example output: { code: 0, key: 'WIN', value: 0 }
	} catch (error) {
		console.error('Failed to set system type:', error);
	}
}

// Set to Windows
// setSystemType('WIN');

// Set to Mac
// setSystemType('MAC');
```

### Notes

TIP

• The `systemType` parameter can only be "WIN" or "MAC"
• After switching the system type, some keyboard behaviors may change, such as key mappings and modifier layout
• It is recommended to verify keyboard functionality after updating the system type

## Other APIs

For more API documentation, please refer to the other pages.