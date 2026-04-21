# 轴体识别与校准

## 概述

这组接口对应最近扩展出来的轴体识别、精调、实时采样和校准能力。它们和普通的按键行程设置不同，重点是“识别当前键盘每个键用了什么轴体”“哪些轴体支持精调”“实时读取 ADC/行程数据”“触发校准流程”。

本分类当前包含以下接口：

- `ServiceKeyboard.getAxisDetails()`
- `ServiceKeyboard.getFineTuneAxisList()`
- `ServiceKeyboard.setKeyCustomAxis(params)`
- `ServiceKeyboard.getADCSample()`
- `ServiceKeyboard.calibrationStart()`
- `ServiceKeyboard.calibrationStop()`
- `ServiceKeyboard.getCalibrationStatus()`

## 获取整个键盘轴体设置详情

ServiceKeyboard.getAxisDetails()

简要描述：返回整个键盘每个按键当前使用的轴体详情。

### 返回值

• 总体类型：`Promise<{ code: number, total: number, list: Array<...>, details: Array<...>, layout: Array<Array<...>> }>`

• 描述：

- `list` 为基础列表。
- `details` 适合直接用于表格展示。
- `layout` 适合直接按键盘 MAP 渲染。
- 若图片资源可用，会返回 `images.icon / z_b / z_y`。

### 使用示例

```javascript
const axisDetails = await ServiceKeyboard.getAxisDetails();
console.log('键盘轴体设置详情:', axisDetails.details);
```

## 获取支持精调的轴体列表

ServiceKeyboard.getFineTuneAxisList()

简要描述：返回当前 SDK 内支持精调的轴体清单。

### 返回值

• 总体类型：`Promise<{ code: number, total: number, list: Array<{ axis: number, brand: string, name: string, model: string, axisRangeMax: number, axisCoefficient: number, images: { icon: string, z_b: string, z_y: string } }> }>`

### 使用示例

```javascript
const fineTuneAxisList = await ServiceKeyboard.getFineTuneAxisList();
console.log('支持精调的轴体:', fineTuneAxisList.list);
```

## 设置按键自定义轴体

ServiceKeyboard.setKeyCustomAxis(params)

简要描述：为指定按键切换到目标轴体，并可选附带最大行程与轴体系数。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| keyName | string | 按键名称，例如 `A` | 是 |
| axis | number | 轴体索引 | 是 |
| axisRangeMax | number | 轴体最大行程 | 否 |
| axisCoefficient | number | 轴体系数 | 否 |

### 使用示例

```javascript
await ServiceKeyboard.setKeyCustomAxis({
	keyName: 'A',
	axis: 24,
	axisRangeMax: 4,
	axisCoefficient: 1000
});
```

## 获取 ADC 实时采样快照

ServiceKeyboard.getADCSample()

简要描述：返回整个键盘的 ADC、行程数据、按键状态和当前按下数量，适合轮询式调试界面。

### 返回值

• 总体类型：`Promise<{ code: number, adc: number, data: number[], journeyData: number[], keyStatusData: number[], maxJourney: number, maxKeyStatus: number, pressedCount: number, total: number, details: Array<...>, layout: Array<Array<...>> }>`

### 使用示例

```javascript
const adc = await ServiceKeyboard.getADCSample();
console.log('当前按下键数量:', adc.pressedCount);
console.log('第一颗键的实时状态:', adc.layout?.[0]?.[0]);
```

## 开始 / 结束校准

ServiceKeyboard.calibrationStart()

ServiceKeyboard.calibrationStop()

简要描述：控制设备进入或退出校准流程。

### 返回值

• `calibrationStart()` 与 `calibrationStop()` 都返回：

`Promise<{ code: number, status: number, message: string }>`

### 使用示例

```javascript
const start = await ServiceKeyboard.calibrationStart();
console.log('开始校准:', start);

const stop = await ServiceKeyboard.calibrationStop();
console.log('结束校准:', stop);
```

## 获取校准状态

ServiceKeyboard.getCalibrationStatus()

简要描述：读取整个键盘的实时校准状态。当前类型定义仍保留该接口，但接口本身暂未开放。

### 返回值

• 总体类型：`Promise<{ code: number, calibrate: number, data: number[] }>`

### 说明

- 文档保留这一节是为了和当前 SDK 类型保持一致。
- 如果设备端尚未开放，前端应把它当作占位接口处理，不要依赖它做完整校准流程。

## 使用建议

TIP

- 做轴体识别页面时，优先读取 `getAxisDetails()`，它同时给出 `details` 和 `layout` 两种结构。
- 做轴体选择器时，建议缓存 `getFineTuneAxisList()` 返回结果，避免每次切换页面都重复读取。
- 做实时调试页时，`getADCSample()` 更适合轮询；常规配置页则优先使用 [轴体行程功能](./performance.md) 页面里的稳定配置接口。