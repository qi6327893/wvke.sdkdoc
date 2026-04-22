# 万花筒灯效

## 概述

本章节介绍当前 SDK 中的灯光相关 API。`getLightingBase()` / `setLightingBase()` 主要针对键盘背光，`getLightingLogoBase()` / `setLightingLogoBase()` 主要针对 Logo 灯区。没有 Logo 灯的键盘，可以忽略全部 Logo 灯相关接口。

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

## 获取键盘背光基础配置

ServiceKeyboard.getLightingBase()

简要描述：获取当前键盘背光基础配置，其中亮度和速度都使用 5 档位。

### 参数

此方法不需要参数。

### 返回说明

当前接口的获取与设置使用同一条命令，核心字段为：`mode`、`childMode`、`luminance`、`speed`。

返回对象字段说明：

• `mode`: 主模式值
• `modeLabel`: 主模式名称
• `childMode`: 子模式序号
• `childModeLabel`: 子模式名称
• `luminance`: 亮度档位
• `speed`: 速度档位

当前 `mode` 使用协议枚举值，支持 `1` 到 `23`，以及 `28`。
常用示例：`1=静态模式`、`2=呼吸模式`、`3=光谱循环`、`23=旋转轮盘`、`28=涂鸦模式`。

完整模式对照如下：

| mode | 名称 |
|------|------|
| 1 | 静态模式 |
| 2 | 呼吸模式 |
| 3 | 光谱循环 |
| 4 | 正弦光波 |
| 5 | 指尖涟漪1 |
| 6 | 指尖涟漪2 |
| 7 | 一触即发1 |
| 8 | 一触即发2 |
| 9 | 踏雪无痕 |
| 10 | 暗流涌动 |
| 11 | 浮光掠影 |
| 12 | 流光溢彩 |
| 13 | 繁星点点 |
| 14 | 变换呼吸 |
| 15 | 斜风细雨 |
| 16 | 川流不息 |
| 17 | 花开富贵 |
| 18 | 跑马游龙 |
| 19 | 漫天飞雪 |
| 20 | 百花齐放 |
| 21 | 峰回路转 |
| 22 | 贪吃小蛇 |
| 23 | 旋转轮盘 |
| 28 | 涂鸦模式 |

完整子模式对照如下：

• `mode=1 静态模式`: `1=炫彩模式一`、`2=炫彩模式二`、`3=炫彩模式三`、`4=炫彩模式四`、`5=炫彩模式五`、`6=炫彩模式六`、`7=炫彩模式七`、`8=炫彩模式八`、`9=炫彩模式九`、`10=单色模式`
• `mode=2 呼吸模式`: `1=炫彩模式一`、`2=炫彩模式二`、`3=炫彩模式三`、`4=炫彩模式四`、`5=炫彩模式五`、`6=炫彩模式六`、`7=炫彩模式七`、`8=炫彩模式八`、`9=炫彩模式九`、`10=单色模式`
• `mode=3 光谱循环`: `1=模式一`、`2=模式二`
• `mode=4 正弦光波`: `1=从左至右`、`2=由内而外`、`3=从右至左`
• `mode=5 指尖涟漪1`: `1=炫彩背景光一`、`2=炫彩背景光二`、`3=炫彩背景光三`、`4=炫彩背景光四`、`5=红色背景光`、`6=橙色背景光`、`7=黄色背景光`、`8=绿色背景光`、`9=青色背景光`、`10=蓝色背景光`、`11=紫色背景光`、`12=粉色背景光`、`13=白色背景光`
• `mode=6 指尖涟漪2`: `1=背景光关闭`、`2=背景光打开`
• `mode=7 一触即发1`: `1=炫彩背景光一`、`2=炫彩背景光二`、`3=炫彩背景光三`、`4=炫彩背景光四`、`5=红色背景光`、`6=橙色背景光`、`7=黄色背景光`、`8=绿色背景光`、`9=青色背景光`、`10=蓝色背景光`、`11=紫色背景光`、`12=粉色背景光`、`13=白色背景光`
• `mode=8 一触即发2`: `1=背景光关闭`、`2=背景光打开`
• `mode=9 踏雪无痕`: `1=持续模式一`、`2=持续模式二`、`3=触发模式三`、`4=触发模式四`
• `mode=10 暗流涌动`: `1=扩散模式一`、`2=汇聚模式一`、`3=扩散模式二`、`4=汇聚模式二`
• `mode=11 浮光掠影`: `1=自上而下一`、`2=自下而下一`、`3=自上而下二`、`4=自下而下二`
• `mode=12 流光溢彩`: `1=从左至右一`、`2=从右至左二`、`3=从左至右一`、`4=从右至左二`
• `mode=13 繁星点点`: `1=背景光关闭`、`2=背景光打开`
• `mode=14 变换呼吸`: `1=模式一`、`2=模式二`
• `mode=15 斜风细雨`: `1=背景光关闭`、`2=背景光打开`
• `mode=16 川流不息`: `1=背景光关闭`、`2=背景光打开`
• `mode=17 花开富贵`: `1=背景光关闭`、`2=背景光打开`
• `mode=18 跑马游龙`: `1=背景光关闭`、`2=背景光打开`
• `mode=19 漫天飞雪`: `1=背景光关闭`、`2=背景光打开`
• `mode=20 百花齐放`: `1=模式一`、`2=模式二`
• `mode=21 峰回路转`: `1=顺时针模式一`、`2=逆时针模式一`、`3=顺时针模式二`、`4=逆时针模式二`
• `mode=22 贪吃小蛇`: `1=背景光关闭`、`2=背景光打开`
• `mode=23 旋转轮盘`: `1=顺时针模式一`、`2=逆时针模式一`、`3=顺时针模式二`、`4=逆时针模式二`
• `mode=28 涂鸦模式`: `1=模式一`、`2=模式二`

其中 `luminance` 和 `speed` 都是档位值，范围为 `1` 到 `5`。
一般可按以下理解使用：

• `1` 表示最低档
• `5` 表示最高档

### 使用示例

```javascript
const lightingBase = await ServiceKeyboard.getLightingBase();
console.log('键盘背光基础配置:', lightingBase);
// 示例输出：{ code: 0, mode: 2, modeLabel: '呼吸模式', childMode: 1, childModeLabel: '炫彩模式一', luminance: 3, speed: 4 }
```

## 设置键盘背光基础配置

ServiceKeyboard.setLightingBase(params)

简要描述：设置键盘背光基础配置，其中亮度和速度都使用 5 档位。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| data | object | 键盘背光基础配置对象，包含 `mode`、`childMode`、`luminance`、`speed` 四个字段 | 是 |

`data` 字段说明：

• `mode`: 主模式值，支持 `1` 到 `23`，以及 `28`
• `childMode`: 当前主模式下的子模式序号，从 `1` 开始；单色灯光可忽略此选项
• `luminance`: 亮度档位，范围 `1` 到 `5`
• `speed`: 速度档位，范围 `1` 到 `5`

### 使用示例

```javascript
const result = await ServiceKeyboard.setLightingBase({
	data: {
		mode: 2,
		childMode: 1,
		luminance: 3,
		speed: 4
	}
});
console.log('设置键盘背光基础配置:', result);
```

### 注意事项

TIP

• `luminance` 仅支持 `1` 到 `5` 的档位值。
• `speed` 仅支持 `1` 到 `5` 的档位值。
• `mode` 和 `childMode` 都建议使用协议枚举值；返回结果会补充对应的 `modeLabel` 和 `childModeLabel` 便于界面展示。
• 如果当前效果为单色灯光，`childMode` 可保持默认值，不影响设置结果。

## 获取自定义键盘背光配置

ServiceKeyboard.getKeyboardBacklightCustom()

简要描述：获取自定义键盘背光配置。读取时会发送背光基础命令，并把协议操作位切换为 `0x02` 以触发设备回传当前每键 RGB 数据。发送后，键盘会自动实时上报灯光数据，前端可以直接用于 UI 渲染。

### 参数

此方法不需要参数。

### 返回说明

返回对象字段说明：

• `data`: 自定义灯光数据数组。每项格式固定为 `[keyHid, r, g, b]`
• `readKeys`: 本次成功读取到的按键数量
• `packetCount`: 本次读取过程中接收到的 HID 数据包数量

### 重点说明

• 调用 `getKeyboardBacklightCustom()` 之后，设备会自动持续上报一段时间的灯光数据，不是只返回单包。
• 如果前端只是做一次性读取，直接等待当前 Promise 返回即可。
• 如果前端需要持续渲染 UI，必须额外做异步监听，监听事件名为 `keyboardBacklightCustomReport`。
• 前端必须做心跳处理，建议每 `5000ms` 再调用一次 `getKeyboardBacklightCustom()`，用于重新触发设备继续实时上报。
• 页面销毁或不再需要渲染时，应移除事件监听并清理心跳定时器。

### 使用示例

```javascript
const customLighting = await ServiceKeyboard.getKeyboardBacklightCustom();
console.log('自定义键盘背光配置:', customLighting);
// 示例输出：{ code: 0, data: [['04', 'ff', '00', '00'], ['05', '00', 'ff', '00']], readKeys: 2, packetCount: 1 }
```

### 持续渲染示例

```javascript
const handleLightingReport = (payload) => {
	console.log('实时灯光上报:', payload.data);
	// 在这里更新 UI
};

ServiceKeyboard.on('keyboardBacklightCustomReport', handleLightingReport);

await ServiceKeyboard.getKeyboardBacklightCustom();

const heartbeatTimer = setInterval(() => {
	ServiceKeyboard.getKeyboardBacklightCustom().catch((error) => {
		console.error('自定义背光心跳失败:', error);
	});
}, 5000);

// 销毁时清理
ServiceKeyboard.off('keyboardBacklightCustomReport', handleLightingReport);
clearInterval(heartbeatTimer);
```

### 调试工具实操

• 先在调试工具中调用 `init` 完成设备初始化。
• 在左侧选择 `getKeyboardBacklightCustom`。
• 点击一次 `调用` 可做单次读取，此时会返回当前一轮读取结果。
• 点击 `开始监听` 后，调试工具会自动监听 `keyboardBacklightCustomReport`，并每 `5000ms` 自动发送一次心跳请求。
• 页面中的“键位颜色网格”会按默认键盘布局渲染每个按键当前颜色，同时下方保留原始 JSON 上报结果，方便协议调试。
• 不再需要实时渲染时，点击 `停止监听`，页面会自动移除监听并清理心跳定时器。

## 设置自定义键盘背光配置

ServiceKeyboard.setKeyboardBacklightCustom(params)

简要描述：设置自定义键盘背光矩阵配置。调用时会自动先把键盘背光切换到涂鸦模式，背光涂鸦模式索引固定为 `28`。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| data | array | 自定义灯光数据数组。每项格式为 [keyHid, r, g, b] | 是 |

### 使用示例

```javascript
await ServiceKeyboard.setKeyboardBacklightCustom({
	data: [["04", "ff", "00", "00"], ["05", "00", "ff", "00"]]
});
```

## 获取 Logo 灯光基础配置

ServiceKeyboard.getLightingLogoBase()

简要描述：获取当前 Logo 灯光基础配置。返回字段为 `mode`、`luminance`、`speed`；Logo 灯光不支持 `childMode`。没有 Logo 灯的键盘，可以忽略此接口。

### 参数

此方法不需要参数。

### 返回说明

返回对象字段说明：

• `mode`: 主模式值
• `modeLabel`: 主模式名称
• `luminance`: 亮度档位
• `speed`: 速度档位

当前 Logo 灯光也使用相同的模式枚举与档位范围：

• `mode` 使用 Logo 专用模式枚举
• `luminance` 支持 `1` 到 `5`
• `speed` 支持 `1` 到 `5`

`getLightingLogoBase` 与 `setLightingLogoBase` 使用同一套 Logo 模式枚举，支持 `L0` 到 `L9`，其中 `L0` 为涂鸦模式。

Logo 模式对照如下：

| mode | 协议标识 | 名称 |
|------|----------|------|
| 0 | L0 | 涂鸦模式 |
| 1 | L1 | 彩虹流动 |
| 2 | L2 | 频谱变化 |
| 3 | L3 | 星光点点 |
| 4 | L4 | 七彩扫描 |
| 5 | L5 | 静态彩虹 |
| 6 | L6 | 彩虹呼吸 |
| 7 | L7 | 七彩变换 |
| 8 | L8 | 彩虹巡视 |
| 9 | L9 | 双虹争艳 |

### 使用示例

```javascript
const logoBase = await ServiceKeyboard.getLightingLogoBase();
console.log('Logo 灯光基础配置:', logoBase);
```

## 设置 Logo 灯光基础配置

ServiceKeyboard.setLightingLogoBase(params)

简要描述：设置当前 Logo 灯光基础配置。Logo 灯光不支持 `childMode`。没有 Logo 灯的键盘，可以忽略此接口。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| data | object | Logo 灯光基础配置对象，包含 `mode`、`luminance`、`speed` 三个字段 | 是 |

`data` 字段说明：

• `mode`: 主模式值，支持 `L0` 到 `L9`，其中 `L0` 为涂鸦模式
• `luminance`: 亮度档位，范围 `1` 到 `5`
• `speed`: 速度档位，范围 `1` 到 `5`

### 使用示例

```javascript
const logoResult = await ServiceKeyboard.setLightingLogoBase({
	data: {
		mode: 1,
		luminance: 3,
		speed: 4
	}
});
console.log('设置 Logo 灯光基础配置:', logoResult);
```

## 获取 Logo 自定义灯光配置

ServiceKeyboard.getLightingLogoCustom()

简要描述：获取 Logo 自定义灯光配置。读取时会发送 Logo 自定义命令，并把协议操作位切换为 `0x02` 以触发设备回传当前 Logo RGB 数据。发送后，设备会自动实时上报 Logo 灯光数据，前端可以直接用于 UI 渲染。没有 Logo 灯的键盘，可以忽略此接口。

### 参数

此方法不需要参数。

### 返回说明

返回对象字段说明：

• `data`: Logo RGB 字节数组，格式固定为 `[r, g, b]`
• `packetCount`: 本次读取过程中接收到的 HID 数据包数量

### 重点说明

• 调用 `getLightingLogoCustom()` 之后，设备会自动持续上报一段时间的 Logo 灯光数据，不是只返回单包。
• 如果前端只是做一次性读取，直接等待当前 Promise 返回即可。
• 如果前端需要持续渲染 UI，必须额外做异步监听，监听事件名为 `lightingLogoCustomReport`。
• 前端必须做心跳处理，建议每 `5000ms` 再调用一次 `getLightingLogoCustom()`，用于重新触发设备继续实时上报。
• 页面销毁或不再需要渲染时，应移除事件监听并清理心跳定时器。

### 使用示例

```javascript
const logoLighting = await ServiceKeyboard.getLightingLogoCustom();
console.log('Logo 自定义灯光配置:', logoLighting);
// 示例输出：{ code: 0, data: ['80', '29', '31'], packetCount: 1 }
```

### 持续渲染示例

```javascript
const handleLogoLightingReport = (payload) => {
	console.log('Logo 实时灯光上报:', payload.data);
	// 在这里更新 UI
};

ServiceKeyboard.on('lightingLogoCustomReport', handleLogoLightingReport);

await ServiceKeyboard.getLightingLogoCustom();

const heartbeatTimer = setInterval(() => {
	ServiceKeyboard.getLightingLogoCustom().catch((error) => {
		console.error('Logo 自定义灯光心跳失败:', error);
	});
}, 5000);

// 销毁时清理
ServiceKeyboard.off('lightingLogoCustomReport', handleLogoLightingReport);
clearInterval(heartbeatTimer);
```

### 调试工具实操

• 先在调试工具中调用 `init` 完成设备初始化。
• 在左侧选择 `getLightingLogoCustom`。
• 点击一次 `调用` 可做单次读取，此时会返回当前一轮 Logo 读取结果。
• 点击 `开始监听` 后，调试工具会自动监听 `lightingLogoCustomReport`，并每 `5000ms` 自动发送一次心跳请求。
• 页面中的“Logo 颜色预览”会展示最近一次收到的颜色，同时下方保留原始 JSON 上报结果，方便协议调试。
• 不再需要实时渲染时，点击 `停止监听`，页面会自动移除监听并清理心跳定时器。

## 设置 Logo 自定义灯光配置

ServiceKeyboard.setLightingLogoCustom(params)

简要描述：设置 Logo 自定义灯光配置。写入前会自动先把 Logo 灯切换到自定义涂鸦模式；该模式在此接口中的协议 ID 固定为 `0`。没有 Logo 灯的键盘，可以忽略此接口。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| data | array | Logo RGB 数据，格式固定为 [r, g, b] | 是 |

### 返回说明

返回对象字段说明：

• `data`: 当前写入的 Logo RGB 字节数组，格式固定为 `[r, g, b]`
• `packetCount`: 本次写入发送的 HID 数据包数量，当前固定为 `1`

### 使用示例

```javascript
await ServiceKeyboard.setLightingLogoCustom({
	data: ["80", "29", "31"]
});
```

