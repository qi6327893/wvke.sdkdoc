# 全局功能

## 概述

本页记录 SDK 调试器“机械轴键盘 → 全局功能”分类中的 13 个接口。设备发现和初始化位于“准备开始 → 初始化设备”，请先完成 `keyboard.init()`，再调用本页接口。

除机械轴专用限制外，返回值和事件机制与 `ServiceKeyboard` 的对应接口一致。

## 获取设备信息

`ServiceKeyboardMK.getDevicesInfo()`

简要描述：读取当前已初始化设备的板卡、固件、连接模式和序列号信息。

### 参数

此方法不需要参数。

### 返回值

返回 `Promise<DeviceInfo>`。常用字段包括 `BoardID`、`FWsize`、`RunMode`、`SN`、`Version`、`ConnectMode`、`RFVer` 和 `CheckNum`。

### 使用示例

```javascript
const info = await keyboard.getDevicesInfo();
console.log(info.Version, info.ConnectMode);
```

## 开启主动上报全局监听

`ServiceKeyboardMK.startGlobalReportListener(options?)`

简要描述：开启配置切换、背光基础参数、Logo 灯基础参数、电池状态等全局主动上报。SDK 会发送 `0xBD = 1`，并定期发送心跳维持会话。

### 参数

| 参数 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| options | object | 可选项。`includeCommandResponses: true` 时也派发 SDK 命令响应。 | 否 |

### 返回值

返回监听状态对象，包含 `enabled`、`events`、`includeCommandResponses` 和 `heartbeatIntervalMs`。

### 使用示例

```javascript
const handler = report => console.log('全局上报:', report);
keyboard.on('keyboardGlobalReport', handler);
await keyboard.startGlobalReportListener();
```

## 关闭主动上报全局监听

`ServiceKeyboardMK.stopGlobalReportListener()`

简要描述：发送 `0xBD = 0`，关闭全局主动上报并清理 SDK 心跳定时器。

### 参数

此方法不需要参数。

### 使用示例

```javascript
await keyboard.stopGlobalReportListener();
keyboard.off('keyboardGlobalReport', handler);
```

## 获取当前配置槽位

`ServiceKeyboardMK.getConfig()`

简要描述：读取当前激活的配置槽位索引和配置名称。

### 返回值

返回 `{ code, configName, value }`，其中 `value` 为当前配置索引。

```javascript
const config = await keyboard.getConfig();
console.log(config.configName, config.value);
```

## 设置配置槽位

`ServiceKeyboardMK.setConfig(configIndex)`

简要描述：切换当前激活的配置槽位。

### 参数

| 参数 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| configIndex | number | 配置槽位索引，通常为设备支持的 `0` 至 `3`。 | 是 |

### 使用示例

```javascript
await keyboard.setConfig(0);
```

## 获取系统类型

`ServiceKeyboardMK.getSystemType()`

简要描述：读取当前系统类型，通常为 `WIN` 或 `MAC`，用于确定修饰键和系统快捷键行为。

### 返回值

返回包含系统类型值和可读标签的对象。

```javascript
const systemType = await keyboard.getSystemType();
```

## 设置系统类型

`ServiceKeyboardMK.setSystemType(systemType)`

简要描述：设置键盘当前使用的操作系统类型。

### 参数

| 参数 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| systemType | string | `WIN` 或 `MAC`。 | 是 |

```javascript
await keyboard.setSystemType('WIN');
```

## 获取回报率

`ServiceKeyboardMK.getRateOfReturn()`

简要描述：读取当前 USB/无线报告率。

### 返回值

返回当前回报率对象，通常包含协议值、显示名称或 `key` 字段。

```javascript
const rate = await keyboard.getRateOfReturn();
console.log(rate);
```

## 设置回报率

`ServiceKeyboardMK.setRateOfReturn(value)`

简要描述：设置设备报告率。支持设备协议值以及 `125Hz`、`250Hz`、`500Hz`、`1000Hz`、`2000Hz`、`4000Hz`、`8000Hz` 等可读值，实际选项以设备返回结果为准。

### 参数

| 参数 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| value | number \| string | 回报率协议值或可读名称。 | 是 |

```javascript
await keyboard.setRateOfReturn('1000Hz');
```

## 获取灯光休眠时间

`ServiceKeyboardMK.getLightingSleepTime()`

简要描述：读取设备灯光休眠时间。`0` 通常表示从不休眠。

### 返回值

返回包含时间值和可读描述的对象。

```javascript
const sleep = await keyboard.getLightingSleepTime();
```

## 设置灯光休眠时间

`ServiceKeyboardMK.setLightingSleepTime(time)`

简要描述：设置键盘灯光自动休眠时间。

### 参数

| 参数 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| time | number | 固件支持的分钟值，具体枚举以设备为准。 | 是 |

```javascript
await keyboard.setLightingSleepTime(15);
```

## 恢复出厂设置

`ServiceKeyboardMK.GFSRestore()`

简要描述：恢复设备配置和相关设置的出厂状态。执行后建议重新读取配置和灯光数据。

### 参数

此方法不需要参数。

```javascript
await keyboard.GFSRestore();
```

## 恢复指定配置

`ServiceKeyboardMK.restoreProfile(profileIndex)`

简要描述：恢复指定 profile 配置。

### 参数

| 参数 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| profileIndex | number | 要恢复的 profile 索引，通常为 `0` 至 `3`。 | 是 |

```javascript
await keyboard.restoreProfile(0);
```
