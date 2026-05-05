# 键位布局与快捷功能

## 概述

这组接口对应当前 SDK 里新增出来的一类客户端能力：键位布局读取、层级映射读取，以及快捷功能清单查询。它们和“设置单键功能”不同，主要用于前端渲染键盘 UI、构建功能选择器、做键位面板初始化。

本分类当前包含以下接口：

- `ServiceKeyboard.getKeyCustomLayer(layer)`
- `ServiceKeyboard.getStandard104KeyLayout()`
- `ServiceKeyboard.getShortcutFunctionList(type)`

## 获取按键自定义层功能

ServiceKeyboard.getKeyCustomLayer(layer)

简要描述：获取指定层级的按键自定义功能映射，当前支持基本层、FN1、FN2、FN3 层。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| layer | number | 层级值。基本层：`0x00`，FN1 层：`0x01`，FN2 层：`0x02`，FN3 层：`0x03` | 是 |

### 返回值

• 总体类型：`Promise<{ code: number, layer: number, list: Array<{ hid: string, type: string, name: string, content: string, image: string, fn_code?: string }>, layout: any[] }>`

• 描述：

- `list` 返回当前层的扁平化功能列表。
- `layout` 返回可直接用于键盘 MAP 渲染的二维结构。
- `image` 为对应功能图标资源地址；如果当前项没有图标，则为空字符串。

### 使用示例

```javascript
const baseLayer = await ServiceKeyboard.getKeyCustomLayer(0x00);
console.log('基础层功能映射:', baseLayer);
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

## 使用建议

TIP

- 做键位编辑器初始化时，通常先读取 `getStandard104KeyLayout()` 或设备真实布局，再结合 `getKeyCustomLayer()` 渲染当前层。
- 做快捷功能选择器时，建议按 `system`、`mouse`、`media`、`firmware` 分组缓存 `getShortcutFunctionList()` 结果，避免重复请求。
- 这几个接口都是“渲染辅助能力”，适合和 [按键功能](./key.md) 页面中的实际写入接口配合使用。