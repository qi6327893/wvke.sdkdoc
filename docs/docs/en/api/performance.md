# Performance

## Overview

This section covers keyboard switch-travel APIs, including keyboard travel configuration, keyboard-wide axis-setting details, the list of fine-tunable switches, per-key custom switch settings, ADC samples, and calibration.

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

## Get Keyboard Axis Details

ServiceKeyboard.getAxisDetails()

Brief: Gets the current switch-setting details for every key on the keyboard.

### Parameters

None.

### Returns

- Overall type: `Promise<{ code: number, total: number, list: Array<{ row?: number, col?: number, keyName: string, hid: string, axis: number, routeAxis: number, brand: string, name: string, model: string, axisRangeMax: number, axisCoefficient: number, supportsFineTune: boolean, images?: { icon: string, z_b: string, z_y: string } }>, details: Array<{ row: number, col: number, keyName: string, name: string, value: string, hid: string, axis: number, routeAxis: number, brand: string, model: string, axisRangeMax: number, axisCoefficient: number, supportsFineTune: boolean, images?: { icon: string, z_b: string, z_y: string } }>, layout: Array<Array<{ row: number, col: number, keyName: string, name: string, value: string, hid: string, axis: number, routeAxis: number, brand: string, model: string, axisRangeMax: number, axisCoefficient: number, supportsFineTune: boolean, images?: { icon: string, z_b: string, z_y: string } }>> }>`
- Description: Returns keyboard-wide switch-setting details. `list` is the base list, `details` is the flattened per-key dataset for table rendering, and `layout` is the same data shaped as the current keyboard MAP. When image assets are available, each switch entry may include `images.icon / z_b / z_y`.

### Example

```javascript
const axisDetails = await ServiceKeyboard.getAxisDetails();
console.log('Keyboard axis details:', axisDetails);
```

## Get Fine-Tunable Switch List

ServiceKeyboard.getFineTuneAxisList()

Brief: Gets the list of switches supported for fine tuning.

### Parameters

None.

### Returns

- Overall type: `Promise<{ code: number, total: number, list: Array<{ axis: number, brand: string, name: string, model: string, axisRangeMax: number, axisCoefficient: number, images: { icon: string, z_b: string, z_y: string } }> }>`
- Description: Returns the list of switches that support fine tuning, including max travel range, coefficient, and image resources. In development, the image fields point to directly accessible source assets; in build output, they resolve to runtime-decrypted URLs.

### Example

```javascript
const axisList = await ServiceKeyboard.getFineTuneAxisList();
console.log('Fine-tunable switch list:', axisList);
```

## Set Custom Switch For Key

ServiceKeyboard.setKeyCustomAxis(params)

Brief: Sets a custom switch for the specified key.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| keyName | string | Key name, such as A | Yes |
| axis | number | Switch index | Yes |
| axisRangeMax | number | Maximum switch travel | No |
| axisCoefficient | number | Switch coefficient | No |

### Returns

- Overall type: `Promise<{ keyName: string, hid: string, axis: number, axisRangeMax?: number, axisCoefficient?: number }>`
- Description: Returns the applied custom-switch information for the key.

### Example

```javascript
const customAxis = await ServiceKeyboard.setKeyCustomAxis({
	keyName: 'A',
	axis: 24,
	axisRangeMax: 4,
	axisCoefficient: 1000
});
console.log('Custom switch result:', customAxis);
```

## Set Key Performance

ServiceKeyboard.setPerformance(params)

Brief: Sets the travel configuration for a specific key, including working mode, trigger position, RT switch, RT trigger position, RT down, RT up, top safe zone, and bottom safe zone. Any omitted fields are automatically filled from the current key defaults and sent together in the same call.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| keyName | string | Key name, for example A | Yes |
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