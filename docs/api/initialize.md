# 初始化设备

## 概览

调试工具左侧的“准备开始 / 初始化设备”分类包含设备连接前使用的三个接口：

- `keyboard.requestDevices()`
- `keyboard.getDevices()`
- `keyboard.init(id)`

后续所有需要访问设备的键盘接口，都应在 `init` 成功后再调用。

## 调试工具普通监听行为

调试工具中部分普通接口带有 `开始监听` / `停止监听` 能力，例如自定义灯光回报、Logo 灯回报、ADC 实时采样和校准状态预览。通过这些接口开启的普通监听只属于当前接口面板；当点击左侧其它接口或切换到“准备开始”页面时，调试工具会先自动停止当前接口相关的监听、轮询或心跳，再打开新的接口页面。

主动上报全局监听 `startGlobalReportListener()` 属于独立的全局监听能力，不按普通接口面板自动停止；不再需要全局主动上报时，请显式调用 `stopGlobalReportListener()`。

## 请求浏览器授权设备

keyboard.requestDevices(configs)

简要描述：调起浏览器 WebHID 授权选择器，并返回用户授权的合法键盘设备列表。可通过 `configs` 临时传入允许连接的合法设备 VID/PID 数组；未传入时，SDK 会使用 `ServiceKeyboard` 实例中的默认 `configs` 生成 WebHID filters。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| configs | Array<{ vendorId: number; productId: number; usagePage?: number; usage?: number }> | 允许连接的合法设备过滤数组。调试 SDK 中默认展示当前内置合法设备 VID/PID 列表（来源于 `src/lib/config/hid-filters.js` 统一入口），可直接编辑后传入 | 否 |

### 返回值

总体类型：`Promise<{ code: number; devices: Device[] }>`

返回的 `devices` 与 `keyboard.getDevices()` 一致，已按设备唯一标识去重。

### 使用示例

```javascript
const { devices } = await keyboard.requestDevices([
	{ vendorId: 0x1CAA, productId: 0x0806, usagePage: 0xFFA0, usage: 0x01 }
]);
console.log('已授权设备:', devices);
```

## 获取已授权设备列表

keyboard.getDevices()

简要描述：获取当前浏览器已经授权过的键盘设备列表。此方法不会调起浏览器授权选择器；如需让用户授权新设备，请先调用 `keyboard.requestDevices()`。

### 参数

此方法不需要参数。

### 返回值

总体类型：`Promise<{ code: number; devices: Device[] }>`

`devices` 数组中的 `Device` 结构：

| 字段 | 类型 | 描述 | 示例 |
|------|------|------|------|
| data | object | 设备数据对象 | - |
| id | string | 设备唯一标识符 | "7338:2054" |
| usage | number | 设备用途值 | 1 |
| usagePage | number | 设备用途页值 | 65440 |
| vendorId | number | 厂商 ID | 7338 |
| productId | number | 产品 ID | 2054 |
| productName | string | 产品名称 | "GT60 PRO" |

### 使用示例

```javascript
import { ServiceKeyboard } from 'wvke-sdk';

const keyboard = new ServiceKeyboard({
	configs: [
		{ vendorId: 0x1caa, productId: 0x0806, usagePage: 0xffa0, usage: 0x01 }
	],
	usage: 0x01,
	usagePage: 0xffa0,
});

const { code, devices } = await keyboard.getDevices();
if (code !== 0 || devices.length === 0) {
	throw new Error('未找到已授权键盘设备，请先调用 requestDevices');
}

console.log('发现的设备:', devices);
```

## 初始化指定的键盘设备

keyboard.init(id)

简要描述：初始化指定键盘设备，并打开 WebHID 连接，供后续接口复用。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| id | string | 要初始化的设备唯一标识符。可从 `keyboard.requestDevices()` 或 `keyboard.getDevices()` 返回的 `devices` 数组中获取 | 是 |

### 返回值

总体类型：`Promise<{ code: number; info: string }>`

| 字段 | 类型 | 描述 |
|------|------|------|
| code | number | `0` 表示初始化成功 |
| info | string | 初始化结果说明 |

### 使用示例

```javascript
const { devices } = await keyboard.requestDevices();

if (!devices.length) {
	throw new Error('未找到可初始化的设备');
}

const result = await keyboard.init(devices[0].id);
console.log('设备初始化结果:', result);
```

## 调试工具位置

在 SDK 调试页面中，这些接口位于：

`准备开始` → `初始化设备`

完成 `requestDevices` / `getDevices` 和 `init` 后，再进入“线性轴键盘”“固件升级”等分类调用其它接口。
