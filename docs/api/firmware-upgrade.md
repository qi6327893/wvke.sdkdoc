# 通用自定义升级

## 概览

`ServiceFirmwareUpgrade` 是独立的通用自定义升级服务。业务侧先把 App 设备切到 BOOT 模式，等待设备自动重启后重新选择并打开 BOOT 设备，再传入 BOOT 设备和 `.bin` 文件路径开始正式升级。升级 RF 芯片时不需要设备先进入 BOOT，可以直接传入已打开设备调用 `upgrade()` 开始升级。SDK 内部会完成旧升级流程中的签名解锁、擦除、分包写入、CRC 校验和跳转 App。

当前公开接口：

- `firmwareUpgrade.enterBoot(device, options?)`
- `firmwareUpgrade.connectBootDevice(options?)`
- `firmwareUpgrade.upgrade(device, binFilePath, options?)`
  - 也支持 `upgrade({ binFilePath?, binData?, onProgress, ... })` 参数对象格式

`enterBoot` 会按协议配置发送进入 BOOT 命令，HID 报告长度由 SDK 内部固定按 64 字节发送。

## enterBoot

ServiceFirmwareUpgrade.enterBoot(device, options?)

简述：向已打开的 App 设备发送进入 BOOT 命令。设备会自动重启；重启后需要业务侧再次调用 `connectBootDevice()` 或 `navigator.hid.requestDevice()` 选择 BOOT 设备并打开。

### 参数

| 字段 | 类型 | 说明 | 必填 |
|------|------|------|------|
| device | HIDDevice | 已打开的 App 模式 WebHID 设备 | 是 |
| options | object | 可选配置，支持 `reportId`、`onProgress` 等字段 | 否 |

### 返回值

整体类型：`Promise<{ code: number, success: boolean, requiresReconnect: boolean, message: string }>`

## connectBootDevice

ServiceFirmwareUpgrade.connectBootDevice(options?)

简述：调起浏览器 WebHID 设备选择器，选择并打开 BOOT 模式设备。选择成功后 SDK 会保存该设备，后续调用 `upgrade()` 时可省略 device 参数。

### 参数

| 字段 | 类型 | 说明 | 必填 |
|------|------|------|------|
| options.filters | Array | WebHID 过滤数组，默认使用 SDK 内置合法 VID/PID | 否 |

### 返回值

整体类型：`Promise<{ code: number, success: boolean, message: string, device: object }>`

## upgrade

ServiceFirmwareUpgrade.upgrade(device, binFilePath, options?)

简述：升级一个已重新连接并打开的 BOOT 设备固件；升级 RF 芯片时无需先进入 BOOT，可直接传入已打开设备开始升级。

也支持参数对象格式：`upgrade({ binFilePath?, binData?, onProgress, ... })`，此时 SDK 会使用 `connectBootDevice()` 已保存的 BOOT 设备。

### 参数（传统格式）

| 字段 | 类型 | 说明 | 必填 |
|------|------|------|------|
| device | HIDDevice | 已打开的 BOOT 模式 WebHID 设备 | 是 |
| binFilePath | string \| Blob | `.bin` 固件路径或浏览器文件对象 | 是 |
| options | object | 升级选项、超时配置和进度回调 | 否 |

### 参数（对象格式）

| 字段 | 类型 | 说明 | 必填 |
|------|------|------|------|
| binFilePath | string | `.bin` 固件路径或 HTTPS URL，与 `binData` 二选一 | 否 |
| binData | ArrayBuffer \| Uint8Array | 已读取的固件二进制数据，与 `binFilePath` 二选一 | 否 |
| onProgress | `(progress) => void` | 升级进度回调 | 否 |
| timeout | number | 普通命令响应超时，默认 `5000` ms | 否 |
| eraseTimeout | number | 擦除阶段超时，默认 `15000` ms | 否 |
| writePacketDelay | number | 固件分包写入间隔，默认 `2` ms | 否 |
| commandDelay | number | 签名链命令间隔，默认 `20` ms | 否 |

`options` 常用字段：

| 字段 | 类型 | 说明 |
|------|------|------|
| onProgress | `(progress) => void` | 升级进度回调 |
| timeout | number | 普通命令响应超时，默认 `5000` ms |
| eraseTimeout | number | 擦除阶段超时，默认 `15000` ms |
| writePacketDelay | number | 固件分包写入间隔，默认 `2` ms |
| commandDelay | number | 签名链命令间隔，默认 `20` ms |
| deviceInfoTimeout | number | 读取设备信息超时，默认 `3000` ms |
| crcTimeout | number | 读取设备 CRC 超时，默认 `10000` ms |
| serialNumber | string \| number[] \| Uint8Array | 可选设备 SN。未传时 SDK 会优先使用设备缓存的 `SNDatas`，缺失时自动读取设备信息 |
| sn / SNDatas / snDatas | string \| number[] \| Uint8Array | `serialNumber` 的兼容别名 |

进度 `phase` 可能为：`boot`、`bootConnected`、`loading`、`loaded`、`sign`、`erase`、`write`、`crc`、`complete`。

### 返回值

整体类型：`Promise<{ code: number, success: boolean, source?: string | Blob, size: number, paddedSize: number, packetCount: number, crc: number, deviceCrc: number }>`

| 字段 | 类型 | 说明 |
|------|------|------|
| code | number | `0` 表示升级流程完成 |
| success | boolean | 是否成功 |
| source | string \| Blob（可选） | 固件来源；使用 `binData` 时可能不返回 |
| size | number | 原始固件大小 |
| paddedSize | number | 按 512 字节补齐后的大小 |
| packetCount | number | 写入分包数量 |
| crc | number | 本地计算 CRC |
| deviceCrc | number | 设备返回 CRC |

如果设备 CRC 与本地 CRC 不一致，SDK 会抛出错误并停止跳转 App。

## 示例

```javascript
import { ServiceFirmwareUpgrade } from 'wvke-sdk';

const filters = [{ vendorId: 0x1caa, productId: 0x0806, usagePage: 0xffa0, usage: 0x01 }];

const [device] = await navigator.hid.requestDevice({ filters });
if (!device.opened) {
	await device.open();
}

const firmwareUpgrade = new ServiceFirmwareUpgrade();

await firmwareUpgrade.enterBoot(device);

// 设备会重启进入 BOOT 模式，需要按 getDevices 同源 filters 重新选择并打开 BOOT 设备。
// 如果升级 RF 芯片，无需执行 enterBoot，可直接将已打开设备传给 upgrade()。
const [bootDevice] = await navigator.hid.requestDevice({ filters });
if (!bootDevice.opened) {
	await bootDevice.open();
}

const result = await firmwareUpgrade.upgrade(bootDevice, '/firmware/example.bin', {
	onProgress(progress) {
		console.log(progress.phase, progress.progress, progress.current, progress.total);
	}
});

console.log('升级完成:', result);
```

浏览器本地文件示例：

```javascript
const fileInput = document.querySelector('input[type="file"]');
const firmwareFile = fileInput.files[0];
await firmwareUpgrade.upgrade(bootDevice, firmwareFile);
```

远程固件示例：

```javascript
await firmwareUpgrade.upgrade(bootDevice, 'https://example.com/firmware/example.bin');
```

已知 SN 时可以显式传入：

```javascript
await firmwareUpgrade.upgrade(bootDevice, '/firmware/example.bin', {
	serialNumber: '2493C45353E0A336'
});
```

## 调试工具

在 SDK 调试页面中，通用自定义升级分组按三步执行：

1. 在“准备开始 / 初始化设备”中先调用键盘 `getDevices` 和 `init`，再点击 `enterBoot` 让设备进入 BOOT 并自动重启。
2. 设备重启后点击 `connectBootDevice`，调试页会按 `getDevices` 同源 filters 在浏览器弹窗里重新选择 BOOT 设备。
3. 点击 `upgrade`，调试页会使用第 2 步连接到的 BOOT 设备正式升级，并显示升级进度。

## 注意事项

TIP

- 浏览器不能直接读取任意本机磁盘路径。可以传入用户通过文件选择框选择的 `File`，或把站点本地固件放在当前站点可访问的位置，例如 `/firmware/example.bin`。
- 远程固件必须使用 HTTPS 地址；开发环境仅允许 localhost 类型的 HTTP 地址。
- 升级过程中不要断开设备或刷新页面。
- 请确保固件文件与目标设备型号匹配。
