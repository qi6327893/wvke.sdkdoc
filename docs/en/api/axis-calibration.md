# Switch Detection & Calibration

## Overview

This category groups the newer switch-detection, fine-tuning, sampling, and calibration APIs. These methods are different from ordinary per-key travel settings because they focus on identifying what switch is used on each key, listing fine-tunable switch models, reading live ADC/travel state, and controlling calibration flows.

Current APIs in this category:

- `ServiceKeyboard.getAxisDetails()`
- `ServiceKeyboard.getFineTuneAxisList()`
- `ServiceKeyboard.setKeyCustomAxis(params)`
- `ServiceKeyboard.getADCSample()`
- `ServiceKeyboard.calibrationStart()`
- `ServiceKeyboard.calibrationStop()`
- `ServiceKeyboard.getCalibrationStatus()`

## Get Keyboard Switch Details

ServiceKeyboard.getAxisDetails()

Brief: Returns the current switch metadata used by every key on the keyboard.

### Returns

- Overall type: `Promise<{ code: number, total: number, list: Array<...>, details: Array<...>, layout: Array<Array<...>> }>`

- Notes:

- `list` is the base dataset.
- `details` is suitable for tables.
- `layout` is suitable for keyboard-map rendering.
- When assets are available, switch entries may include `images.icon / z_b / z_y`.

### Example

```javascript
const axisDetails = await ServiceKeyboard.getAxisDetails();
console.log('Keyboard switch details:', axisDetails.details);
```

## Get Fine-Tunable Switch List

ServiceKeyboard.getFineTuneAxisList()

Brief: Returns the switch catalog currently supported for fine tuning.

### Returns

- Overall type: `Promise<{ code: number, total: number, list: Array<{ axis: number, brand: string, name: string, model: string, axisRangeMax: number, axisCoefficient: number, images: { icon: string, z_b: string, z_y: string } }> }>`

### Example

```javascript
const fineTuneAxisList = await ServiceKeyboard.getFineTuneAxisList();
console.log('Fine-tunable switches:', fineTuneAxisList.list);
```

## Set Custom Switch For Key

ServiceKeyboard.setKeyCustomAxis(params)

Brief: Assigns a target switch model to a specific key, optionally including max travel and coefficient values.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| keyName | string | Key name such as `A` | Yes |
| axis | number | Switch index | Yes |
| axisRangeMax | number | Maximum switch travel | No |
| axisCoefficient | number | Switch coefficient | No |

### Example

```javascript
await ServiceKeyboard.setKeyCustomAxis({
	keyName: 'A',
	axis: 24,
	axisRangeMax: 4,
	axisCoefficient: 1000
});
```

## Get ADC Sample Snapshot

ServiceKeyboard.getADCSample()

Brief: Returns keyboard-wide ADC values, travel data, key status, and pressed-key count. This method is suitable for polling-based debug views.

### Returns

- Overall type: `Promise<{ code: number, adc: number, data: number[], journeyData: number[], keyStatusData: number[], maxJourney: number, maxKeyStatus: number, pressedCount: number, total: number, details: Array<...>, layout: Array<Array<...>> }>`

### Example

```javascript
const adc = await ServiceKeyboard.getADCSample();
console.log('Pressed key count:', adc.pressedCount);
console.log('First key live snapshot:', adc.layout?.[0]?.[0]);
```

## Start / Stop Calibration

ServiceKeyboard.calibrationStart()

ServiceKeyboard.calibrationStop()

Brief: Enters or exits the device calibration flow.

### Returns

- Both methods return:

`Promise<{ code: number, status: number, message: string }>`

### Example

```javascript
const start = await ServiceKeyboard.calibrationStart();
console.log('Calibration started:', start);

const stop = await ServiceKeyboard.calibrationStop();
console.log('Calibration stopped:', stop);
```

## Get Calibration Status

ServiceKeyboard.getCalibrationStatus()

Brief: Reads the real-time calibration status of the entire keyboard. The type definition still exposes this method, but the actual API is not yet open.

### Returns

- Overall type: `Promise<{ code: number, calibrate: number, data: number[] }>`

### Notes

- This page keeps the method documented so the docs stay aligned with the current SDK types.
- If firmware support is not yet available, treat it as a placeholder API and do not depend on it for a full calibration workflow.

## Recommendations

TIP

- Use `getAxisDetails()` first when building switch-recognition pages because it returns both `details` and `layout` structures.
- Cache `getFineTuneAxisList()` when building switch pickers.
- Use `getADCSample()` for live debug dashboards; for stable per-key configuration, keep using [Switch Travel Functions](./performance.md).