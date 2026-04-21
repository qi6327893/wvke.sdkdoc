---
title: wvke-sdk | Perfect Customization!
titleTemplate: false
---

# wvke-sdk

wvke-sdk is a JavaScript SDK for communicating with gaming peripherals, including keyboards and mice.

Built on a modern WebHID technology stack, the project enables deep customization and intelligent control of keyboards, mice, and other peripherals. It is designed to accelerate end-to-end delivery of gaming peripheral products and unlock broader possibilities across the gaming ecosystem. The project is developed and maintained by the GDE Game Driver Engine Team as a professional SDK platform for gaming peripheral innovation, providing enterprise-grade hardware communication solutions for developers.

## SDK Types

### ⌨️ Linear Keyboard

Built for mechanical keyboards, with support for:

- Key mapping and layer switching
- RGB lighting control
- Macro recording and playback
- Polling rate tuning (1KHz-8KHz)
- System mode switching (Windows/Mac)

### 🖱️ Mouse SDK

Built for gaming mice, with support for:

- DPI tuning (400-16000 DPI)
- Button programming and macros
- RGB lighting customization
- Lift-off distance adjustment
- Polling optimization

## Features

- Modern JavaScript/TypeScript support
- WebHID hardware communication
- Full lighting control APIs
- Key mapping and macro APIs
- Mouse DPI and button mapping APIs
- Detailed API documentation

## Quick Start

```bash
npm install wvke-sdk
```

## Keyboard Example

```javascript
import { ServiceKeyboard } from 'wvke-sdk';

// Recommended: pass device params (vendorId/productId/usagePage/usage) on initialization
const vendorId = 0x0000; // your VID
const productId = 0x0000; // your PID
const keyboard = new ServiceKeyboard({
	configs: [{ vendorId, productId, usagePage: 0xffb0, usage: 0x01 }],
	usage: 0x01,
	usagePage: 0xffb0,
});

const { code, devices } = await keyboard.getDevices();

if (code !== 0 || devices.length === 0) {
	throw new Error('No keyboard devices found');
}

await keyboard.init(devices[0].id);
const info = await keyboard.getDevicesInfo();
console.log('Device info:', info);
```

## Mouse Example

```javascript
import { ServiceMouse } from 'wvke-sdk';

// Recommended: pass device params (vendorId/productId/usagePage/usage) on initialization
const vendorId = 0x0000; // your VID
const productId = 0x0000; // your PID
const mouse = new ServiceMouse({
	configs: [{ vendorId, productId, usagePage: 0xffb1, usage: 0x02 }],
	usage: 0x02,
	usagePage: 0xffb1,
});

const devices = await mouse.getDevices();
await mouse.init(devices[0].id);
const dpi = await mouse.getDPI();
console.log('Current DPI:', dpi.value);
```