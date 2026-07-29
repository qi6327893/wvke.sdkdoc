# Gamepad Features

## Overview

This section covers the SDK APIs related to gamepad mode, mapping settings, curve options, and linear curve configuration.

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

• Overall type: `Promise<{ code: number, enableAngleAdjustment: number, enableSquareStickOutput: number, enableExtremePriority: number, angleAdjustmentValue: number }>`
• Description: Returns a `Promise` that resolves to the current gamepad curve options.

| Field | Type | Description | Example |
|------|------|------|------|
| code | number | API status code. `0` means success. | 0 |
| enableAngleAdjustment | number | Whether angle adjustment is enabled. `0` means disabled and `1` means enabled. | 0 |
| enableSquareStickOutput | number | Whether square-stick output is enabled. `0` means disabled and `1` means enabled. | 1 |
| enableExtremePriority | number | Whether extreme-priority mode is enabled. `0` means disabled and `1` means enabled. | 0 |
| angleAdjustmentValue | number | Angle adjustment value in degrees. Defaults to `45` when older firmware does not return this field. | 45 |

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
| settings.angleAdjustmentValue | string \| number | Angle adjustment value in degrees. Defaults to `45` when omitted, supported range is `0` to `90`. | No |
| settings.enableSquareStickOutput | string \| number \| boolean | Whether square-stick output is enabled. | Yes |
| settings.enableExtremePriority | string \| number \| boolean | Whether extreme-priority mode is enabled. | Yes |

### Return Value

• Overall type: `Promise<{ code: number, enableAngleAdjustment: number, enableSquareStickOutput: number, enableExtremePriority: number, angleAdjustmentValue: number }>`

### Example

```javascript
async function updateGamepadCurveOptions() {
	try {
		const result = await ServiceKeyboard.setGamepadCurveOptions({
			enableAngleAdjustment: 0,
			angleAdjustmentValue: 45,
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
