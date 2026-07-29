# 按键功能

## 概述

本页记录 SDK 调试器“机械轴键盘 → 按键功能”分类中的 7 个接口，涵盖层布局、快捷功能查询、单键配置和恢复操作。

机械轴服务不提供 RT 按键编组、轴体行程或轴体相关接口。

## 获取自定义层按键功能

`ServiceKeyboardMK.getKeyCustomLayer(layer, options?)`

简要描述：读取指定层的自定义按键功能映射和布局。返回项中的 `image` 可用于渲染功能图标。

### 参数

| 参数 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| layer | number | 键盘层，基本层通常为 `0x00`，FN 层按设备定义。 | 是 |
| options | object | 图片选项。`includeImages: false` 时不返回图片地址；`verifyImages: true` 时验证图片可访问性。 | 否 |

### 返回值

返回 `{ code, layer, list, layout }`，其中 `list` 为按键功能列表，`layout` 为布局矩阵。

### 使用示例

```javascript
const result = await keyboard.getKeyCustomLayer(0x00, {
  includeImages: true,
});
console.log(result.list);
```

## 获取标准 104 键布局

`ServiceKeyboardMK.getStandard104KeyLayout()`

简要描述：获取标准 104 键的行列布局，用于构建键盘可视化面板和按键选择器。

### 参数

此方法不需要参数。

### 返回值

返回标准布局矩阵，条目通常包含按键名称、HID、行列位置等字段。

```javascript
const layout = await keyboard.getStandard104KeyLayout();
```

## 获取快捷功能列表

`ServiceKeyboardMK.getShortcutFunctionList(type, options?)`

简要描述：获取可写入按键配置的系统、鼠标、媒体、固件、热键或手柄功能列表。

### 参数

| 参数 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| type | string | `system`、`mouse`、`media`、`firmware`、`hotkey` 或 `gamepad`。 | 是 |
| options | object | 可选查询配置。 | 否 |

### 返回值

返回功能项数组，每项包含显示名称、协议 ID、功能类型和可选图标信息。

```javascript
const functions = await keyboard.getShortcutFunctionList('system');
```

## 获取自定义快捷键 CODE

`ServiceKeyboardMK.getHotKeyCodeCustom(hotKey)`

简要描述：将快捷键文本转换为可直接用于按键配置的协议 CODE。

### 参数

| 参数 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| hotKey | string | 快捷键组合，例如 `Ctrl+C`、`Ctrl+Shift+S`。 | 是 |

### 返回值

返回快捷键文本、修饰键、协议 CODE 以及可用于 `setKeyConfig` 的 `config` 对象。

```javascript
const code = await keyboard.getHotKeyCodeCustom('Ctrl+Alt+Delete');
await keyboard.setKeyConfig('A', 0x00, code.config);
```

## 设置单个按键功能

`ServiceKeyboardMK.setKeyConfig(keyName, layer, config)`

简要描述：设置指定层的单个按键功能。`config` 应来自功能列表或 `getHotKeyCodeCustom()` 的结果。

### 参数

| 参数 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| keyName | string | 按键名称，例如 `A`。 | 是 |
| layer | number | 目标键盘层。 | 是 |
| config | object | 按键功能协议配置，通常包含 `id` 和 `type`。 | 是 |

### 使用示例

```javascript
await keyboard.setKeyConfig('A', 0x00, {
  id: 0x05,
  type: 0x00,
});
```

## 恢复指定按键功能

`ServiceKeyboardMK.recoverKeyConfig(keyName, layer)`

简要描述：将指定层的指定按键恢复为出厂功能。

### 参数

| 参数 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| keyName | string | 要恢复的按键名称。 | 是 |
| layer | number | 目标键盘层。 | 是 |

```javascript
await keyboard.recoverKeyConfig('A', 0x00);
```

## 恢复全部按键功能

`ServiceKeyboardMK.recoverAllKeyConfig(layer?)`

简要描述：恢复指定层的全部按键功能；省略层参数时使用 SDK 默认层行为。

### 参数

| 参数 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| layer | number | 要恢复的键盘层。 | 否 |

```javascript
await keyboard.recoverAllKeyConfig(0x00);
```
