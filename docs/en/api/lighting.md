# Lighting Effects

## Overview

This section documents the lighting APIs currently available in the SDK. `getLightingBase()` and `setLightingBase()` primarily target keyboard backlight, while `getLightingLogoBase()` and `setLightingLogoBase()` target the LOGO lighting zone. Keyboards without logo lighting can ignore all logo-lighting APIs.

## Get Supported Keyboard Backlight Effect List (Client-side)

ServiceKeyboard.getKeyboardBacklightEffectList()

Brief description: Return the built-in keyboard backlight main modes and sub-mode list supported by the current SDK. This is useful for rendering client-side selectors, lighting panels, or mode lookup tables.

### Parameters

This method does not require any parameters.

### Return Details

Returned field meanings:

• `total`: total number of supported backlight main modes
• `list`: supported backlight effect list

Each item in `list` contains:

• `mode`: backlight main mode value
• `label`: backlight main mode label
• `childModes`: supported sub-mode list for the current main mode

Each item in `childModes` contains:

• `value`: sub-mode value, starting from `1`
• `label`: sub-mode label

### Example

```javascript
const backlightEffects = await ServiceKeyboard.getKeyboardBacklightEffectList();
console.log('Supported keyboard backlight effects:', backlightEffects);
```

## Get Supported Ambient Lighting Effect List (Client-side)

ServiceKeyboard.getAmbientLightEffectList()

Brief description: Return the built-in ambient-light / logo-light main mode list supported by the current SDK. This is useful for directly rendering a mode selector on the client side.

### Parameters

This method does not require any parameters.

### Return Details

Returned field meanings:

• `total`: total number of supported ambient-light main modes
• `list`: supported ambient-light effect list

Each item in `list` contains:

• `mode`: ambient-light main mode value
• `label`: ambient-light main mode label
• `alias`: protocol alias, such as `L0` through `L9`

### Example

```javascript
const ambientEffects = await ServiceKeyboard.getAmbientLightEffectList();
console.log('Supported ambient lighting effects:', ambientEffects);
```

## Get Keyboard Backlight Base Config

ServiceKeyboard.getLightingBase()

Brief description: Retrieve the current keyboard backlight base configuration. Both brightness and speed use 5 levels.

### Parameters

This method does not require any parameters.

### Return Details

The get and set operations share the same command. The core fields are `mode`, `childMode`, `luminance`, and `speed`.

Returned field meanings:

• `mode`: main mode value
• `modeLabel`: main mode display name
• `childMode`: sub-mode sequence number
• `childModeLabel`: sub-mode display name
• `luminance`: brightness level
• `speed`: speed level

The `mode` field uses protocol enum values and currently supports `1` through `23`, plus `28`.
Common examples: `1=Static Mode`, `2=Breathing Mode`, `3=Spectrum Cycle`, `23=Roulette`, `28=Doodle Mode`.

Complete mode mapping:

| mode | Current `modeLabel` | Reference meaning |
|------|---------------------|-------------------|
| 1 | 静态模式 | Static Mode |
| 2 | 呼吸模式 | Breathing Mode |
| 3 | 光谱循环 | Spectrum Cycle |
| 4 | 正弦光波 | Sine Wave |
| 5 | 指尖涟漪1 | Ripple 1 |
| 6 | 指尖涟漪2 | Ripple 2 |
| 7 | 一触即发1 | Reactive 1 |
| 8 | 一触即发2 | Reactive 2 |
| 9 | 踏雪无痕 | Snow Trace |
| 10 | 暗流涌动 | Undercurrent |
| 11 | 浮光掠影 | Gliding Light |
| 12 | 流光溢彩 | Flowing Colors |
| 13 | 繁星点点 | Stars |
| 14 | 变换呼吸 | Variable Breathing |
| 15 | 斜风细雨 | Drizzle |
| 16 | 川流不息 | Endless Stream |
| 17 | 花开富贵 | Blooming |
| 18 | 跑马游龙 | Running Dragon |
| 19 | 漫天飞雪 | Snowfall |
| 20 | 百花齐放 | Full Blossom |
| 21 | 峰回路转 | Twists And Turns |
| 22 | 贪吃小蛇 | Snake |
| 23 | 旋转轮盘 | Roulette |
| 28 | 涂鸦模式 | Doodle Mode |

Complete sub-mode mapping:

• `mode=1 Static Mode`: `1=炫彩模式一 (Rainbow Mode 1)`、`2=炫彩模式二 (Rainbow Mode 2)`、`3=炫彩模式三 (Rainbow Mode 3)`、`4=炫彩模式四 (Rainbow Mode 4)`、`5=炫彩模式五 (Rainbow Mode 5)`、`6=炫彩模式六 (Rainbow Mode 6)`、`7=炫彩模式七 (Rainbow Mode 7)`、`8=炫彩模式八 (Rainbow Mode 8)`、`9=炫彩模式九 (Rainbow Mode 9)`、`10=单色模式 (Single Color Mode)`
• `mode=2 Breathing Mode`: `1=炫彩模式一 (Rainbow Mode 1)`、`2=炫彩模式二 (Rainbow Mode 2)`、`3=炫彩模式三 (Rainbow Mode 3)`、`4=炫彩模式四 (Rainbow Mode 4)`、`5=炫彩模式五 (Rainbow Mode 5)`、`6=炫彩模式六 (Rainbow Mode 6)`、`7=炫彩模式七 (Rainbow Mode 7)`、`8=炫彩模式八 (Rainbow Mode 8)`、`9=炫彩模式九 (Rainbow Mode 9)`、`10=单色模式 (Single Color Mode)`
• `mode=3 Spectrum Cycle`: `1=模式一 (Mode 1)`、`2=模式二 (Mode 2)`
• `mode=4 Sine Wave`: `1=从左至右 (Left to Right)`、`2=由内而外 (Inside to Outside)`、`3=从右至左 (Right to Left)`
• `mode=5 Ripple 1`: `1=炫彩背景光一 (Rainbow Background 1)`、`2=炫彩背景光二 (Rainbow Background 2)`、`3=炫彩背景光三 (Rainbow Background 3)`、`4=炫彩背景光四 (Rainbow Background 4)`、`5=红色背景光 (Red Background)`、`6=橙色背景光 (Orange Background)`、`7=黄色背景光 (Yellow Background)`、`8=绿色背景光 (Green Background)`、`9=青色背景光 (Cyan Background)`、`10=蓝色背景光 (Blue Background)`、`11=紫色背景光 (Purple Background)`、`12=粉色背景光 (Pink Background)`、`13=白色背景光 (White Background)`
• `mode=6 Ripple 2`: `1=背景光关闭 (Background Off)`、`2=背景光打开 (Background On)`
• `mode=7 Reactive 1`: `1=炫彩背景光一 (Rainbow Background 1)`、`2=炫彩背景光二 (Rainbow Background 2)`、`3=炫彩背景光三 (Rainbow Background 3)`、`4=炫彩背景光四 (Rainbow Background 4)`、`5=红色背景光 (Red Background)`、`6=橙色背景光 (Orange Background)`、`7=黄色背景光 (Yellow Background)`、`8=绿色背景光 (Green Background)`、`9=青色背景光 (Cyan Background)`、`10=蓝色背景光 (Blue Background)`、`11=紫色背景光 (Purple Background)`、`12=粉色背景光 (Pink Background)`、`13=白色背景光 (White Background)`
• `mode=8 Reactive 2`: `1=背景光关闭 (Background Off)`、`2=背景光打开 (Background On)`
• `mode=9 Snow Trace`: `1=持续模式一 (Continuous Mode 1)`、`2=持续模式二 (Continuous Mode 2)`、`3=触发模式三 (Trigger Mode 3)`、`4=触发模式四 (Trigger Mode 4)`
• `mode=10 Undercurrent`: `1=扩散模式一 (Diffuse Mode 1)`、`2=汇聚模式一 (Converge Mode 1)`、`3=扩散模式二 (Diffuse Mode 2)`、`4=汇聚模式二 (Converge Mode 2)`
• `mode=11 Gliding Light`: `1=自上而下一 (Top to Bottom 1)`、`2=自下而下一 (Bottom to Top 1)`、`3=自上而下二 (Top to Bottom 2)`、`4=自下而下二 (Bottom to Top 2)`
• `mode=12 Flowing Colors`: `1=从左至右一 (Left to Right 1)`、`2=从右至左二 (Right to Left 2)`、`3=从左至右一 (Left to Right 1)`、`4=从右至左二 (Right to Left 2)`
• `mode=13 Stars`: `1=背景光关闭 (Background Off)`、`2=背景光打开 (Background On)`
• `mode=14 Variable Breathing`: `1=模式一 (Mode 1)`、`2=模式二 (Mode 2)`
• `mode=15 Drizzle`: `1=背景光关闭 (Background Off)`、`2=背景光打开 (Background On)`
• `mode=16 Endless Stream`: `1=背景光关闭 (Background Off)`、`2=背景光打开 (Background On)`
• `mode=17 Blooming`: `1=背景光关闭 (Background Off)`、`2=背景光打开 (Background On)`
• `mode=18 Running Dragon`: `1=背景光关闭 (Background Off)`、`2=背景光打开 (Background On)`
• `mode=19 Snowfall`: `1=背景光关闭 (Background Off)`、`2=背景光打开 (Background On)`
• `mode=20 Full Blossom`: `1=模式一 (Mode 1)`、`2=模式二 (Mode 2)`
• `mode=21 Twists And Turns`: `1=顺时针模式一 (Clockwise Mode 1)`、`2=逆时针模式一 (Counterclockwise Mode 1)`、`3=顺时针模式二 (Clockwise Mode 2)`、`4=逆时针模式二 (Counterclockwise Mode 2)`
• `mode=22 Snake`: `1=背景光关闭 (Background Off)`、`2=背景光打开 (Background On)`
• `mode=23 Roulette`: `1=顺时针模式一 (Clockwise Mode 1)`、`2=逆时针模式一 (Counterclockwise Mode 1)`、`3=顺时针模式二 (Clockwise Mode 2)`、`4=逆时针模式二 (Counterclockwise Mode 2)`
• `mode=28 Doodle Mode`: `1=模式一 (Mode 1)`、`2=模式二 (Mode 2)`

The `luminance` and `speed` fields are both level values ranging from `1` to `5`.

• `1` means the lowest level
• `5` means the highest level

### Example

```javascript
const lightingBase = await ServiceKeyboard.getLightingBase();
console.log('Keyboard backlight base config:', lightingBase);
// Example output: { code: 0, mode: 2, modeLabel: '呼吸模式', childMode: 1, childModeLabel: '炫彩模式一', luminance: 3, speed: 4 }
```

## Set Keyboard Backlight Base Config

ServiceKeyboard.setLightingBase(params)

Brief description: Update the keyboard backlight base configuration. Both brightness and speed use 5 levels.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| data | object | Keyboard backlight base config object containing `mode`, `childMode`, `luminance`, and `speed` | Yes |

`data` fields:

• `mode`: main mode value, supporting `1` through `23`, plus `28`
• `childMode`: sub-mode index for the selected main mode, starting from `1`; this option can be ignored for single-color lighting
• `luminance`: brightness level from `1` to `5`
• `speed`: speed level from `1` to `5`

### Example

```javascript
const result = await ServiceKeyboard.setLightingBase({
	data: {
		mode: 2,
		childMode: 1,
		luminance: 3,
		speed: 4
	}
});
console.log('Set keyboard backlight base config:', result);
```

### Notes

TIP

• `luminance` only supports level values from `1` to `5`
• `speed` only supports level values from `1` to `5`
• `mode` and `childMode` should use protocol enum values; the response includes `modeLabel` and `childModeLabel` for direct UI display
• If the selected effect is single-color lighting, `childMode` can stay at its default value and can be ignored

## Get Custom Keyboard Backlight Matrix

ServiceKeyboard.getKeyboardBacklightCustom()

Brief description: Retrieve the custom keyboard backlight data. The SDK sends the keyboard backlight base command with its operation byte switched to `0x02`, which triggers the device to stream current per-key RGB data back. After the request is sent, the keyboard automatically reports lighting updates in real time, so the frontend can render the UI directly.

### Parameters

This method does not require any parameters.

### Return Details

Returned field meanings:

• `data`: custom lighting data array, where each item uses the fixed format `[keyHid, r, g, b]`
• `readKeys`: number of keys successfully read in this operation
• `packetCount`: number of HID packets received while reading

### Important Notes

• After calling `getKeyboardBacklightCustom()`, the device keeps streaming lighting data for a period of time instead of returning only a single packet.
• If the frontend only needs a one-time snapshot, waiting for the Promise result is enough.
• If the frontend needs continuous UI rendering, it must also listen asynchronously to the `keyboardBacklightCustomReport` event.
• The frontend must implement heartbeat logic and should call `getKeyboardBacklightCustom()` again every `5000ms` to re-trigger continuous real-time reporting.
• When the page is destroyed or streaming is no longer needed, remove the event listener and clear the heartbeat timer.

### Example

```javascript
const customLighting = await ServiceKeyboard.getKeyboardBacklightCustom();
console.log('Custom keyboard backlight matrix:', customLighting);
// Example output: { code: 0, data: [['04', 'ff', '00', '00'], ['05', '00', 'ff', '00']], readKeys: 2, packetCount: 1 }
```

### Continuous Rendering Example

```javascript
const handleLightingReport = (payload) => {
	console.log('Real-time lighting report:', payload.data);
	// Update your UI here
};

ServiceKeyboard.on('keyboardBacklightCustomReport', handleLightingReport);

await ServiceKeyboard.getKeyboardBacklightCustom();

const heartbeatTimer = setInterval(() => {
	ServiceKeyboard.getKeyboardBacklightCustom().catch((error) => {
		console.error('Custom backlight heartbeat failed:', error);
	});
}, 5000);

// Cleanup on unmount
ServiceKeyboard.off('keyboardBacklightCustomReport', handleLightingReport);
clearInterval(heartbeatTimer);
```

### Debug Tool Walkthrough

• Call `init` in the debug tool first to initialize the device.
• Select `getKeyboardBacklightCustom` from the left panel.
• Click `Invoke` once for a one-time read and inspect the current snapshot result.
• Click `Start Listening` to subscribe to `keyboardBacklightCustomReport`; the debug tool will also send a heartbeat request automatically every `5000ms`.
• The “Key Color Grid” renders the current per-key colors using the default keyboard layout, while the raw JSON report remains visible below for protocol debugging.
• When live rendering is no longer needed, click `Stop Listening`; the page will remove the listener and clear the heartbeat timer automatically.

## Set Custom Keyboard Backlight Matrix

ServiceKeyboard.setKeyboardBacklightCustom(params)

Brief description: Update the custom keyboard backlight matrix. The SDK automatically switches keyboard backlight to Doodle Mode before writing, and the keyboard-backlight Doodle Mode index is fixed at `28`.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| data | array | Custom lighting data array. Each item uses the format [keyHid, r, g, b] | Yes |

### Example

```javascript
await ServiceKeyboard.setKeyboardBacklightCustom({
	data: [["04", "ff", "00", "00"], ["05", "00", "ff", "00"]]
});
```

## Get Logo Lighting Base Config

ServiceKeyboard.getLightingLogoBase()

Brief description: Retrieve the current logo lighting base configuration. The returned fields are `mode`, `luminance`, and `speed`; logo lighting does not support `childMode`. Keyboards without logo lighting can ignore this API.

### Parameters

This method does not require any parameters.

### Return Details

Returned field meanings:

• `mode`: main mode value
• `modeLabel`: main mode display name
• `luminance`: brightness level
• `speed`: speed level

The logo lighting base configuration uses the same enum and level ranges:

• `mode` uses a dedicated logo-mode enum
• `luminance` supports `1` through `5`
• `speed` supports `1` through `5`

`getLightingLogoBase` and `setLightingLogoBase` use the same logo-mode enum and support `L0` through `L9`, where `L0` is Doodle Mode.

Logo mode mapping:

| mode | Protocol alias | Name |
|------|----------------|------|
| 0 | L0 | Doodle Mode |
| 1 | L1 | Rainbow Flow |
| 2 | L2 | Spectrum Shift |
| 3 | L3 | Starlight |
| 4 | L4 | Color Scan |
| 5 | L5 | Static Rainbow |
| 6 | L6 | Rainbow Breathing |
| 7 | L7 | Color Shift |
| 8 | L8 | Rainbow Patrol |
| 9 | L9 | Dual Rainbow |

### Example

```javascript
const logoBase = await ServiceKeyboard.getLightingLogoBase();
console.log('Logo lighting base config:', logoBase);
```

## Set Logo Lighting Base Config

ServiceKeyboard.setLightingLogoBase(params)

Brief description: Update the current logo lighting base configuration. Logo lighting does not support `childMode`. Keyboards without logo lighting can ignore this API.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| data | object | Logo lighting base config object containing `mode`, `luminance`, and `speed` | Yes |

`data` fields:

• `mode`: main mode value, supporting `L0` through `L9`, where `L0` is Doodle Mode
• `luminance`: brightness level from `1` to `5`
• `speed`: speed level from `1` to `5`

### Example

```javascript
const logoResult = await ServiceKeyboard.setLightingLogoBase({
	data: {
		mode: 1,
		luminance: 3,
		speed: 4
	}
});
console.log('Set logo lighting base config:', logoResult);
```

## Get Custom Logo Lighting

ServiceKeyboard.getLightingLogoCustom()

Brief description: Retrieve the custom logo lighting data. The SDK sends the custom-logo command with its operation byte switched to `0x02`, which triggers the device to stream the current logo RGB data back. After the request is sent, the device automatically reports logo-lighting updates in real time, so the frontend can render the UI directly. Keyboards without logo lighting can ignore this API.

### Parameters

This method does not require any parameters.

### Return Details

Returned field meanings:

• `data`: logo RGB byte array in the fixed format `[r, g, b]`
• `packetCount`: number of HID packets received while reading

### Important Notes

• After calling `getLightingLogoCustom()`, the device keeps streaming logo-lighting data for a period of time instead of returning only a single packet.
• If the frontend only needs a one-time snapshot, waiting for the Promise result is enough.
• If the frontend needs continuous UI rendering, it must also listen asynchronously to the `lightingLogoCustomReport` event.
• The frontend must implement heartbeat logic and should call `getLightingLogoCustom()` again every `5000ms` to re-trigger continuous real-time reporting.
• When the page is destroyed or streaming is no longer needed, remove the event listener and clear the heartbeat timer.

### Example

```javascript
const logoLighting = await ServiceKeyboard.getLightingLogoCustom();
console.log('Custom logo lighting:', logoLighting);
// Example output: { code: 0, data: ['80', '29', '31'], packetCount: 1 }
```

### Continuous Rendering Example

```javascript
const handleLogoLightingReport = (payload) => {
	console.log('Real-time logo lighting report:', payload.data);
	// Update your UI here
};

ServiceKeyboard.on('lightingLogoCustomReport', handleLogoLightingReport);

await ServiceKeyboard.getLightingLogoCustom();

const heartbeatTimer = setInterval(() => {
	ServiceKeyboard.getLightingLogoCustom().catch((error) => {
		console.error('Custom logo heartbeat failed:', error);
	});
}, 5000);

// Cleanup on unmount
ServiceKeyboard.off('lightingLogoCustomReport', handleLogoLightingReport);
clearInterval(heartbeatTimer);
```

### Debug Tool Walkthrough

• Call `init` in the debug tool first to initialize the device.
• Select `getLightingLogoCustom` from the left panel.
• Click `Invoke` once for a one-time read and inspect the current logo snapshot result.
• Click `Start Listening` to subscribe to `lightingLogoCustomReport`; the debug tool will also send a heartbeat request automatically every `5000ms`.
• The “Logo Color Preview” shows the latest received logo color, while the raw JSON report remains visible below for protocol debugging.
• When live rendering is no longer needed, click `Stop Listening`; the page will remove the listener and clear the heartbeat timer automatically.

## Set Custom Logo Lighting

ServiceKeyboard.setLightingLogoCustom(params)

Brief description: Update the custom logo lighting data. Before writing, the SDK automatically switches the logo light into custom doodle mode; the protocol mode ID used by this API is fixed at `0`. Keyboards without logo lighting can ignore this API.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| data | array | Logo RGB data in the fixed format [r, g, b] | Yes |

### Return Details

Returned field meanings:

• `data`: the logo RGB byte array that was written, in the fixed format `[r, g, b]`
• `packetCount`: number of HID packets sent in this write operation, currently fixed at `1`

### Example

```javascript
await ServiceKeyboard.setLightingLogoCustom({
	data: ["80", "29", "31"]
});
```

