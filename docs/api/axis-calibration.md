# 轴体相关功能

## 概述

这组接口对应调试页中的“轴体相关功能”分类，重点是“识别当前键盘每个键用了什么轴体”“为某个按键切换目标轴体”。

本分类当前包含以下接口：

- `ServiceKeyboard.getAxisDetails()`
- `ServiceKeyboard.setKeyCustomAxis(params)`

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

## 设置按键自定义轴体

ServiceKeyboard.setKeyCustomAxis(params)

简要描述：为指定按键（支持单个或批量）切换到目标轴体，并设置最大行程与轴体系数。

### 参数

**批量模式（推荐）** — 传入 `keys` 数组：

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| keys | Array\<{ keyName: string, hid?: string }\> | 按键数组，每项包含 `keyName`（按键名称，如 `A`）和可选的 `hid` | 是 |
| axis | number | 轴体索引，对 `keys` 中未单独指定 `axis` 的项生效 | 是 |
| axisRangeMax | number | 轴体最大行程 | 是 |
| axisCoefficient | number | 轴体系数 | 是 |

`keys` 数组中每项也可单独覆盖 `axis` / `axisRangeMax` / `axisCoefficient`。

### 使用示例

```javascript
// 批量模式 — 为多个按键统一设置轴体
await ServiceKeyboard.setKeyCustomAxis({
	keys: [
		{ keyName: 'W', hid: '0x1A' },
		{ keyName: 'A', hid: '0x04' },
		{ keyName: 'S', hid: '0x16' },
		{ keyName: 'D', hid: '0x07' }
	],
	axis: 24,
	axisRangeMax: 4,
	axisCoefficient: 1000
});

```

## 使用建议

TIP

- 做轴体识别页面时，优先读取 `getAxisDetails()`，它同时给出 `details` 和 `layout` 两种结构。
- 做轴体选择器时，请先到 [客户端数据接口](./key-layout.md) 读取 `getFineTuneAxisList()` 支持精调的轴体清单。
- 做实时调试、校准和行程配置时，请转到 [轴体行程功能](./performance.md) 页面查看 `getADCSample()`、`calibrationStart()`、`calibrationStop()`、`getCalibrationStatus()`、`getPerformance()` 与 `setPerformance()`。