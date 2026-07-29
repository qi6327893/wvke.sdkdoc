# 轴体行程功能

## 概述

本章节严格对应 SDK 调试器中的“轴体行程功能”分类，接口顺序与调试器注册顺序一致：

1. `calibrationStart`
2. `calibrationStop`
3. `getCalibrationStatus`
4. `getPerformance`
5. `setPerformance`
6. `getADCSample`

这些接口用于设备校准、键盘行程配置、RT 配置以及 ADC 和实时行程数据读取。

## 开始校准

`ServiceKeyboard.calibrationStart()`

调试器描述：开始设备校准流程，并可监听实时校准状态。

### 参数

无。

### 返回值

返回 `Promise<{ code: number, status: number, message: string }>`。

- `status`: `0` 表示开始校准命令已完成发送。
- `message`: 固件返回的状态描述，通常为 `Calibration started`。

### 使用示例

```javascript
const result = await ServiceKeyboard.calibrationStart();
console.log('开始校准:', result);
```

## 结束校准

`ServiceKeyboard.calibrationStop()`

调试器描述：结束设备校准流程。

### 参数

无。

### 返回值

返回 `Promise<{ code: number, status: number, message: string }>`。

- `status`: `0` 表示结束校准命令已完成发送。
- `message`: 固件返回的状态描述，通常为 `Calibration finished`。

### 使用示例

```javascript
const result = await ServiceKeyboard.calibrationStop();
console.log('结束校准:', result);
```

## 获取校准状态

`ServiceKeyboard.getCalibrationStatus()`

调试器描述：获取整个键盘的实时校准状态。

### 参数

无。

### 返回值

返回 `Promise<Object>`，包含以下字段：

- `calibrate`: SDK 记录的校准流程状态，`1` 表示已开始，`0` 表示未开始或已结束。
- `data`: 整张键盘的有效校准状态数组，等同于 `calibrationStatusData`。
- `keyStatusData`: 按键状态数组，仅保留状态值低 4 位；通常 `0` 表示抬起，非 `0` 表示按下。
- `calibrationStatusData`: 按键校准状态数组，来自状态值高 4 位，并包含旧固件兼容处理。
- `maxKeyStatus`: 当前最大的按键状态值。
- `maxCalibrationStatus`: 当前最大的校准状态值。
- `pressedCount`: 当前判定为按下的按键数量。
- `calibratedCount`: 当前已校准的按键数量。
- `currentRoundCalibratedCount`: 当前轮次已校准的按键数量。
- `total`: 当前布局中参与统计的有效按键数量。
- `details`: 平铺后的按键详情数组，适合表格或列表渲染。
- `layout`: 保留键盘行列结构的二维数组，适合直接渲染键盘图。

`details` 和 `layout` 中每个有效按键对象包含：`row`、`col`、`keyName`、`name`、`hid`、`keyStatus`、`calibrationStatus`、`calibrated`、`currentRoundCalibrated` 和 `pressed`。

### 状态值说明

- 按键状态取原始状态值低 4 位，校准状态取原始状态值高 4 位。
- 新固件中，校准状态的 `0x08` 表示已校准，`0x04` 表示本轮已校准；两个标志同时存在时通常为 `0x0c`。
- 为兼容旧固件，当高 4 位为 `0` 且低 4 位返回 `2` 时，SDK 会按校准成功处理。

### 使用示例

```javascript
const status = await ServiceKeyboard.getCalibrationStatus();
console.log('已校准键数:', status.calibratedCount);
console.log('本轮已校准键数:', status.currentRoundCalibratedCount);
console.log('第一个按键状态:', status.details[0]);
```

## 获取键盘行程配置

`ServiceKeyboard.getPerformance()`

调试器描述：获取整个键盘的行程配置：触发位置、RT 开关状态、RT 默认设置的值、安全区设置的值、按键工作模式。

### 参数

无。

### 返回值

返回 `Promise<Object>`，包含以下字段：

- `mode`: 当前键盘 RT 全行程模式。
- `normalPress`: 普通触发位置。
- `normalRelease`: 普通释放位置。
- `rtFirstTouch`: RT 首次触发位置。
- `rtPress`: RT 按下位置。
- `rtRelease`: RT 弹起位置。
- `pressDeadStroke`: 顶部安全区。
- `releaseDeadStroke`: 底部安全区。
- `axis`: 当前键盘轴体编号。
- `systemType`: 当前系统类型。
- `axisRangeMax`: 当前轴体最大行程，可能不存在。
- `axisCoefficient`: 当前轴体系数，可能不存在。
- `calibrate`: 当前 SDK 记录的校准状态。
- `total`: 有效按键数量。
- `details`: 平铺后的按键行程配置数组。
- `layout`: 保留键盘行列结构的二维行程配置数组。

每个 `details` 和 `layout` 按键项包含触发位置、RT 参数、安全区、工作模式以及对应轴体信息。

### 使用示例

```javascript
const performance = await ServiceKeyboard.getPerformance();
console.log('键盘行程配置:', performance);
console.log('普通触发位置:', performance.normalPress);
console.log('RT 按下/弹起:', performance.rtPress, performance.rtRelease);
```

## 设置按键行程配置

`ServiceKeyboard.setPerformance(params)`

调试器描述：设置单个按键的行程配置：包含设置按键工作模式、触发位置、按键 RT 开关、触发 RT 位置、RT 按下、RT 弹起、顶部安全区、底部安全区；未传字段会自动补当前按键默认值并一起下发。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| `keyName` | `string` | 按键名称，例如 `A`。 | 是 |
| `workingMode` | `number` | 按键工作模式。也可使用兼容字段 `mode`。 | 否 |
| `triggerPosition` | `number` | 普通触发位置。 | 否 |
| `rtSwitch` | `number` | RT 开关：`0`=关闭，`1`=受全局影响，`2`=全行程开关关闭时仍生效，`3`=全行程开关开启时生效。 | 否 |
| `triggerRtPosition` | `number` | RT 触发位置。 | 否 |
| `rtDown` | `number` | RT 按下位置。 | 否 |
| `rtUp` | `number` | RT 弹起位置。 | 否 |
| `zoneSafeTop` | `number` | 顶部安全区。 | 否 |
| `zoneSafeBottom` | `number` | 底部安全区。 | 否 |
| `onProgress` | `function` | 可选的分层传输进度回调。 | 否 |

### 返回值

返回 `Promise<Object>`，包含：`code`、`keyName`、`hid`、`workingMode`、`journeyMode`、`journeyModeName`、`advancedMode`、`advancedModeName`、`rtSwitch`、`triggerPosition`、`triggerRtPosition`、`appliedTriggerPosition`、`rtDown`、`rtUp`、`zoneSafeTop` 和 `zoneSafeBottom`。

- `appliedTriggerPosition`: 本次实际下发到设备的触发位置。
- `journeyMode` / `advancedMode`: 从最终工作模式拆出的行程模式和高级模式。

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

`ServiceKeyboard.getADCSample()`

调试器描述：获取整个键盘的 ADC 实时采样数据、实时行程数据。

### 参数

无。

### 返回值

返回 `Promise<Object>`，包含以下字段：

- `adc`: 整张键盘采集到的最大 ADC 原始值。
- `data`: 整张键盘的 ADC 原始值数组。
- `journeyData`: 整张键盘的实时行程数组，单位为 `mm`。
- `keyStatusData`: 整张键盘的实时按键状态数组，仅保留状态值低 4 位。
- `maxJourney`: 整张键盘采集到的最大实时行程值，单位为 `mm`。
- `maxKeyStatus`: 当前最大的按键状态值。
- `pressedCount`: 当前判定为按下的按键数量。
- `total`: 当前布局中参与统计的有效按键数量。
- `details`: 平铺后的按键详情数组。
- `layout`: 保留键盘行列结构的二维数组。

每个 `details` 和 `layout` 按键项包含：`row`、`col`、`keyName`、`name`、`hid`、`journey`、`adc`、`keyStatus` 和 `pressed`。

### 使用示例

```javascript
const sample = await ServiceKeyboard.getADCSample();
console.log('ADC 采样数据:', sample);
console.log('当前按下键数量:', sample.pressedCount);
console.log('最大行程:', sample.maxJourney);
console.log('第一个按键详情:', sample.details[0]);
```

### 实时轮询

`getADCSample()` 是快照式读取接口，不会自动持续上报。需要实时显示时，应由页面自行轮询，并在页面销毁时清理定时器：

```javascript
let timer = null;

const pollAdcSample = async () => {
  try {
    const snapshot = await ServiceKeyboard.getADCSample();
    console.log('当前按下键数量:', snapshot.pressedCount);
  } catch (error) {
    console.error('读取 ADC 采样失败:', error);
  }
};

await pollAdcSample();
timer = setInterval(pollAdcSample, 200);

// 页面销毁时清理
clearInterval(timer);
```

## 分类边界

以下接口属于调试器的“轴体相关功能”，不属于本分类：

- `ServiceKeyboard.getAxisDetails()`
- `ServiceKeyboard.setKeyCustomAxis(params)`

详情请参阅 [轴体相关功能](./axis-calibration.md)。