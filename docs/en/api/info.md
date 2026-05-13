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
| ConnectMode | number | Connection mode: `0` wired, `1`/`2`/`3` Bluetooth channels 1/2/3, `4` 2.4G | 4 |
| RFVer | number | RF chip version, using only the low byte | 2 |
| CheckNum | string | Check value | "00000000" |

Example return structure:

```json
{
	"BoardID": 1174538260,
	"FWsize": 2,
	"RunMode": 0,
	"SN": "2493C45353E0A336",
	"Version": "004",
	"ConnectMode": 4,
	"RFVer": 2,
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

In the debug tool, the polling-rate enum is displayed in descending order as `0=8000Hz`, `1=4000Hz`, `2=2000Hz`, `3=1000Hz`, `4=500Hz`, `5=250Hz`, `6=125Hz`.

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

Debug enum mapping:

| Debug Enum Value | Polling Rate |
|------|------|
| 0 | 8000Hz |
| 1 | 4000Hz |
| 2 | 2000Hz |
| 3 | 1000Hz |
| 4 | 500Hz |
| 5 | 250Hz |
| 6 | 125Hz |

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
• In the debug tool, the displayed enum uses `0` through `6` in reverse order for the same polling-rate levels.
• The SDK automatically converts common string inputs to the corresponding protocol value.
• After setting the polling rate, it is recommended to call `ServiceKeyboard.getRateOfReturn()` again to confirm the result.

## Start Global Active Report Listener

ServiceKeyboard.startGlobalReportListener(options)

Brief description: Listens for keyboard active reports for profile switching, keyboard backlight base changes, and logo lighting base changes.

`startGlobalReportListener()` sends firmware protocol `0xBD` to enable active reporting and keeps the session alive with a 60-second heartbeat. Real-time report data is delivered through `on('keyboardGlobalReport', callback)` or the specific event callbacks below.

### Parameters

| Parameter | Type | Description | Required |
|------|------|------|------|
| options | object | Optional settings. Set `includeCommandResponses` to `true` to also emit responses produced by SDK commands. Defaults to `false`. | No |

### Return Value

• Overall type: `Promise<{ code: number, enabled: boolean, events: string[], includeCommandResponses: boolean, heartbeatIntervalMs: number }>`
• Description: Returns a `Promise` that resolves to the current active-listener state. `heartbeatIntervalMs` is fixed at `60000`.

### Events

| Event | Description |
|------|------|
| keyboardGlobalReport | All recognized global active reports. |
| keyboardConfigChange | Profile slot change report, including `configIndex`, `configName`, and `value`. |
| keyboardLightingChange | Keyboard backlight base report, including `mode`, `childMode`, `luminance`, `speed`, and `sleep`. |
| keyboardLogoLightingChange | Logo lighting base report, including `mode`, `luminance`, `speed`, and `sleep`. |
| keyboardBatteryChange | Battery level and charging-state report, including `battery` and `isCharging`. |

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

ServiceKeyboard.on('keyboardBatteryChange', (report) => {
	console.log('Battery and charging state:', report.battery, report.isCharging);
});

await ServiceKeyboard.startGlobalReportListener();

// Stop listening and remove the business callback when real-time data is no longer needed.
await ServiceKeyboard.stopGlobalReportListener();
ServiceKeyboard.off('keyboardGlobalReport', handleGlobalReport);
```

### Protocol Notes

TIP

• The firmware function code is `0xBD`.
• Starting the listener sends `0xBD = 1`.
• After startup, the SDK resends `0xBD = 1` every 60 seconds as a heartbeat so the firmware active-report session does not expire.
• Stopping the listener sends `0xBD = 0` and clears the local heartbeat timer.

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

Battery status report:

```json
{
	"code": 0,
	"type": "battery",
	"battery": 87,
	"isCharging": 1
}
```

## Stop Global Active Report Listener

ServiceKeyboard.stopGlobalReportListener()

Brief description: Stops the keyboard active report listener started by `startGlobalReportListener()`.

### Return Value

• Overall type: `Promise<{ code: number, enabled: boolean }>`
• Description: Returns a `Promise` that resolves to the stopped listener state.

### Example

```javascript
await ServiceKeyboard.stopGlobalReportListener();
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

## Get Gamepad Mode

ServiceKeyboard.getGamepadMode()

Brief description: Gets the current gamepad mode.

### Parameters

This method does not require any parameters.

### Return Value

• Overall type: `Promise<{ code: number, key: string, value: number }>`
• Description: Returns a `Promise` that resolves to the current gamepad-mode state.

| Field | Type | Description | Example |
|------|------|------|------|
| code | number | API status code. `0` means success. | 0 |
| key | string | Current gamepad-mode label. | "Xbox gamepad mode" |
| value | number | Current protocol value. `0` = keyboard-only mode, `1` = Xbox gamepad mode, `2` = classic gamepad mode. | 1 |

### Example

```javascript
async function fetchGamepadMode() {
	try {
		const result = await ServiceKeyboard.getGamepadMode();
		console.log('Current gamepad mode:', result);
	} catch (error) {
		console.error('Failed to get gamepad mode:', error);
	}
}
```

## Set Gamepad Mode

ServiceKeyboard.setGamepadMode(value)

Brief description: Sets the current gamepad mode.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| value | string \| number | Gamepad mode value. Supports `0/1/2`, and labels such as `keyboard-only mode`, `Xbox gamepad mode`, and `classic gamepad mode`. | Yes |

### Return Value

• Overall type: `Promise<{ code: number, key: string, value: number }>`
• Description: Returns a `Promise` that resolves to the applied gamepad-mode state.

### Mode Enum

| Protocol Value | Meaning |
|------|------|
| 0 | Keyboard-only mode |
| 1 | Xbox gamepad mode |
| 2 | Classic gamepad mode |

### Example

```javascript
async function updateGamepadMode(value) {
	try {
		const result = await ServiceKeyboard.setGamepadMode(value);
		console.log('Set gamepad mode result:', result);
	} catch (error) {
		console.error('Failed to set gamepad mode:', error);
	}
}

// updateGamepadMode(0);
// updateGamepadMode(1);
// updateGamepadMode(2);
```

## Get Gamepad Mapping Settings

ServiceKeyboard.getGamepadMappingSettings()

Brief description: Gets the current gamepad mapping settings.

### Parameters

This method does not require any parameters.

### Return Value

• Overall type: `Promise<{ code: number, enableMappedKeyboardKeys: number, disableMappedKeyInput: number }>`
• Description: Returns a `Promise` that resolves to the current gamepad mapping settings.

| Field | Type | Description | Example |
|------|------|------|------|
| code | number | API status code. `0` means success. | 0 |
| enableMappedKeyboardKeys | number | Whether mapped keyboard keys are enabled. `0` means disabled and `1` means enabled. | 1 |
| disableMappedKeyInput | number | Whether original keyboard input is disabled for mapped keys. `0` means allowed and `1` means blocked. | 0 |

### Example

```javascript
async function fetchGamepadMappingSettings() {
	try {
		const result = await ServiceKeyboard.getGamepadMappingSettings();
		console.log('Current gamepad mapping settings:', result);
	} catch (error) {
		console.error('Failed to get gamepad mapping settings:', error);
	}
}
```

## Set Gamepad Mapping Settings

ServiceKeyboard.setGamepadMappingSettings(settings)

Brief description: Sets the current gamepad mapping settings.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| settings.enableMappedKeyboardKeys | string \| number \| boolean | Whether mapped keyboard keys are enabled. | Yes |
| settings.disableMappedKeyInput | string \| number \| boolean | Whether original keyboard input is disabled for mapped keys. | Yes |

### Return Value

• Overall type: `Promise<{ code: number, enableMappedKeyboardKeys: number, disableMappedKeyInput: number }>`

### Example

```javascript
async function updateGamepadMappingSettings() {
	try {
		const result = await ServiceKeyboard.setGamepadMappingSettings({
			enableMappedKeyboardKeys: 1,
			disableMappedKeyInput: 0,
		});
		console.log('Set gamepad mapping result:', result);
	} catch (error) {
		console.error('Failed to set gamepad mapping settings:', error);
	}
}
```

## Get Gamepad Curve Options

ServiceKeyboard.getGamepadCurveOptions()

Brief description: Gets the current gamepad curve options.

### Parameters

This method does not require any parameters.

### Return Value

• Overall type: `Promise<{ code: number, enableAngleAdjustment: number, enableSquareStickOutput: number, enableExtremePriority: number }>`
• Description: Returns a `Promise` that resolves to the current gamepad curve options.

| Field | Type | Description | Example |
|------|------|------|------|
| code | number | API status code. `0` means success. | 0 |
| enableAngleAdjustment | number | Whether angle adjustment is enabled. `0` means disabled and `1` means enabled. | 0 |
| enableSquareStickOutput | number | Whether square-stick output is enabled. `0` means disabled and `1` means enabled. | 1 |
| enableExtremePriority | number | Whether extreme-priority mode is enabled. `0` means disabled and `1` means enabled. | 0 |

### Example

```javascript
async function fetchGamepadCurveOptions() {
	try {
		const result = await ServiceKeyboard.getGamepadCurveOptions();
		console.log('Current gamepad curve options:', result);
	} catch (error) {
		console.error('Failed to get gamepad curve options:', error);
	}
}
```

## Set Gamepad Curve Options

ServiceKeyboard.setGamepadCurveOptions(settings)

Brief description: Sets the current gamepad curve options.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| settings.enableAngleAdjustment | string \| number \| boolean | Whether angle adjustment is enabled. | Yes |
| settings.enableSquareStickOutput | string \| number \| boolean | Whether square-stick output is enabled. | Yes |
| settings.enableExtremePriority | string \| number \| boolean | Whether extreme-priority mode is enabled. | Yes |

### Return Value

• Overall type: `Promise<{ code: number, enableAngleAdjustment: number, enableSquareStickOutput: number, enableExtremePriority: number }>`

### Example

```javascript
async function updateGamepadCurveOptions() {
	try {
		const result = await ServiceKeyboard.setGamepadCurveOptions({
			enableAngleAdjustment: 0,
			enableSquareStickOutput: 1,
			enableExtremePriority: 0,
		});
		console.log('Set gamepad curve options result:', result);
	} catch (error) {
		console.error('Failed to set gamepad curve options:', error);
	}
}
```

## Get Gamepad Linear Curve

ServiceKeyboard.getGamepadLinearCurve()

Brief description: Gets the current gamepad linear curve and returns A, B, C, and D travel values plus their response precision values.

### Parameters

This method does not require any parameters.

### Return Value

• Overall type: `Promise<{ code: number, pointA: number, pointAValue: number, pointB: number, pointBValue: number, pointC: number, pointCValue: number, pointD: number, pointDValue: number }>`
• Description: Returns a `Promise` that resolves to the current gamepad linear-curve data.

| Field | Type | Description | Example |
|------|------|------|------|
| code | number | API status code. `0` means success. | 0 |
| pointA | number | Point A in mm. | 0.1 |
| pointAValue | number | Point A response precision value, from 0 to 255. | 20 |
| pointB | number | Point B in mm. | 0.2 |
| pointBValue | number | Point B response precision value, from 0 to 255. | 60 |
| pointC | number | Point C in mm. | 3.0 |
| pointCValue | number | Point C response precision value, from 0 to 255. | 200 |
| pointD | number | Point D in mm. | 3.3 |
| pointDValue | number | Point D response precision value, from 0 to 255. | 255 |

### Example

```javascript
async function fetchGamepadLinearCurve() {
	try {
		const result = await ServiceKeyboard.getGamepadLinearCurve();
		console.log('Current gamepad linear curve:', result);
	} catch (error) {
		console.error('Failed to get gamepad linear curve:', error);
	}
}
```

## Set Gamepad Linear Curve

ServiceKeyboard.setGamepadLinearCurve(curve)

Brief description: Sets the current gamepad linear curve using A, B, C, and D travel values plus their response precision values.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| curve.pointA | string \| number | Point A in mm. | Yes |
| curve.pointAValue | string \| number | Point A response precision value, from 0 to 255. Defaults to 20. | No |
| curve.pointB | string \| number | Point B in mm. | Yes |
| curve.pointBValue | string \| number | Point B response precision value, from 0 to 255. Defaults to 60. | No |
| curve.pointC | string \| number | Point C in mm. | Yes |
| curve.pointCValue | string \| number | Point C response precision value, from 0 to 255. Defaults to 200. | No |
| curve.pointD | string \| number | Point D in mm. | Yes |
| curve.pointDValue | string \| number | Point D response precision value, from 0 to 255. Defaults to 255. | No |

### Return Value

• Overall type: `Promise<{ code: number, pointA: number, pointAValue: number, pointB: number, pointBValue: number, pointC: number, pointCValue: number, pointD: number, pointDValue: number }>`

### Example

```javascript
async function updateGamepadLinearCurve() {
	try {
		const result = await ServiceKeyboard.setGamepadLinearCurve({
			pointA: 0.1,
			pointAValue: 20,
			pointB: 0.2,
			pointBValue: 60,
			pointC: 3.0,
			pointCValue: 200,
			pointD: 3.3,
			pointDValue: 255,
		});
		console.log('Set gamepad linear curve result:', result);
	} catch (error) {
		console.error('Failed to set gamepad linear curve:', error);
	}
}
```

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