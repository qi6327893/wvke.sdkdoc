# 高级按键功能

## 概述

本页记录 SDK 调试器“机械轴键盘 → 高级按键功能”分类中的 6 个接口：获取和删除高级键，以及 MT、TGL、END、SOCD 配置。

机械轴服务不提供 DKS、MPT 和 RT 专用高级接口。

## 获取高级按键布局详情

`ServiceKeyboardMK.getAdvancedKeyLayout()`

简要描述：读取当前键盘高级按键布局和详细配置列表，用于展示已配置的高级功能。

### 参数

此方法不需要参数。

### 返回值

返回 `{ code, total, details, layout }`。`details` 是已配置高级键列表，`layout` 是包含完整键盘矩阵的布局数据；自定义功能通常还会带有 `custom` 和 `data` 字段。

```javascript
const layout = await keyboard.getAdvancedKeyLayout();
console.log(layout.details);
```

## 删除高级键

`ServiceKeyboardMK.delAdvancedKey(params)`

简要描述：删除指定按键上的高级功能。删除前可使用 `getAdvancedKeyLayout()` 确认当前配置。

### 参数

| 参数 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| params | object | 删除目标配置，通常包含 `keyName`，也可携带模式或任务信息。 | 是 |

```javascript
await keyboard.delAdvancedKey({
  keyName: 'A',
});
```

## 设置 MT 高级键

`ServiceKeyboardMK.setAdvancedKeyMT(params)`

简要描述：将一个按键配置为 MT（按键/修饰键）功能，在不同触发场景执行对应任务。

### 参数

| 参数 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| params | object | MT 配置对象。 | 是 |
| params.keyName | string | 被配置的按键名称。 | 是 |
| params.task | string | 任务格式通常为 `任务键1|任务键2|延迟毫秒`。 | 是 |

```javascript
await keyboard.setAdvancedKeyMT({
  keyName: 'A',
  task: 'B|C|200',
});
```

## 设置 TGL 高级键

`ServiceKeyboardMK.setAdvancedKeyTGL(params)`

简要描述：将一个按键配置为 TGL（切换锁定）功能，触发后切换目标任务状态。

### 参数

| 参数 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| params | object | TGL 配置对象。 | 是 |
| params.keyName | string | 被配置的按键名称。 | 是 |
| params.task | string | 目标任务按键名称。 | 是 |
| params.delay | number | 延迟时间，单位为毫秒。 | 否 |

```javascript
await keyboard.setAdvancedKeyTGL({
  keyName: 'A',
  task: 'B',
  delay: 200,
});
```

## 设置 END 高级键

`ServiceKeyboardMK.setAdvancedKeyEND(params)`

简要描述：将一个按键配置为 END 功能，在按键动作结束阶段执行目标任务。

### 参数

| 参数 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| params | object | END 配置对象。 | 是 |
| params.keyName | string | 被配置的按键名称。 | 是 |
| params.task | string | END 目标任务按键名称。 | 是 |
| params.delay | number | 可选延迟时间，单位为毫秒。 | 否 |

```javascript
await keyboard.setAdvancedKeyEND({
  keyName: 'A',
  task: 'B',
  delay: 0,
});
```

## 设置 SOCD 高级键

`ServiceKeyboardMK.setAdvancedKeySOCD(params)`

简要描述：为两个不同按键配置 SOCD（同时相反方向输入处理）规则。两个按键必须不同，并且设备需要支持对应固件功能。

### 参数

| 参数 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| keyName | string | 第一个按键名称。 | 是 |
| keyName2 | string | 第二个按键名称。 | 是 |
| mode | number \| string | SOCD 模式值或模式名称。 | 是 |
| delay | number | 延迟值，单位由协议定义。 | 是 |

支持的常用模式包括：后输入优先、绝对优先第一个键、绝对优先第二个键、抵消和深度优先；具体枚举以 SDK 类型声明为准。

### 使用示例

```javascript
await keyboard.setAdvancedKeySOCD({
  keyName: 'A',
  keyName2: 'D',
  mode: 0,
  delay: 0,
});
```
