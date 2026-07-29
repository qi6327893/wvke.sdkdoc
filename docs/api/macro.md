# 宏

## 概述

键盘宏功能通过按键自定义层 API 进行管理。宏配置作为按键功能类型之一（`type: "MACRO"`），可绑定到任意层级（基本层、FN1、FN2、FN3）的任意按键上。

读取宏配置请使用 `ServiceKeyboard.getKeyCustomLayer(layer)`，写入宏配置请使用 `ServiceKeyboard.setKeyConfig(keyName, layer, config)`，详见 [按键功能](./key.md)。

## 读取宏配置

使用 `getKeyCustomLayer(layer)` 读取指定层级的按键配置，返回的 `list` 中 `type` 为 `"MACRO"` 的项即为宏配置。

```javascript
// 读取 FN1 层的所有按键配置
const result = await ServiceKeyboard.getKeyCustomLayer(0x01);

// 筛选宏配置
const macroKeys = result.list.filter(item => item.type === 'MACRO');
console.log('FN1 层宏按键:', macroKeys);
```

## 设置宏配置

使用 `setKeyConfig(keyName, layer, config)` 将指定按键设置为宏类型。

```javascript
// 将 FN1 层的 A 键设置为宏（id 为具体宏功能值，type 为 MACRO 对应的协议值）
await ServiceKeyboard.setKeyConfig('A', 0x01, {
  id: '0xA1',    // 宏功能 ID（具体值请参考协议文档）
  type: '0x06'   // MACRO 类型值
});
```

### 参数说明

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| keyName | string | 按键名称，例如 `A`、`B`、`Esc`。 | 是 |
| layer | number | 层级值。基本层：`0x00`，FN1：`0x01`，FN2：`0x02`，FN3：`0x03`。 | 是 |
| config.id | string | 宏功能的具体 ID 值（十六进制字符串）。 | 是 |
| config.type | string | 功能类型，宏固定为 MACRO 类型对应的协议值（十六进制字符串）。 | 是 |

## 协议相关

宏功能的底层协议命令为 `SET_DEVICE_FUNCTION_MACRO_CMD`（`5C 00 20 FF 01 ...`），SDK 内部通过 `setKeyConfig` 封装了该协议。

宏类型子命令 (`MACRO_TYPE_CMD`) 包括：

| 子类型 | 描述 |
|------|------|
| DELAY | 延时 |
| KEY_DOWN | 按键按下 |
| KEY_UP | 按键抬起 |
| MOUSE_X | 鼠标 X 轴移动 |
| MOUSE_Y | 鼠标 Y 轴移动 |