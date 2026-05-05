# 按键功能

## 概述

本章节介绍当前 SDK 中与按键布局和单键配置相关的 API。

## 获取键盘默认按键布局

ServiceKeyboard.getDefaultKeyLayout()

简要描述：获取键盘默认按键布局。

### 参数

此方法不需要参数。

### 返回值

• 总体类型：`Promise<any>`
• 描述：返回默认按键布局数据，具体结构取决于设备布局定义。

### 使用示例

```javascript
const layout = await ServiceKeyboard.getDefaultKeyLayout();
console.log('默认按键布局:', layout);
```

## 获取按键自定义层功能

ServiceKeyboard.getKeyCustomLayer(layer)

简要描述：获取指定层级的按键自定义功能映射，当前支持基本层、FN1、FN2、FN3 层。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| layer | number | 层级值。基本层：`0x00`，FN1 层：`0x01`，FN2 层：`0x02`，FN3 层：`0x03` | 是 |

### 返回值

• 总体类型：`Promise<{ code: number, layer: number, list: Array<{ hid: string, type: string, name: string, content: string, image: string, fn_code?: string }>, layout: any[] }>`
• 描述：返回指定层级的自定义功能列表，以及当前键位布局映射。其中 `image` 为对应自定义功能图标资源地址；若当前项没有可用图标，则返回空字符串。

### 使用示例

```javascript
const result = await ServiceKeyboard.getKeyCustomLayer(0x00);
console.log('基础层自定义功能:', result);
```

## 获取标准104键盘布局接口（客户端功能）

ServiceKeyboard.getStandard104KeyLayout()

简要描述：获取标准 104 键盘布局数据。该接口为客户端封装功能，不依赖设备实时返回。

### 参数

此方法不需要参数。

### 返回值

• 总体类型：`Promise<{ total: number, rows: Array<Array<{ row: number, col: number, hid: string, name: string, value: string, vkCode?: number, scanCode?: number }>>, list: Array<{ row: number, col: number, hid: string, name: string, value: string, vkCode?: number, scanCode?: number }> }>`
• 描述：返回标准 104 键的行列布局和扁平列表，可直接用于客户端键位选择器或布局渲染。

### 使用示例

```javascript
const layout104 = await ServiceKeyboard.getStandard104KeyLayout();
console.log('标准104键盘布局:', layout104);
```

## 获取快捷键功能列表接口（客户端功能）

ServiceKeyboard.getShortcutFunctionList(type)

简要描述：按类型获取快捷键功能列表。该接口为客户端封装功能。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| type | string | 快捷键类型，只支持 `system`、`mouse`、`media`、`firmware`、`hotkey` | 是 |

### 返回值

• 总体类型：`Promise<{ type: string, total: number, list: Array<{ index: number, type: string, name: string, code: string, task: string, id: number, commandType: number, image: string }> }>`
• 描述：返回指定类型的快捷键功能列表，其中 `code` 为原始协议值，`task` 为热键组合（仅 `hotkey` 类型有值，例如 `Ctrl+C`），`id` 与 `commandType` 为拆解后的数值字段，`image` 为对应图标资源地址；若当前项没有可用图标，则返回空字符串。

### 使用示例

```javascript
const shortcuts = await ServiceKeyboard.getShortcutFunctionList('system');
console.log('system 快捷键功能列表:', shortcuts);
```

## 获取自定义快捷键 CODE

ServiceKeyboard.getHotKeyCodeCustom(hotKey)

简要描述：通过自定义快捷键组合获取协议 CODE。该接口为客户端封装功能，支持单个修饰键和多个修饰键组合。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| hotKey | string | 快捷键组合，例如 `Ctrl+C`、`Ctrl+Shift+S`、`Ctrl+Alt+Delete`。修饰键支持 `Ctrl`、`Shift`、`Alt`、`Win` | 是 |

### 返回值

• 总体类型：`Promise<{ code: number, hotKey: string, keyName: string, modifiers: string[], shortcutCode: string, id: number, commandType: number, key: string, modifier: string, config: { id: string, type: string } }>`
• 描述：`shortcutCode` 是可展示的协议 CODE，例如 `0x06,0x61`；`id` 是普通按键 HID 数值；`commandType` 是热键修饰符协议值；`config` 可直接作为 `setKeyConfig` 的第三个参数使用。

### 使用示例

```javascript
const hotKeyCode = await ServiceKeyboard.getHotKeyCodeCustom('Ctrl+Alt+Delete');
console.log('自定义快捷键 CODE:', hotKeyCode.shortcutCode);
await ServiceKeyboard.setKeyConfig('A', '0x00', hotKeyCode.config);
```

## 设置单个按键功能

ServiceKeyboard.setKeyConfig(keyName, layer, config)

简要描述：设置指定按键在指定层级下的功能配置。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| keyName | string | 按键名称，例如 `A`、`B`、`Esc` 或 `Space`。SDK 会根据按键名称自动解析对应 HID | 是 |
| layer | number | 层级值。基本层：`0x00`，FN1 层：`0x01`，FN2 层：`0x02`，FN3 层：`0x03` | 是 |
| config | object | 按键配置对象。`config.id` 为具体功能值，`config.type` 为功能值的修饰符；具体功能描述值请参考协议表 | 是 |

### 返回值

• 总体类型：`Promise<any>`
• 描述：返回设置后的按键配置结果。

### 使用示例

```javascript
const result = await ServiceKeyboard.setKeyConfig('A', 0x00, {
	id: 0x05,
	type: 0x00
});
console.log('设置按键结果:', result);
```