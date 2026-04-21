# 全局功能

## 获取设备列表

```javascript
import { ServiceMouse } from 'wvke-sdk';

const mouse = new ServiceMouse();
const devices = await mouse.getDevices();

if (devices.length === 0) {
  throw new Error('未找到鼠标设备');
}

console.log(devices);
```

返回示例：
```json
[
  {
    "id": "mouse-device-1",
    "usage": 2,
    "usagePage": 65440,
    "vendorId": 7331,
    "productId": 2050,
    "productName": "Gaming Mouse Pro",
    "type": "mouse"
  }
]
```

## 初始化设备

```javascript
await mouse.init(devices[0].id);
```

## 获取设备信息

```javascript
const info = await mouse.getDevicesInfo();
console.log(info);
```

返回示例：
```json
{
  "type": 2,
  "subType": 0,
  "boardId": 3145730,
  "appVersion": "1.0.1.0",
  "pcbVersion": "1-0-0-0",
  "runModeVersion": 0,
  "sn": "54817806657765148212548",
  "timestamp": "2025052215:4"
}
```

## 获取协议版本

```javascript
const version = await mouse.getProtocolVersion();
console.log(version);
```

返回示例：
```json
{
  "mainVersion": 1,
  "subVersion": 0,
  "hardwareVersion": 1,
  "softwareVersion": 0
}
```
