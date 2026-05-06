# 功能数据接口

## 概述

这组接口对应当前 SDK 里的客户端数据能力：标准键盘布局读取、快捷功能清单查询，以及客户端可直接缓存和渲染的轴体清单。它们和“设置单键功能”不同，主要用于前端渲染键盘 UI、构建功能选择器、做键位面板初始化。

本分类当前包含以下接口：

- `ServiceKeyboard.getStandard104KeyLayout()`
- `ServiceKeyboard.getShortcutFunctionList(type)`
- `ServiceKeyboard.getFineTuneAxisList()`
- `ServiceKeyboard.getKeyboardBacklightEffectList()`
- `ServiceKeyboard.getAmbientLightEffectList()`

## 获取支持的背光灯效列表接口（客户端功能）

ServiceKeyboard.getKeyboardBacklightEffectList()

简要描述：返回当前 SDK 内置支持的键盘背光主模式与子模式列表，便于客户端直接渲染下拉框、灯效面板或模式映射表。

### 参数

此方法不需要参数。

### 返回说明

返回对象字段说明：

• `total`: 支持的背光主模式总数
• `list`: 背光灯效列表

`list` 中每一项包含：

• `mode`: 背光主模式值
• `label`: 背光主模式名称
• `childModes`: 当前主模式支持的子模式列表

`childModes` 中每一项包含：

• `value`: 子模式值，从 `1` 开始
• `label`: 子模式名称

### 使用示例

```javascript
const backlightEffects = await ServiceKeyboard.getKeyboardBacklightEffectList();
console.log('支持的背光灯效列表:', backlightEffects);
```

## 获取支持的氛围灯灯效列表接口（客户端功能）

ServiceKeyboard.getAmbientLightEffectList()

简要描述：返回当前 SDK 内置支持的氛围灯 / Logo 灯主模式列表，便于客户端直接渲染模式选择器。没有 Logo 灯的键盘，也可以把该接口结果当作静态能力表使用。

### 参数

此方法不需要参数。

### 返回说明

返回对象字段说明：

• `total`: 支持的氛围灯主模式总数
• `list`: 氛围灯灯效列表

`list` 中每一项包含：

• `mode`: 氛围灯主模式值
• `label`: 氛围灯主模式名称
• `alias`: 协议别名，如 `L0` 到 `L9`

### 使用示例

```javascript
const ambientEffects = await ServiceKeyboard.getAmbientLightEffectList();
console.log('支持的氛围灯灯效列表:', ambientEffects);
```

## 获取标准 104 键盘布局

ServiceKeyboard.getStandard104KeyLayout()

简要描述：获取标准 104 键键盘布局数据。该接口属于客户端封装能力，不依赖设备实时返回。

### 参数

无

### 返回值

• 总体类型：`Promise<{ code: number, total: number, rows: Array<Array<{ row: number, col: number, hid: string, name: string, value: string, vkCode?: number, scanCode?: number }>>, list: Array<{ row: number, col: number, hid: string, name: string, value: string, vkCode?: number, scanCode?: number }> }>`

• 描述：

- `rows` 适合直接渲染标准键盘矩阵。
- `list` 适合做搜索、下拉列表、键位选择弹层。
- `vkCode` / `scanCode` 仅在可解析时返回。

### 使用示例

```javascript
const layout104 = await ServiceKeyboard.getStandard104KeyLayout();
console.log('标准 104 键布局:', layout104.rows);
```

## 获取快捷功能列表

ServiceKeyboard.getShortcutFunctionList(type)

简要描述：按分类获取快捷功能列表。该接口同样属于客户端封装能力，适合驱动或网页端生成功能菜单。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| type | string | 功能分类，只支持 `system`、`mouse`、`media`、`firmware`、`hotkey` | 是 |

### 返回值

• 总体类型：`Promise<{ code: number, type: string, total: number, list: Array<{ index: number, type: string, name: string, code: string, task: string, id: number, commandType: number, image: string }> }>`

• 描述：

- `code` 是原始协议值。
- `task` 是热键组合，仅 `hotkey` 类型有值。
- `id` 与 `commandType` 是便于前端直接处理的拆解字段。
- `image` 为功能图标资源地址；无图标时为空字符串。

### 使用示例

```javascript
const mediaShortcuts = await ServiceKeyboard.getShortcutFunctionList('media');
console.log('媒体功能列表:', mediaShortcuts.list);
```

## 获取支持精调的轴体列表

ServiceKeyboard.getFineTuneAxisList()

简要描述：返回当前 SDK 内支持精调的轴体清单。该接口属于驱动客户端功能，适合客户端提前加载并缓存，用于轴体选择器、轴体资料展示或轴体参数面板。

### 参数

无

### 返回值

• 总体类型：`Promise<{ code: number, total: number, list: Array<{ axis: number, brand: string, name: string, model: string, axisRangeMax: number, axisCoefficient: number, images: { icon: string, z_b: string, z_y: string } }> }>`

• 描述：

- `axis` 是轴体索引，可用于 `setKeyCustomAxis(params)` 的 `axis` 字段。
- `brand` / `name` / `model` 可用于客户端展示轴体品牌、名称和型号。
- `axisRangeMax` / `axisCoefficient` 是轴体默认参数，可作为自定义轴体配置的参考值。
- `images` 返回轴体图标和结构图片资源地址。

### 使用示例

```javascript
const fineTuneAxisList = await ServiceKeyboard.getFineTuneAxisList();
console.log('支持精调的轴体:', fineTuneAxisList.list);
```

## 使用建议

TIP

- 做键位编辑器初始化时，可先读取 `getStandard104KeyLayout()` 作为标准键盘布局基础。
- 做快捷功能选择器时，建议按 `system`、`mouse`、`media`、`firmware` 分组缓存 `getShortcutFunctionList()` 结果，避免重复请求。
- 做轴体选择器时，建议缓存 `getFineTuneAxisList()` 返回结果，避免每次切换页面都重复读取。
- 做灯效选择器时，建议缓存 `getKeyboardBacklightEffectList()` 和 `getAmbientLightEffectList()` 返回结果，避免把静态枚举写死在页面里。
- 这些接口都是“渲染辅助能力”，适合和 [按键功能](./key.md)、[轴体相关功能](./axis-calibration.md) 页面中的实际写入接口配合使用。