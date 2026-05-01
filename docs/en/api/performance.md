# Performance

## Overview

This page strictly matches the Switch Travel Functions category from the SDK debug page. It covers keyboard travel configuration, real-time ADC sampling, and calibration-related APIs.

## Start Calibration

ServiceKeyboard.calibrationStart()

Brief: Starts the device calibration flow.

### Parameters

This method does not require parameters.

### Returns

- Overall type: `Promise<{ status: number, message: string }>`
- Description: Returns the calibration start status.

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

Brief: Gets the real-time calibration status of the entire keyboard. This API is not yet available.

### Parameters

None.

### Returns

- Overall type: `Promise<{ code: number, calibrate: number, data: number[] }>`
- Description: The return shape is still kept in the type definition, but this API is not yet available and is currently shown in the debug page as a placeholder only.

### Example

```javascript
const status = await ServiceKeyboard.getCalibrationStatus();
console.log('Calibration status:', status);
```

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

Brief: Gets a real-time snapshot of ADC sample data, travel data, and key-press status for the entire keyboard. This API is suitable for periodic polling on the frontend to render live key-state changes.

### Parameters

None.

### Returns

- Overall type: `Promise<{ adc: number, data: number[], journeyData: number[], keyStatusData: number[], maxJourney: number, maxKeyStatus: number, pressedCount: number, total: number, details: Array, layout: Array }>`
- Description: Returns ADC sample data, travel data, key status, and keyboard-map structures in `details` and `layout` for direct UI rendering.

### Example

```javascript
const adc = await ServiceKeyboard.getADCSample();
console.log('ADC sample:', adc);
console.log('Pressed key count:', adc.pressedCount);
```

## Related Category

TIP

- If you need keyboard-wide switch details, the fine-tunable switch list, or per-key custom switch assignment, see [Switch Related Functions](./axis-calibration.md).