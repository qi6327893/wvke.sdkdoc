---
title: WVKE - 完客开放平台 | 打造属于自己的个性化驱动。完客-完美客制！
titleTemplate: false
---

# WVKE - 完客开放平台

> 打造属于自己的个性化驱动。完客-完美客制！

wvke-sdk 是一个用于与游戏外设硬件设备通信的 JavaScript SDK，支持键盘和鼠标设备。

SDK包含硬件底层通讯接口、客户端功能列表枚举接口、云端运营接口。

项目基于现代WebHID技术栈，实现键盘、鼠标等外设的深度定制与智能化控制，旨在加速游戏外设产品整体落地速度，赋能游戏生态的无限可能；项目由GDE 游戏驱动引擎团队开发和维护，引领游戏外设技术革新的专业SDK平台，为开发者提供企业级的硬件通信解决方案。

## SDK 类型

### ⌨️ 线性轴键盘

- 按键映射和层级切换
- RGB灯光控制（单色/全彩/流光效果）
- WebHID 授权、已授权设备读取和指定设备初始化
- RT 全行程开关、触发行程和安全区调节
- 宏录制和脚本执行
- 回报率调节（1KHz-8KHz）
- 系统类型切换（Windows/Mac）

### ⌨️ 机械轴键盘

- 独立的 `ServiceKeyboardMK` 服务分类
- 按键映射、层级切换和按键编组
- RGB 灯光、Logo 灯光和自定义灯效
- 回报率、配置槽位和系统类型设置
- MT、TGL、END、SOCD 高级按键功能

### 🖱️ 鼠标 SDK

- DPI调节（400-16000 DPI）
- 按键编程和宏设置
- RGB灯光自定义
- 抬升高度调节
- 轮询率优化

## 特性

- 🚀 现代 JavaScript/TypeScript 支持
- 📱 WebHID 硬件通信
- 🎨 完整的灯光控制
- ⌨️ 按键映射和宏支持
- 🎯 RT 行程、高级按键和指定设备初始化
- 🖱️ 鼠标DPI和按键编程
- 🔄 在线固件推送升级
- 🎮 手柄模式切换与实时测试（XBOX / Classic）
- 📚 详细的 API 文档

## 使用教程

### 安装

```bash
npm install wvke-sdk
```

### 键盘使用

```javascript
import { ServiceKeyboard } from 'wvke-sdk';

// 推荐：初始化时传入设备参数（vendorId/productId/usagePage/usage）
const vendorId = 0x0000; // 你的VID
const productId = 0x0000; // 你的PID
const keyboard = new ServiceKeyboard({
	configs: [{ vendorId, productId, usagePage: 0xffb0, usage: 0x01 }],
	usage: 0x01,
	usagePage: 0xffb0,
});

// 首次连接时调起浏览器授权；configs 可临时覆盖合法 VID/PID 数组
const { code, devices } = await keyboard.requestDevices([
	{ vendorId: 0x1caa, productId: 0x0806, usagePage: 0xffa0, usage: 0x01 }
]);

if (code !== 0 || devices.length === 0) {
	throw new Error('未找到可授权键盘设备');
}

// 已授权设备可直接通过 getDevices() 读取，不会再次弹出授权窗口
const authorizedResult = await keyboard.getDevices();
console.log('已授权设备:', authorizedResult.devices);

// 按 requestDevices / getDevices 返回的 id 初始化指定设备
await keyboard.init(devices[0].id);

// 获取设备信息
const info = await keyboard.getDevicesInfo();
console.log('设备信息:', info);
```

### 鼠标使用

```javascript
import { ServiceMouse } from 'wvke-sdk';

// 推荐：初始化时传入设备参数（vendorId/productId/usagePage/usage）
const vendorId = 0x0000; // 你的VID
const productId = 0x0000; // 你的PID
const mouse = new ServiceMouse({
	configs: [{ vendorId, productId, usagePage: 0xffb1, usage: 0x02 }],
	usage: 0x02,
	usagePage: 0xffb1,
});

// 获取设备列表
const devices = await mouse.getDevices();

// 初始化设备
await mouse.init(devices[0].id);

// 获取DPI
const dpi = await mouse.getDPI();
console.log('当前DPI:', dpi.value);
```

### 通用自定义升级

```javascript
import { ServiceFirmwareUpgrade } from 'wvke-sdk';

const filters = [{ vendorId: 0x1caa, productId: 0x0806, usagePage: 0xffa0, usage: 0x01 }];

const [device] = await navigator.hid.requestDevice({ filters });
if (!device.opened) {
	await device.open();
}

const firmwareUpgrade = new ServiceFirmwareUpgrade();

await firmwareUpgrade.enterBoot(device);

const [bootDevice] = await navigator.hid.requestDevice({ filters });
if (!bootDevice.opened) {
	await bootDevice.open();
}

// 本地路径可以使用站点可访问的 .bin 文件路径
await firmwareUpgrade.upgrade(bootDevice, '/firmware/example.bin');

// 远程路径仅支持 HTTPS
await firmwareUpgrade.upgrade(bootDevice, 'https://example.com/firmware/example.bin');
```

更完整的参数、进度回调和调试工具说明见 [通用自定义升级 API](./api/firmware-upgrade.md)。

---

## 媒体宣发合作

<button onclick="this.style.display='none';document.getElementById('wk-contact').style.display='block'" style="background:none;border:1px solid #f59e0b;color:#f59e0b;border-radius:6px;padding:8px 18px;cursor:pointer;font-size:15px;font-weight:700">📢 媒体宣发合作 点击这里</button>

<div id="wk-contact" style="display:none;margin-top:14px;padding:18px 22px;border:1px solid #e2e8f0;border-radius:8px;background:#f8fafc">
  <p style="margin:0 0 10px;font-size:14px;color:#334155">媒体宣发合作请联系：</p>
  <a href="mailto:aaron@niudodo.com" style="font-size:20px;font-weight:900;color:#2563eb;text-decoration:none">aaron@niudodo.com</a>
</div>
