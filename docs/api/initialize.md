# 初始化设备

## 概览

调试工具左侧的“准备开始 / 初始化设备”分类包含设备连接前必须先调用的两个接口：

- `keyboard.getDevices()`
- `keyboard.init(id)`

后续所有需要访问设备的键盘接口，都应在 `init` 成功后再调用。

## 获取设备列表

keyboard.getDevices()

简要描述：获取当前浏览器已授权或可选择的键盘设备列表。SDK 会按 `ServiceKeyboard` 实例中的 `configs` 生成 WebHID filters，字段包含 `vendorId`、`productId`、`usagePage`、`usage`。

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
	throw new Error('未找到可用键盘设备');
}

console.log('发现的设备:', devices);
```

## 初始化指定的键盘设备

keyboard.init(id)

简要描述：初始化指定键盘设备，并打开 WebHID 连接，供后续接口复用。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| id | string | 要初始化的设备唯一标识符。可从 `keyboard.getDevices()` 返回的 `devices` 数组中获取 | 是 |

### 返回值

总体类型：`Promise<{ code: number; info: string }>`

| 字段 | 类型 | 描述 |
|------|------|------|
| code | number | `0` 表示初始化成功 |
| info | string | 初始化结果说明 |

### 使用示例

```javascript
const { devices } = await keyboard.getDevices();

if (!devices.length) {
	throw new Error('未找到可初始化的设备');
}

const result = await keyboard.init(devices[0].id);
console.log('设备初始化结果:', result);
```

## 调试工具位置

在 SDK 调试页面中，这两个接口位于：

`准备开始` → `初始化设备`

完成 `getDevices` 和 `init` 后，再进入“线性轴键盘”“固件升级”等分类调用其它接口。
