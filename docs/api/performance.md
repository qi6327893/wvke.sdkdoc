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

简要描述：设置指定按键的行程配置，包含设置按键工作模式、触发位置、按键 RT 开关、触发 RT 位置、RT 按下、RT 弹起、顶部安全区、底部安全区。未传字段会自动读取当前按键默认值，并在同一次调用里一并下发。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| keyName | string | 按键名称，例如 A | 是 |
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

简要描述：获取整个键盘的 ADC 实时采样数据、实时行程数据和按键实时状态快照。该接口适合前端定时轮询，用于展示实时按下状态变化。

### 参数

无

### 返回值

• 总体类型：`Promise<{ adc: number, data: number[], journeyData: number[], keyStatusData: number[], maxJourney: number, maxKeyStatus: number, pressedCount: number, total: number, details: Array, layout: Array }>`
• 描述：返回 ADC 实时采样数据、实时行程数据、按键状态，以及可直接用于键盘 UI 渲染的 `details` 与 `layout`。

### 使用示例

```javascript
const adc = await ServiceKeyboard.getADCSample();
console.log('ADC 采样数据:', adc);
console.log('当前按下键数量:', adc.pressedCount);
```

## 相关分类

TIP

- 如果你要读取整张键盘的轴体详情、支持精调的轴体清单，或为某个按键切换目标轴体，请改看 [轴体相关功能](./axis-calibration.md)。