# 轴体行程功能

## 概述

本章节严格对应调试页中的“轴体行程功能”分类，包含键盘行程配置、ADC 实时采样以及校准相关能力。

## 开始校准

ServiceKeyboard.calibrationStart()

简要描述：开始设备校准流程。

### 参数

此方法不需要参数。

### 返回值

• 总体类型：`Promise<{ status: number, message: string }>`
• 描述：返回校准开始状态。

### 使用示例

```javascript
const calibration = await ServiceKeyboard.calibrationStart();
console.log('开始校准:', calibration);
```

## 结束校准

ServiceKeyboard.calibrationStop()

简要描述：结束设备校准流程。

### 参数

此方法不需要参数。

### 返回值

• 总体类型：`Promise<{ status: number, message: string }>`
• 描述：返回校准结束状态。

### 使用示例

```javascript
const calibration = await ServiceKeyboard.calibrationStop();
console.log('结束校准:', calibration);
```

## 获取校准状态

ServiceKeyboard.getCalibrationStatus()

简要描述：获取整个键盘的实时校准状态。该接口暂未开放。

### 参数

无

### 返回值

• 总体类型：`Promise<{ code: number, calibrate: number, data: number[] }>`
• 描述：当前类型定义中仍保留返回结构，但该接口暂未开放，调试页仅做占位展示。

### 使用示例

```javascript
const status = await ServiceKeyboard.getCalibrationStatus();
console.log('校准状态:', status);
```

## 获取键盘行程配置

ServiceKeyboard.getPerformance()

简要描述：获取整个键盘的行程配置：触发位置、RT 开关状态、RT 默认设置的值、安全区设置的值、按键工作模式。

### 参数

无

### 返回值

• 总体类型：`Promise<{ code: number, mode: number, normalPress: number, normalRelease: number, rtFirstTouch: number, rtPress: number, rtRelease: number, pressDeadStroke: number, releaseDeadStroke: number, axis?: number, axisRangeMax?: number, axisCoefficient?: number, calibrate: number, total: number, details: Array<...>, layout: Array<Array<...>> }>`
• 描述：返回设备当前读取到的整张键盘行程配置。其中顶层字段表示当前键盘的全局公共值；`details` 返回扁平化的按键明细；`layout` 返回按键 MAP 结构，每个按键项都包含 `triggerPosition`、`rtDown`、`rtUp`、`zoneSafeTop`、`zoneSafeBottom`、`workingMode` 以及对应的轴体信息。

### 使用示例

```javascript
const performance = await ServiceKeyboard.getPerformance();
console.log(performance.mode);
console.log(performance.normalPress);
console.log(performance.rtPress, performance.rtRelease);
console.log(performance.layout[0][0]);
console.log('键盘行程配置:', performance);
```

## 设置按键性能配置

ServiceKeyboard.setPerformance(params)

简要描述：设置单个按键的行程配置，包含设置按键工作模式、触发位置、按键 RT 开关、触发 RT 位置、RT 按下、RT 弹起、顶部安全区、底部安全区。未传字段会自动读取当前按键默认值，并在同一次调用里一并下发。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| keyName | string | 按键名称，例如 `A` | 是 |
| workingMode | number | 按键工作模式 | 否 |
| triggerPosition | number | 触发位置 | 否 |
| rtSwitch | number | 按键 RT 开关，0 为关闭，1 为开启 | 否 |
| triggerRtPosition | number | 触发 RT 位置 | 否 |
| rtDown | number | RT 按下 | 否 |
| rtUp | number | RT 弹起 | 否 |
| zoneSafeTop | number | 顶部安全区 | 否 |
| zoneSafeBottom | number | 底部安全区 | 否 |

### 返回值

• 总体类型：`Promise<{ code: number, keyName: string, hid: string, workingMode: number, journeyMode: number, journeyModeName: string, advancedMode: number, advancedModeName: string, rtSwitch: number, triggerPosition: number, triggerRtPosition: number, appliedTriggerPosition: number, rtDown: number, rtUp: number, zoneSafeTop: number, zoneSafeBottom: number }>`
• 描述：返回设置后的指定按键行程配置。除 `workingMode` 外，返回值会额外拆出 `journeyMode / advancedMode` 及其名称，便于直接判断设备端最终采用的组合模式；`appliedTriggerPosition` 表示本次实际下发到设备的触发位置。

### 使用示例

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
console.log('设置结果:', result);
```

## 获取 ADC 采样数据

ServiceKeyboard.getADCSample()

简要描述：获取整个键盘的 ADC 实时采样数据、实时行程数据和按键实时状态快照。接口内部会按顺序读取整张矩阵的行程数据、ADC 原始值和按键状态，并整理成可直接用于键盘 UI 渲染的结构。该接口适合前端定时轮询，用于展示实时按下状态变化。

### 参数

无

### 返回说明

返回对象字段说明：

• `adc`: 当前整张键盘里采集到的最大 ADC 原始值
• `data`: 整张键盘的 ADC 原始值数组，顺序与固件矩阵顺序一致
• `journeyData`: 整张键盘的实时行程数组，单位为 `mm`
• `keyStatusData`: 整张键盘的实时按键状态数组，通常 `0` 表示抬起，非 `0` 表示按下
• `maxJourney`: 当前整张键盘里采集到的最大实时行程值，单位为 `mm`
• `maxKeyStatus`: 当前最大行程对应的按键状态值
• `pressedCount`: 当前判定为按下状态的按键数量
• `total`: 当前布局中参与统计的有效按键数量
• `details`: 平铺后的按键详情数组，适合表格或列表渲染
• `layout`: 保留键盘行列结构的二维数组，适合直接渲染键盘图

### details / layout 单项字段说明

`details` 和 `layout` 中的每个有效按键对象都包含以下字段：

• `row`: 行索引，从 `0` 开始
• `col`: 列索引，从 `0` 开始
• `keyName`: 按键显示名称
• `name`: 按键名称，通常与 `keyName` 一致
• `hid`: 按键 HID，格式如 `0x04`
• `journey`: 当前实时行程，单位为 `mm`
• `adc`: 当前 ADC 原始值
• `keyStatus`: 当前按键状态值
• `pressed`: 是否判定为按下，等价于 `keyStatus > 0`

### 重点说明

• 这是快照式读取接口，每次调用返回的是“当前这一刻”的整张键盘状态，不会像 `getKeyboardBacklightCustom()` 那样自动持续上报。
• 如果前端要做实时可视化，建议自行定时轮询，例如每 `100ms` 到 `300ms` 读取一次，具体频率按页面性能和设备负载权衡。
• `journeyData`、`keyStatusData` 和 `data` 更适合底层调试；如果是做 UI，优先使用已经整理好的 `details` 或 `layout`。
• `layout` 会保留键盘原始排布，适合直接绘制键盘图；`details` 是平铺结果，适合统计、筛选和表格展示。
• 在高频轮询场景中，建议页面离开后及时停止定时器，避免持续占用 HID 通道。

### 使用示例

```javascript
const adc = await ServiceKeyboard.getADCSample();
console.log('ADC 采样数据:', adc);
console.log('当前按下键数量:', adc.pressedCount);
console.log('最大行程:', adc.maxJourney);
console.log('第一个按键详情:', adc.details[0]);
```

示例输出结构：

```javascript
{
	code: 0,
	adc: 3210,
	maxJourney: 3.72,
	maxKeyStatus: 1,
	pressedCount: 2,
	total: 61,
	data: [110, 95, 3210],
	journeyData: [0, 0.12, 3.72],
	keyStatusData: [0, 0, 1],
	details: [
		{
			row: 0,
			col: 0,
			keyName: 'Esc',
			name: 'Esc',
			hid: '0x29',
			journey: 0,
			adc: 110,
			keyStatus: 0,
			pressed: false
		}
	],
	layout: [[/* 保留键盘排布的二维数组 */]]
}
```

### 轮询示例

```javascript
let timer = null;

const pollAdcSample = async () => {
	try {
		const snapshot = await ServiceKeyboard.getADCSample();
		console.log('当前按下键数量:', snapshot.pressedCount);
		console.log('当前最大 ADC:', snapshot.adc);
		// 在这里刷新键盘 UI
	} catch (error) {
		console.error('读取 ADC 采样失败:', error);
	}
};

await pollAdcSample();
timer = setInterval(pollAdcSample, 200);

// 页面销毁时清理
clearInterval(timer);
```

### 调试工具实操

• 先在调试工具中调用 `init` 完成设备初始化。
• 在左侧选择 `getADCSample`。
• 点击一次 `调用`，可读取当前整张键盘的 ADC、实时行程和按下状态快照。
• 点击 `开始监听` 后，调试工具会定时轮询 `getADCSample`，并把实时按下状态同步到键盘图。
• 页面中的“实时采样键盘图”会高亮当前按下按键，并展示每个键的实时行程、ADC 和状态。
• 页面下方还会显示按下键数、最大行程、最大 ADC 等汇总信息，以及原始 JSON 结果，便于协议调试。
• 不再需要实时预览时，点击 `停止监听`，避免继续轮询设备。

## 相关分类

TIP

- 如果你要读取整张键盘的轴体详情、支持精调的轴体清单，或为某个按键切换目标轴体，请改看 [轴体相关功能](./axis-calibration.md)。