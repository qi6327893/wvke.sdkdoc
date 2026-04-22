# Switch Related Functions

## Overview

This page matches the Switch Related Functions category from the SDK debug page. These methods focus on identifying the current switch used by each key, listing fine-tunable switch models, and assigning a target switch to a specific key.

Current APIs in this category:

- `ServiceKeyboard.getAxisDetails()`
- `ServiceKeyboard.getFineTuneAxisList()`
- `ServiceKeyboard.setKeyCustomAxis(params)`

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

## Recommendations

TIP

- Use `getAxisDetails()` first when building switch-recognition pages because it returns both `details` and `layout` structures.
- Cache `getFineTuneAxisList()` when building switch pickers.
- For live debugging, calibration, and travel configuration, use [Switch Travel Functions](./performance.md) for `getADCSample()`, `calibrationStart()`, `calibrationStop()`, `getCalibrationStatus()`, `getPerformance()`, and `setPerformance()`.