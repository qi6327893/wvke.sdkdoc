# 自定义灯光与实时回传

## 概述

这组接口对应当前 SDK 里新增的实时灯光读取能力。和基础灯效接口不同，它们既支持单次读取，也支持通过事件持续接收设备回传，用于做键盘涂鸦、Logo 灯预览、在线调试等场景。

本分类当前包含以下能力：

- `ServiceKeyboard.getKeyboardBacklightCustom()`
- `ServiceKeyboard.setKeyboardBacklightCustom(params)`
- `ServiceKeyboard.getLightingLogoCustom()`
- `ServiceKeyboard.setLightingLogoCustom(params)`
- `ServiceKeyboard.on('keyboardBacklightCustomReport', callback)`
- `ServiceKeyboard.on('lightingLogoCustomReport', callback)`
- 对应的 `off(...)` 解绑接口

## 键盘自定义背光读取

ServiceKeyboard.getKeyboardBacklightCustom()

简要描述：触发设备回传当前每键 RGB 数据。调用后设备不会只返回单包，而是会在一段时间内持续上报灯光数据。

### 返回值

• 总体类型：`Promise<{ code: number, data: Array<[string, string, string, string]>, readKeys: number, packetCount: number }>`

• 字段说明：

- `data`: 每项格式固定为 `[keyHid, r, g, b]`
- `readKeys`: 本次成功读取的按键数量
- `packetCount`: 接收到的 HID 数据包数量

### 持续监听

对应事件名：`keyboardBacklightCustomReport`

```javascript
const handleLightingReport = (payload) => {
	console.log('实时灯光上报:', payload.data);
};

ServiceKeyboard.on('keyboardBacklightCustomReport', handleLightingReport);
await ServiceKeyboard.getKeyboardBacklightCustom();

const heartbeatTimer = setInterval(() => {
	ServiceKeyboard.getKeyboardBacklightCustom().catch(console.error);
}, 5000);

// 清理
ServiceKeyboard.off('keyboardBacklightCustomReport', handleLightingReport);
clearInterval(heartbeatTimer);
```

## 键盘自定义背光写入

ServiceKeyboard.setKeyboardBacklightCustom(params)

简要描述：写入自定义每键灯光矩阵。调用时会自动把键盘背光切换到涂鸦模式。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| data | array | 每项格式为 `[keyHid, r, g, b]` 的自定义灯光数据 | 是 |

### 使用示例

```javascript
await ServiceKeyboard.setKeyboardBacklightCustom({
	data: [['04', 'ff', '00', '00'], ['05', '00', 'ff', '00']]
});
```

## Logo 自定义灯光读取

ServiceKeyboard.getLightingLogoCustom()

简要描述：触发设备回传当前 Logo RGB 数据。没有 Logo 灯的设备可以忽略这组接口。

### 返回值

• 总体类型：`Promise<{ code: number, data: [string, string, string], packetCount: number }>`

• 字段说明：

- `data`: 固定格式 `[r, g, b]`
- `packetCount`: 接收到的 HID 数据包数量

### 持续监听

对应事件名：`lightingLogoCustomReport`

```javascript
const handleLogoReport = (payload) => {
	console.log('Logo 实时灯光上报:', payload.data);
};

ServiceKeyboard.on('lightingLogoCustomReport', handleLogoReport);
await ServiceKeyboard.getLightingLogoCustom();

const heartbeatTimer = setInterval(() => {
	ServiceKeyboard.getLightingLogoCustom().catch(console.error);
}, 5000);

// 清理
ServiceKeyboard.off('lightingLogoCustomReport', handleLogoReport);
clearInterval(heartbeatTimer);
```

## Logo 自定义灯光写入

ServiceKeyboard.setLightingLogoCustom(params)

简要描述：写入 Logo RGB 数据。写入前会自动切换到 Logo 自定义模式。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| data | array | 固定格式 `[r, g, b]` 的 Logo 颜色数据 | 是 |

### 使用示例

```javascript
await ServiceKeyboard.setLightingLogoCustom({
	data: ['80', '29', '31']
});
```

## 使用建议

TIP

- 只读一次状态时，直接等待 Promise 结果即可。
- 需要持续渲染 UI 时，必须同时监听事件，并做 `5000ms` 心跳。
- 页面销毁时必须调用 `off(...)`，否则容易留下重复监听。
- 基础灯效模式、Logo 模式枚举和亮度速度档位，仍然以 [万花筒灯效](./lighting.md) 页面为准。