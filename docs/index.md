---
title: WVKE - 完客开放平台 | 完客-完美客制！
titleTemplate: false
---

# WVKE - 完客开放平台

> 完客-完美客制！

wvke-sdk 是一个用于与游戏外设硬件设备通信的 JavaScript SDK，支持键盘和鼠标设备。

项目基于现代WebHID技术栈，实现键盘、鼠标等外设的深度定制与智能化控制，旨在加速游戏外设产品整体落地速度，赋能游戏生态的无限可能；项目由GDE 游戏驱动引擎团队开发和维护，引领游戏外设技术革新的专业SDK平台，为开发者提供企业级的硬件通信解决方案。

## SDK 类型

### ⌨️ 线性轴键盘

- 按键映射和层级切换
- RGB灯光控制（单色/全彩/流光效果）
- 宏录制和脚本执行
- 回报率调节（1KHz-8KHz）
- 系统类型切换（Windows/Mac）

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
- 🖱️ 鼠标DPI和按键编程
- 🔄 在线固件推送升级
- 📚 详细的 API 文档

## 快速开始

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

// 获取设备列表
const { code, devices } = await keyboard.getDevices();

if (code !== 0 || devices.length === 0) {
	throw new Error('未找到可用键盘设备');
}

// 初始化设备
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