# Performance

## Overview

This page strictly matches the Switch Travel Functions category from the SDK debug page. It covers keyboard travel configuration, real-time ADC sampling, and calibration-related APIs.

## Start Calibration

ServiceKeyboard.calibrationStart()

Brief: Starts the device calibration flow. The SDK only sends the start-calibration command and no longer automatically starts the legacy multi-layer realtime listener. To show live calibration progress, poll `getCalibrationStatus()`.

### Parameters

This method does not require parameters.

### Returns

- Overall type: `Promise<{ status: number, message: string }>`
- Description: Returns the calibration start status.

### Debug Tool Behavior

- In the debug tool, selecting `calibrationStart` and clicking `Invoke` starts calibration first, then automatically starts live calibration-status polling.
- Live status is collected by polling `getCalibrationStatus()` periodically. It reads only the key-status layer, without ADC or travel layers.
- Clicking `Stop Listening` or leaving this API stops polling and cancels pending realtime matrix requests.

### Example

```javascript
const calibration = await ServiceKeyboard.calibrationStart();
console.log('Calibration started:', calibration);
```

## End Calibration

ServiceKeyboard.calibrationStop()

Brief: Ends the device calibration flow.

### Parameters

This method does not require parameters.

### Returns

- Overall type: `Promise<{ status: number, message: string }>`
- Description: Returns the calibration end status.

### Example

```javascript
const calibration = await ServiceKeyboard.calibrationStop();
console.log('Calibration finished:', calibration);
```

## Get Calibration Status

ServiceKeyboard.getCalibrationStatus()

Brief: Gets the real-time calibration status of the entire keyboard. This API reads only the key-status layer, without ADC raw values or live travel data. The returned keyboard-map layout matches `getADCSample()`, so it can be used directly for a calibration-status keyboard view.

### Parameters

None.

### Returns

Returned object fields:

- `calibrate`: calibration-flow state tracked by the SDK. `1` means calibration has been started, and `0` means calibration is not currently started or has been stopped.
- `data`: effective calibration-status array for the full keyboard. Same as `calibrationStatusData`.
- `keyStatusData`: full-keyboard key-status array, normalized to the low 4 bits only. Usually `0` means released and non-zero means pressed.
- `calibrationStatusData`: full-keyboard effective calibration-status array from the high 4 bits, with legacy firmware compatibility applied.
- `maxKeyStatus`: current maximum key-status value.
- `maxCalibrationStatus`: current maximum calibration-status value.
- `pressedCount`: number of keys currently treated as pressed.
- `calibratedCount`: number of keys currently treated as calibrated.
- `currentRoundCalibratedCount`: number of keys calibrated in the current round.
- `total`: number of valid keys in the current layout.
- `details`: flattened key list for tables or lists.
- `layout`: two-dimensional keyboard-row layout for direct keyboard rendering.

### details / layout Item Fields

Each valid key object in `details` and `layout` contains:

- `row`: zero-based row index.
- `col`: zero-based column index.
- `keyName`: display name of the key.
- `name`: key name, usually the same as `keyName`.
- `hid`: key HID, for example `0x04`.
- `keyStatus`: current key-status value, normalized to the low 4 bits.
- `calibrationStatus`: current effective calibration-status value.
- `calibrated`: `1` means calibrated, `0` means uncalibrated.
- `currentRoundCalibrated`: `1` means calibrated in the current round, `0` means not calibrated in the current round.
- `pressed`: `1` means pressed, `0` means released.

### Status Value Notes

- Key state is read from the low 4 bits of the raw status value. Calibration state is read from the high 4 bits.
- On newer firmware, calibration-status flag `0x08` means calibrated and `0x04` means calibrated in the current round. When both flags are set, the effective status is usually `0x0c`.
- For legacy firmware compatibility, if the high 4 bits stay `0` while the low 4 bits return `2`, the SDK treats the key as calibration-success and returns `calibrationStatus = 0x0c`, `calibrated = 1`, and `currentRoundCalibrated = 1`.
- `layout` is generated from the SDK-mapped keyboard matrix and matches the keyboard placement used by `getADCSample()`.

### Example

```javascript
const status = await ServiceKeyboard.getCalibrationStatus();
console.log('Calibration status:', status);
console.log('Calibrated key count:', status.calibratedCount);
console.log('First key calibration status:', status.details[0]);
```

Example output shape:

```javascript
{
	code: 0,
	calibrate: 1,
	data: [0, 12, 12],
	keyStatusData: [0, 2, 0],
	calibrationStatusData: [0, 12, 12],
	maxKeyStatus: 2,
	maxCalibrationStatus: 12,
	pressedCount: 1,
	calibratedCount: 2,
	currentRoundCalibratedCount: 2,
	total: 61,
	details: [
		{
			row: 0,
			col: 0,
			keyName: 'Esc',
			name: 'Esc',
			hid: '0x29',
			keyStatus: 0,
			calibrationStatus: 0,
			calibrated: 0,
			currentRoundCalibrated: 0,
			pressed: 0
		}
	],
	layout: [[/* two-dimensional keyboard layout */]]
}
```

### Polling Example

```javascript
await ServiceKeyboard.calibrationStart();

let timer = null;

const pollCalibrationStatus = async () => {
	try {
		const snapshot = await ServiceKeyboard.getCalibrationStatus();
		console.log('Calibrated key count:', snapshot.calibratedCount);
		console.log('Current-round calibrated key count:', snapshot.currentRoundCalibratedCount);
		// Refresh the calibration-status keyboard view here.
	} catch (error) {
		console.error('Failed to read calibration status:', error);
	}
};

await pollCalibrationStatus();
timer = setInterval(pollCalibrationStatus, 150);

// Clear when the page is destroyed or calibration ends.
clearInterval(timer);
await ServiceKeyboard.calibrationStop();
```

### Debug Tool Usage

- Call `init` in the debug tool first to initialize the device.
- Select `calibrationStart` in the left panel.
- Click `Invoke`; the debug tool starts calibration and automatically polls `getCalibrationStatus` periodically.
- The Calibration Status Map displays each key's pressed state, calibrated state, and current-round calibration state.
- Live calibration status reads only the key-status layer, without ADC or travel layers. Its live-preview cadence is close to `getADCSample`.
- Click `Stop Listening` when realtime preview is no longer needed.

## Get Keyboard Travel Configuration

ServiceKeyboard.getPerformance()

Brief: Gets the keyboard travel configuration, including trigger position, RT switch status, RT default values, safe-zone values, and key operating mode.

### Parameters

None.

### Returns

- Overall type: `Promise<{ code: number, mode: number, normalPress: number, normalRelease: number, rtFirstTouch: number, rtPress: number, rtRelease: number, pressDeadStroke: number, releaseDeadStroke: number, axis?: number, axisRangeMax?: number, axisCoefficient?: number, calibrate: number, total: number, details: Array<...>, layout: Array<Array<...>> }>`
- Description: Returns the current full-keyboard travel configuration. Top-level fields provide the keyboard-wide shared values, while `details` contains the flattened key list and `layout` returns the keyboard MAP. Each key item includes `triggerPosition`, `rtDown`, `rtUp`, `zoneSafeTop`, `zoneSafeBottom`, `workingMode`, and resolved switch metadata when available.

### Example

```javascript
const performance = await ServiceKeyboard.getPerformance();
console.log(performance.mode);
console.log(performance.normalPress);
console.log(performance.rtPress, performance.rtRelease);
console.log(performance.layout[0][0]);
console.log('Keyboard travel configuration:', performance);
```

## Set Key Performance

ServiceKeyboard.setPerformance(params)

Brief: Sets the travel configuration for a single key, including working mode, trigger position, RT switch, RT trigger position, RT down, RT up, top safe zone, and bottom safe zone. Any omitted fields are automatically filled from the key's current defaults and sent together in the same call.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| keyName | string | Key name, for example `A` | Yes |
| workingMode | number | Key working mode | No |
| triggerPosition | number | Trigger position | No |
| rtSwitch | number | Per-key RT switch, 0 off and 1 on | No |
| triggerRtPosition | number | RT trigger position | No |
| rtDown | number | RT down | No |
| rtUp | number | RT up | No |
| zoneSafeTop | number | Top safe zone | No |
| zoneSafeBottom | number | Bottom safe zone | No |

### Returns

- Overall type: `Promise<{ code: number, keyName: string, hid: string, workingMode: number, journeyMode: number, journeyModeName: string, advancedMode: number, advancedModeName: string, rtSwitch: number, triggerPosition: number, triggerRtPosition: number, appliedTriggerPosition: number, rtDown: number, rtUp: number, zoneSafeTop: number, zoneSafeBottom: number }>`
- Description: Returns the applied travel configuration for the specified key. In addition to `workingMode`, the response also exposes `journeyMode / advancedMode` and their labels so the final device-side mode combination is explicit; `appliedTriggerPosition` is the trigger position actually sent to the device.

### Example

```javascript
const result = await ServiceKeyboard.setPerformance({
	keyName: 'A',
	workingMode: 0,
	triggerPosition: 0.5,
	rtSwitch: 1,
	triggerRtPosition: 0.3,
	rtDown: 0.3,
	rtUp: 0.3,
	zoneSafeTop: 0.2,
	zoneSafeBottom: 0.2
});
console.log('Set performance result:', result);
```

## Get ADC Sample

ServiceKeyboard.getADCSample()

Brief: Gets a real-time snapshot of ADC sample data, live travel data, and key-press status for the entire keyboard. The API reads travel data, ADC raw values, and key status in order, then organizes the result into structures that can be rendered directly as a keyboard UI. It is suitable for frontend polling to render live key-state changes.

### Parameters

None.

### Returns

Returned object fields:

- `adc`: maximum ADC raw value currently sampled across the keyboard.
- `data`: full-keyboard ADC raw-value array in firmware matrix order.
- `journeyData`: full-keyboard live travel array in `mm`.
- `keyStatusData`: full-keyboard key-status array, normalized to the low 4 bits only. Usually `0` means released and non-zero means pressed.
- `maxJourney`: maximum live travel value currently sampled across the keyboard, in `mm`.
- `maxKeyStatus`: key-status value for the current maximum travel entry.
- `pressedCount`: number of keys currently treated as pressed.
- `total`: number of valid keys in the current layout.
- `details`: flattened key list for tables or lists.
- `layout`: two-dimensional keyboard-row layout for direct keyboard rendering.

### details / layout Item Fields

Each valid key object in `details` and `layout` contains:

- `row`: zero-based row index.
- `col`: zero-based column index.
- `keyName`: display name of the key.
- `name`: key name, usually the same as `keyName`.
- `hid`: key HID, for example `0x04`.
- `journey`: current live travel, in `mm`.
- `adc`: current ADC raw value.
- `keyStatus`: current key-status value, normalized to the low 4 bits.
- `pressed`: whether the key is treated as pressed, equivalent to low-4-bit `keyStatus > 0`.

### Notes

- This is a snapshot API. Each call returns the full-keyboard state at that moment and does not continuously report by itself.
- For live visualization, poll it from the frontend, for example every `100ms` to `300ms`, depending on page performance and device load.
- Pressed/released state is determined only from the low 4 bits of the status value. High bits do not affect `pressed`.
- For UI rendering, prefer `details` or `layout`; the raw arrays are more useful for low-level debugging.
- Stop the timer when leaving the page to avoid continuing to occupy the HID channel.

### Example

```javascript
const adc = await ServiceKeyboard.getADCSample();
console.log('ADC sample:', adc);
console.log('Pressed key count:', adc.pressedCount);
console.log('Max travel:', adc.maxJourney);
console.log('First key detail:', adc.details[0]);
```

### Polling Example

```javascript
let timer = null;

const pollAdcSample = async () => {
	try {
		const snapshot = await ServiceKeyboard.getADCSample();
		console.log('Pressed key count:', snapshot.pressedCount);
		console.log('Current max ADC:', snapshot.adc);
		// Refresh the keyboard UI here.
	} catch (error) {
		console.error('Failed to read ADC sample:', error);
	}
};

await pollAdcSample();
timer = setInterval(pollAdcSample, 200);

// Clear when the page is destroyed.
clearInterval(timer);
```

### Debug Tool Usage

- Call `init` in the debug tool first to initialize the device.
- Select `getADCSample` in the left panel.
- Click `Invoke` once to read the current ADC, live travel, and pressed-state snapshot.
- Click `Start Listening`; the debug tool polls `getADCSample` periodically and syncs the live pressed state to the keyboard map.
- The Realtime Sample Map highlights currently pressed keys and displays each key's live travel, ADC value, and status.
- Click `Stop Listening` when realtime preview is no longer needed.

## Related Category

TIP

- If you need keyboard-wide switch details, the fine-tunable switch list, or per-key custom switch assignment, see [Switch Related Functions](./axis-calibration.md).