# 全局功能

本页记录初始化完成后的键盘全局接口。设备发现和初始化接口已移动到 [初始化设备](./initialize.md)。

## 获取设备信息

keyboard.getDevicesInfo()

简要描述: 获取当前已初始化设备的信息。

### 参数

此方法不需要参数 (通常在成功调用 `keyboard.init()` 后使用)。

### 返回值

• 总体类型: `Promise<DeviceInfo>`
• 描述: 返回一个 `Promise`，该 `Promise` 解析为一个包含设备信息的 `DeviceInfo` 对象。
• 解析对象结构 (`DeviceInfo`):

| 字段 | 类型 | 描述 | 示例 |
|------|------|------|------|
| BoardID | number | 板卡ID | 1174538260 |
| FWsize | number | 固件大小标识 | 2 |
| RunMode | number | 运行模式 | 0 |
| SN | string | 设备序列号 | "2493C45353E0A336" |
| Version | string | 固件版本号 | "004" |
| ConnectMode | number | 连接模式：`0` 有线，`1`/`2`/`3` 分别为蓝牙通道 1/2/3，`4` 为 2.4G | 4 |
| RFVer | number | RF 芯片版本号，只取低位字节 | 2 |
| CheckNum | string | 校验值 | "00000000" |

示例返回结构：

```json
{
  "BoardID": 1174538260,
  "FWsize": 2,
  "RunMode": 0,
  "SN": "2493C45353E0A336",
  "Version": "004",
  "ConnectMode": 4,
  "RFVer": 2,
  "CheckNum": "00000000"
}
```

## 开启主动上报全局监听

ServiceKeyboard.startGlobalReportListener(options)

简要描述: 监听键盘主动上报的配置切换、键盘背光基础参数变化、Logo 灯基础参数变化、驱动离线设置通知，以及按键行程状态实时渲染开关变化。

`startGlobalReportListener()` 会向固件发送 `0xBD` 启动命令，并在启动后每隔 1 分钟发送一次心跳；实时上报数据通过 `on('keyboardGlobalReport', callback)` 或下方具体事件回调获取。

### 参数

| 参数 | 类型 | 描述 | 是否必需 |
|------|------|------|------|
| options | object | 可选配置。`includeCommandResponses` 为 `true` 时也会派发 SDK 主动发命令产生的响应；默认 `false`。 | 否 |

### 返回值

• 总体类型: `Promise<{ code: number, enabled: boolean, events: string[], includeCommandResponses: boolean, heartbeatIntervalMs: number }>`
• 描述: 返回一个 `Promise`，解析为当前全局主动上报监听状态，其中 `heartbeatIntervalMs` 固定为 `60000`。

### 事件

| 事件名 | 描述 |
|------|------|
| keyboardGlobalReport | 所有已识别的全局主动上报。 |
| keyboardConfigChange | 配置槽位切换上报，包含 `configIndex`、`configName`、`value`。 |
| keyboardLightingChange | 键盘背光基础参数上报，包含 `mode`、`childMode`、`luminance`、`speed`、`sleep`。 |
| keyboardLogoLightingChange | Logo 灯基础参数上报，包含 `mode`、`luminance`、`speed`、`sleep`。 |
| keyboardBatteryChange | 电池电量与充电状态上报，包含 `battery`、`isCharging`。 |
| keyboardOfflineSettingsChange | 驱动离线设置状态通知，包含 `value`、`statusLabel`。 |
| keyboardKeyTravelRenderChange | 按键行程状态实时渲染开关上报，包含 `value`、`enabled`。 |

### 使用示例

```javascript
const handleGlobalReport = (report) => {
  console.log('键盘全局主动上报:', report);
};

ServiceKeyboard.on('keyboardGlobalReport', handleGlobalReport);

ServiceKeyboard.on('keyboardConfigChange', (report) => {
  console.log('当前配置:', report.configIndex);
});

ServiceKeyboard.on('keyboardLightingChange', (report) => {
  console.log('灯光模式/亮度/速度:', report.mode, report.luminance, report.speed);
});

ServiceKeyboard.on('keyboardBatteryChange', (report) => {
  console.log('电池与充电状态:', report.battery, report.isCharging);
});

await ServiceKeyboard.startGlobalReportListener();

// 不再需要实时数据时关闭监听，并移除业务侧回调。
await ServiceKeyboard.stopGlobalReportListener();
ServiceKeyboard.off('keyboardGlobalReport', handleGlobalReport);
```

### 协议说明

TIP

• 固件功能码为 `0xBD`。
• 启动监听时发送 `0xBD = 1`。
• 启动后 SDK 会每隔 60 秒重复发送一次 `0xBD = 1` 作为心跳，避免固件主动上报会话超时。
• 关闭监听时发送 `0xBD = 0`，并清理本地心跳定时器。

### 上报示例

配置切换上报：

```json
{
  "code": 0,
  "type": "config",
  "configIndex": 2,
  "configName": "Config2",
  "value": 2
}
```

键盘背光基础参数上报：

```json
{
  "code": 0,
  "type": "lighting",
  "mode": 1,
  "modeLabel": "静态模式",
  "childMode": 0,
  "luminance": 3,
  "speed": 4,
  "sleep": 15
}
```

电池状态上报：

```json
{
  "code": 0,
  "type": "battery",
  "battery": 87,
  "isCharging": 1
}
```

离线设置通知上报：

```json
{
  "code": 0,
  "type": "offlineSettings",
  "value": 0,
  "statusLabel": "start"
}
```

• value 为 `0` 时表示固件启动离线设置流程，statusLabel 为 `"start"`。
• value 为 `1` 时表示固件离线设置完成，statusLabel 为 `"complete"`。

按键行程状态实时渲染开关上报：

```json
{
  "code": 0,
  "type": "keyTravelRender",
  "value": 1,
  "enabled": true
}
```

## 关闭主动上报全局监听

ServiceKeyboard.stopGlobalReportListener()

简要描述: 关闭通过 `startGlobalReportListener()` 开启的键盘主动上报全局监听。

### 返回值

• 总体类型: `Promise<{ code: number, enabled: boolean }>`
• 描述: 返回一个 `Promise`，解析为关闭后的监听状态。

### 使用示例

```javascript
await ServiceKeyboard.stopGlobalReportListener();
```

## 获取当前配置槽位

ServiceKeyboard.getConfig()

简要描述: 获取当前键盘激活的配置槽位索引。

### 参数

此方法不需要参数。

### 返回值

• 总体类型: `Promise<{ code: number, configName: string, value: number }>`
• 描述: 返回当前激活的配置槽位信息。
• 解析对象结构:

| 字段 | 类型 | 描述 | 示例 |
|------|------|------|------|
| code | number | 接口调用状态码，`0` 表示成功。 | 0 |
| configName | string | 当前配置名称。 | "Config2" |
| value | number | 当前配置索引。范围 `0` 到 `3`。 | 2 |

### 使用示例

```javascript
async function fetchConfig() {
  try {
    const result = await ServiceKeyboard.getConfig();
    console.log('当前配置槽位:', result);
    // 示例输出: { code: 0, configName: 'Config2', value: 2 }
  } catch (error) {
    console.error('获取配置槽位失败:', error);
  }
}
```

## 设置配置槽位

ServiceKeyboard.setConfig(configIndex)

简要描述: 切换到指定的配置槽位。切换后建议重新读取设备配置、灯光和按键数据。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| configIndex | number | 配置槽位索引。范围 `0` 到 `3`，分别对应 4 个配置槽位。 | 是 |

### 返回值

• 总体类型: `Promise<{ code: number }>`
• 描述: 返回设置结果状态码。

### 使用示例

```javascript
async function switchConfig(index) {
  try {
    const result = await ServiceKeyboard.setConfig(index);
    console.log('切换配置槽位结果:', result);
    // 切换后建议刷新灯光、按键和行程相关数据
  } catch (error) {
    console.error('切换配置槽位失败:', error);
  }
}

// 切换到第 0 个配置（Config0）
// switchConfig(0);
```

### 注意事项

TIP

• 配置槽位索引范围为 `0` 到 `3`。
• 切换配置后，灯光参数、按键映射、行程设置等可能发生变化，建议重新读取相关数据。
• 建议在配置切换过程中显示加载状态，以提供更好的用户体验。

# 全局功能

本页包含设备初始化完成后的键盘全局接口。设备发现与初始化请参考 [初始化设备](./initialize.md)。
  • 键盘配置信息
  • 灯光设置
  • 按键映射
  • 其他相关设置
• 建议在配置切换过程中显示加载状态，以提供更好的用户体验。

## 获取当前回报率

ServiceKeyboard.getRateOfReturn()

简要描述: 获取当前键盘回报率。

调试工具中，回报率枚举显示顺序为：`0=8000Hz`、`1=4000Hz`、`2=2000Hz`、`3=1000Hz`、`4=500Hz`、`5=250Hz`、`6=125Hz`。

### 参数

此方法不需要参数。

### 返回值

• 总体类型: `Promise<{ code: number, key: string, value: number }>`
• 描述: 返回一个 `Promise`，该 `Promise` 解析为当前回报率信息。
• 解析对象结构:

| 字段 | 类型 | 描述 | 示例 |
|------|------|------|------|
| code | number | 接口调用状态码，`0` 表示成功。 | 0 |
| key | string | 当前回报率显示值。 | "1000Hz" |
| value | number | 当前回报率协议值。 | 3 |

协议值映射关系：

| 协议值 | 回报率 |
|------|------|
| 0 | 8000Hz |
| 1 | 4000Hz |
| 2 | 2000Hz |
| 3 | 1000Hz |
| 4 | 500Hz |
| 5 | 250Hz |
| 6 | 125Hz |

调试枚举映射关系（与协议值一致）：

| 调试枚举值 | 回报率 |
|------|------|
| 0 | 8000Hz |
| 1 | 4000Hz |
| 2 | 2000Hz |
| 3 | 1000Hz |
| 4 | 500Hz |
| 5 | 250Hz |
| 6 | 125Hz |

### 使用示例

```javascript
async function fetchRateOfReturn() {
  try {
    const rateInfo = await ServiceKeyboard.getRateOfReturn();
    console.log('当前回报率:', rateInfo);
    // 示例输出: { code: 0, key: '1000Hz', value: 3 }
  } catch (error) {
    console.error('获取当前回报率失败:', error);
  }
}

// fetchRateOfReturn();
```

## 设置回报率

ServiceKeyboard.setRateOfReturn()

简要描述: 设置当前键盘回报率。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| value | string \| number | 回报率值。支持协议值 `0` 到 `6`，也支持 `125Hz`、`250Hz`、`500Hz`、`1000Hz`、`2000Hz`、`4000Hz`、`8000Hz`，以及 `1KHz`、`2KHz`、`4KHz`、`8KHz` 这类写法。 | 是 |

### 返回值

• 总体类型: `Promise<{ code: number, key: string, value: number }>`
• 描述: 返回一个 `Promise`，设置成功时解析为当前回报率结果，其中 `key` 为显示值，`value` 为协议值。

### 使用示例

```javascript
async function updateRateOfReturn(value) {
  try {
    const result = await ServiceKeyboard.setRateOfReturn(value);
    console.log('设置回报率结果:', result);
    // 示例输出: { code: 0, key: '1000Hz', value: 4 }
  } catch (error) {
    console.error('设置回报率失败:', error);
  }
}

// 使用协议值
// updateRateOfReturn(3);    // 1000Hz

// 使用显示值
// updateRateOfReturn('1000Hz');

// 使用 KHz 写法
// updateRateOfReturn('8KHz');
```

### 注意事项

TIP

• 键盘固件协议中，回报率使用 `0` 到 `6` 表示不同档位，调试枚举与协议值一致。
• SDK 会自动把常见字符串输入转换为对应协议值。
• 设置回报率后，建议再次调用 `ServiceKeyboard.getRateOfReturn()` 进行确认。

## 获取全局行程位置

ServiceKeyboard.getGlobalJourney()

简要描述: 获取当前键盘全局行程位置（触发位置），单位为 mm。

### 参数

此方法不需要参数。

### 返回值

• 总体类型: `Promise<{ code: number, value: number, unit: string }>`
• 描述: 返回当前全局行程位置。

| 字段 | 类型 | 描述 | 示例 |
|------|------|------|------|
| code | number | 接口调用状态码，`0` 表示成功。 | 0 |
| value | number | 当前全局行程位置，单位 mm。 | 1.5 |
| unit | string | 单位。固定为 `"mm"`。 | "mm" |

### 使用示例

```javascript
async function fetchGlobalJourney() {
  try {
    const result = await ServiceKeyboard.getGlobalJourney();
    console.log('当前全局行程位置:', result);
    // 示例输出: { code: 0, value: 1.5, unit: 'mm' }
  } catch (error) {
    console.error('获取全局行程位置失败:', error);
  }
}
```

## 设置全局行程位置

ServiceKeyboard.setGlobalJourney(value)

简要描述: 设置当前键盘全局行程位置（触发位置），单位为 mm。注意：需固件支持，部分版本可能仅支持读取。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| value | number | 全局行程位置，单位 mm。例如 `1.5`、`2.0`、`3.0` 等。 | 是 |

### 返回值

• 总体类型: `Promise<{ code: number, value: number, unit: string }>`
• 描述: 返回设置后的全局行程位置结果。

### 使用示例

```javascript
async function updateGlobalJourney(value) {
  try {
    const result = await ServiceKeyboard.setGlobalJourney(value);
    console.log('设置全局行程位置结果:', result);
    // 示例输出: { code: 0, value: 2.0, unit: 'mm' }
  } catch (error) {
    console.error('设置全局行程位置失败:', error);
  }
}

// 设置全局行程位置为 2.0mm
// updateGlobalJourney(2.0);
```

### 注意事项

TIP

• 单位固定为 mm，SDK 会自动转换为固件所需的微米（μm）值。
• 需固件支持，部分固件版本可能仅支持读取全局行程位置，设置操作可能无效。
• 全局行程位置与单个按键的 `setPerformance` 中的 `triggerPosition` 不同：全局行程是默认值，单键配置可以覆盖。

## 获取 RT 全行程开关

ServiceKeyboard.getRtAllJourneySwitch()

简要描述: 获取当前 RT 全行程开关状态。

### 参数

此方法不需要参数。

### 返回值

• 总体类型: `Promise<{ code: number, key: string, value: number, enabled: boolean }>`
• 描述: 返回一个 `Promise`，解析为当前 RT 全行程开关状态。

| 字段 | 类型 | 描述 | 示例 |
|------|------|------|------|
| code | number | 接口调用状态码，`0` 表示成功。 | 0 |
| key | string | 当前开关状态描述。 | "开启" |
| value | number | 当前协议值。`1`=开启，`0`=关闭。 | 1 |
| enabled | boolean | 当前是否开启。 | true |

### 协议说明

TIP

• 读取命令: `5C 03 00 93 A1 FF FF FF`。
• 响应中的状态值读取自第 7 字节。
• `value=1` 表示 RT 全行程开启，`value=0` 表示关闭。

### 使用示例

```javascript
async function fetchRtAllJourneySwitch() {
  const result = await ServiceKeyboard.getRtAllJourneySwitch();
  console.log('RT 全行程开关:', result);
}
```

## 设置 RT 全行程开关

ServiceKeyboard.setRtAllJourneySwitch(value)

简要描述: 设置当前 RT 全行程开关状态。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| value | boolean \| string \| number | 开关值。支持 `1/0`、`true/false`、`enable/disable`、`on/off`。 | 是 |

### 返回值

• 总体类型: `Promise<{ code: number, key: string, value: number, enabled: boolean }>`
• 描述: 返回一个 `Promise`，设置成功时解析为当前生效的开关状态。

### 协议说明

TIP

• 设置命令: `5C 03 00 93 A1 value FF FF`。
• 第 6 字节为开关值，`1` 表示开启，`0` 表示关闭。

### 使用示例

```javascript
await ServiceKeyboard.setRtAllJourneySwitch(1);
await ServiceKeyboard.setRtAllJourneySwitch(false);
```

## 获取休眠时间

ServiceKeyboard.getLightingSleepTime()

简要描述: 获取当前键盘休眠时间设置。

### 参数

此方法不需要参数。

### 返回值

• 总体类型: `Promise<{ code: number, key: string, value: number }>`
• 描述: 返回一个 `Promise`，解析为当前休眠时间结果，其中 `value` 为协议分钟值，`key` 为可读描述。
• 解析对象结构:

| 字段 | 类型 | 描述 | 示例 |
|------|------|------|------|
| code | number | 接口调用状态码，`0` 表示成功。 | 0 |
| key | string | 当前休眠时间的可读描述。 | "15分钟" |
| value | number | 当前休眠时间协议值。`0` 表示从不睡眠。 | 15 |

支持的典型值：

| 协议值 | 含义 |
|------|------|
| 0 | 从不睡眠 |
| 1 | 1分钟 |
| 2 | 2分钟 |
| 3 | 3分钟 |
| 5 | 5分钟 |
| 10 | 10分钟 |
| 15 | 15分钟 |
| 20 | 20分钟 |
| 30 | 30分钟 |
| 45 | 45分钟 |
| 60 | 60分钟 |

### 使用示例

```javascript
async function fetchLightingSleepTime() {
  try {
    const result = await ServiceKeyboard.getLightingSleepTime();
    console.log('当前休眠时间:', result);
    // 示例输出: { code: 0, key: '15分钟', value: 15 }
  } catch (error) {
    console.error('获取休眠时间失败:', error);
  }
}

// fetchLightingSleepTime();
```

## 设置休眠时间

ServiceKeyboard.setLightingSleepTime(time)

简要描述: 设置当前键盘休眠时间。`0` 表示从不睡眠。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| time | string \| number | 休眠时间。支持 `0`、`1`、`2`、`3`、`5`、`10`、`15`、`20`、`30`、`45`、`60`，也支持如 `15分钟` 的写法。 | 是 |

### 返回值

• 总体类型: `Promise<{ code: number, key: string, value: number }>`
• 描述: 返回一个 `Promise`，设置成功时解析为当前生效的休眠时间结果。

### 使用示例

```javascript
async function updateLightingSleepTime(time) {
  try {
    const result = await ServiceKeyboard.setLightingSleepTime(time);
    console.log('设置休眠时间结果:', result);
    // 示例输出: { code: 0, key: '从不睡眠', value: 0 }
  } catch (error) {
    console.error('设置休眠时间失败:', error);
  }
}

// 设置为从不睡眠
// updateLightingSleepTime(0);

// 设置为 15 分钟
// updateLightingSleepTime(15);
```

### 注意事项

TIP

• `time` 仅支持固定枚举值：`0`、`1`、`2`、`3`、`5`、`10`、`15`、`20`、`30`、`45`、`60`。
• `0` 表示从不睡眠。
• 设置成功后，返回结果中的 `key` 可直接用于界面展示。

## 恢复出厂设置

ServiceKeyboard.GFSRestore()

简要描述: 将键盘恢复到出厂默认设置。

### 参数

此方法不需要参数。

### 返回值

• 总体类型: `Promise<{ code: number, success: boolean, message: string }>`
• 描述: 返回一个 `Promise`，恢复成功后解析为恢复结果对象。

| 字段 | 类型 | 描述 | 示例 |
|------|------|------|------|
| code | number | 接口调用状态码，`0` 表示成功。 | 0 |
| success | boolean | 是否恢复成功。 | true |
| message | string | 恢复结果说明。 | "Factory settings restored successfully" |

### 使用示例

```javascript
async function restoreFactorySettings() {
  try {
    const result = await ServiceKeyboard.GFSRestore();
    console.log('恢复出厂设置结果:', result);
    // 示例输出: { code: 0, success: true, message: 'Factory settings restored successfully' }
  } catch (error) {
    console.error('恢复出厂设置失败:', error);
  }
}

// restoreFactorySettings();
```

### 注意事项

TIP

• 恢复出厂设置后，配置槽位、灯光参数、系统类型、休眠时间等全局设置可能恢复为默认值。
• 恢复完成后，建议重新调用 `getConfig()`、`getSystemType()`、`getRateOfReturn()`、`getLightingSleepTime()` 等接口刷新当前状态。
• 如果业务侧缓存了按键布局、DPI 或其他设备数据，也建议同步刷新。

## 恢复指定配置

ServiceKeyboard.restoreProfile(profileIndex)

简要描述：恢复指定的配置 profile。profileIndex 范围 0-3，0 表示清除第一个 profile。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| profileIndex | number | profile 编号，范围 0-3 | 是 |

### 返回值

• 总体类型：`Promise<{ code: number, success: boolean, profileIndex: number, message: string }>`

### 使用示例

```javascript
const result = await ServiceKeyboard.restoreProfile(0);
console.log('恢复 profile 结果:', result);
```

## 获取系统类型

ServiceKeyboard.getSystemType()

简要描述: 获取当前键盘设置的系统类型（Windows或Mac）。

### 参数

此方法不需要参数。

### 返回值

• 总体类型: `Promise<{ key: string, value: number }>`
• 描述: 返回一个 `Promise`，该 `Promise` 解析为包含系统类型信息的对象。
• 解析对象结构:

| 字段 | 类型 | 描述 | 示例 |
|------|------|------|------|
| key | string | 系统类型名称，可能的值为 "WIN" 或 "MAC"。 | "MAC" |
| value | number | 系统类型对应的值，0 表示 Windows，1 表示 Mac。 | 1 |

### 使用示例

```javascript
async function fetchSystemType() {
  try {
    const systemType = await ServiceKeyboard.getSystemType();
    console.log('当前系统类型:', systemType);
    // 示例输出: { "key": "MAC", "value": 1 }
  } catch (error) {
    console.error('获取系统类型失败:', error);
  }
}

// fetchSystemType();
```

## 设置系统类型

ServiceKeyboard.setSystemType()

简要描述: 设置键盘的系统类型（Windows或Mac）。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| systemType | string | 要设置的系统类型，只能是 "WIN" 或 "MAC"。 | 是 | 无 |

### 返回值

• 总体类型: `Promise<{ code: number, key: string, value: number }>`
• 描述: 返回一个 `Promise`，操作成功后返回当前系统类型结果，其中 `key` 为系统类型名称，`value` 为协议值。

### 使用示例

```javascript
async function setSystemType(systemType) {
  try {
    // systemType 只能是 "WIN" 或 "MAC"
    const result = await ServiceKeyboard.setSystemType(systemType);
    console.log('设置系统类型结果:', result);
    // 示例输出: { code: 0, key: 'WIN', value: 0 }
  } catch (error) {
    console.error('设置系统类型失败:', error);
  }
}

// 设置为 Windows 系统
// setSystemType("WIN");

// 设置为 Mac 系统
// setSystemType("MAC");
```

### 注意事项

TIP

• 参数 `systemType` 只能是 "WIN" 或 "MAC"。
• 切换系统类型后，键盘的某些功能可能会有所不同，例如按键映射、修饰键布局等。
• 建议在设置系统类型后，验证键盘的功能是否正常工作。

## 设置键盘 SN 号

ServiceKeyboard.setSn(sn, length?)

简要描述: 设置键盘的序列号（SN）。支持指定 SN 字节长度，不足部分自动用 `0x00` 填充。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| sn | string | 键盘序列号。 | 是 |
| length | number | SN 字节长度，默认 `2`，范围 `1`~`20`。 | 否 |

### 返回值

• 总体类型: `Promise<{ code: number, sn: string, length: number }>`
• 描述: 返回一个 `Promise`，设置成功时解析为包含 SN 和实际长度的结果。

### 使用示例

```javascript
async function setKeyboardSn(sn, length) {
  try {
    const result = await ServiceKeyboard.setSn(sn, length);
    console.log('设置 SN 结果:', result);
    // 示例输出: { code: 0, sn: 'AB', length: 2 }
  } catch (error) {
    console.error('设置 SN 失败:', error);
  }
}

// setKeyboardSn('AB');           // 默认2字节
// setKeyboardSn('ABCDEF', 6);    // 指定6字节
// setKeyboardSn('HELLO', 20);    // 补齐到20字节
```

### 协议说明

TIP

• 设置命令: 首字节 `0xB1`，第一数据字节为 SN 长度（`length`），后接 SN 的 ASCII 字节。

## 读取键盘 SN 号

ServiceKeyboard.getSn(length?)

简要描述: 读取键盘当前的序列号（SN）。可指定读取的字节长度。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| length | number | 读取的 SN 字节长度，默认 `2`，范围 `1`~`20`。 | 否 |

### 返回值

• 总体类型: `Promise<{ code: number, sn: string, length: number }>`
• 描述: 返回一个 `Promise`，解析为包含 SN 字符串和实际长度的结果。

| 字段 | 类型 | 描述 | 示例 |
|------|------|------|------|
| code | number | 接口调用状态码，`0` 表示成功。 | 0 |
| sn | string | 键盘序列号。 | "AB" |
| length | number | 实际读取的字节长度。 | 2 |

### 使用示例

```javascript
async function fetchSn(length) {
  try {
    const result = await ServiceKeyboard.getSn(length);
    console.log('当前 SN:', result);
    // 示例输出: { code: 0, sn: 'AB', length: 2 }
  } catch (error) {
    console.error('获取 SN 失败:', error);
  }
}

// fetchSn();      // 默认读取2字节
// fetchSn(20);    // 读取20字节
```

### 协议说明

TIP

• 读取命令: `0xB2`。首数据字节为 SN 长度。
• 响应中第一数据字节为 SN 长度，后续字节为 SN 的 ASCII 字符。

## 设置系统通知

ServiceKeyboard.setSystemNotification({ volume?, brightness? })

简要描述: 通过键盘下发系统通知命令（0xBF），控制系统音量和屏幕亮度。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| volume | number | 系统音量值，范围 `0` 到 `100`。 | 否 |
| brightness | number | 屏幕亮度值，范围 `0` 到 `100`。 | 否 |

• 至少需要传入 `volume` 或 `brightness` 其中之一。

### 返回值

• 总体类型: `Promise<{ code: number, volume?: number, brightness?: number }>`
• 描述: 返回实际设置的值。

### 使用示例

```javascript
// 仅设置系统音量
await ServiceKeyboard.setSystemNotification({ volume: 60 });

// 仅设置屏幕亮度
await ServiceKeyboard.setSystemNotification({ brightness: 80 });

// 同时设置
await ServiceKeyboard.setSystemNotification({ volume: 50, brightness: 75 });
```

### 协议说明

TIP

• 设置命令: `0xBF`。
• BYTE1: `0x01`（系统音量类型），BYTE2: 音量值（0~100）。
• BYTE3: `0x02`（屏幕亮度类型），BYTE4: 亮度值（0~100）。

## 获取 WIN 键锁定状态

ServiceKeyboard.getWinKeyStatus()

简要描述：获取当前键盘 WIN 键是否处于锁定状态。

### 参数

此方法不需要参数。

### 返回值

| 字段 | 类型 | 说明 | 示例 |
|------|------|------|------|
| locked | boolean | `true` 表示 WIN 键已锁定 | `true` |
| value | number | 协议值，`1` 表示锁定 | `1` |

### 使用示例

```javascript
const status = await ServiceKeyboard.getWinKeyStatus();
console.log('WIN 键锁定状态:', status.locked ? '已锁定' : '未锁定');
```

## 设置锁定 WIN 键

ServiceKeyboard.setWinKeyLock(locked)

简要描述：锁定或解锁键盘的 WIN 键。

### 参数

| 字段 | 类型 | 说明 | 必填 |
|------|------|------|------|
| locked | boolean \| number | `true` 或 `1` 表示锁定，`false` 或 `0` 表示解锁 | 是 |

### 返回值

| 字段 | 类型 | 说明 |
|------|------|------|
| locked | boolean | 设置后的锁定状态 |
| value | number | 协议值 |

### 使用示例

```javascript
// 锁定 WIN 键
await ServiceKeyboard.setWinKeyLock(true);

// 解锁 WIN 键
await ServiceKeyboard.setWinKeyLock(false);
```

### 协议说明

TIP

- 设置命令：`5C 03 00 93 20 value FF FF`，`value=1` 锁定，`value=0` 解锁
- 读取命令：`5C 03 00 93 20 FF FF`，响应第 7 字节为状态值

## 获取按键行程实时渲染开关

`ServiceKeyboard.getKeyTravelRender()`

调试器描述：获取按键行程状态实时渲染开关。协议功能码为 `0xC0`，`value=1` 表示开启，`value=0` 表示关闭。

### 参数

无。

### 返回值

返回 `Promise<{ code: number, key: string, value: number, enabled: boolean }>`。

- `value`: 协议值，`1` 表示开启，`0` 表示关闭。
- `enabled`: 是否已开启实时渲染。

### 使用示例

```javascript
const result = await ServiceKeyboard.getKeyTravelRender();
console.log('行程渲染开关:', result.enabled ? '已开启' : '已关闭');
```

## 设置按键行程实时渲染开关

`ServiceKeyboard.setKeyTravelRender(value)`

调试器描述：设置按键行程状态实时渲染开关。协议功能码为 `0xC0`，`value=1` 表示开启，`value=0` 表示关闭。

### 参数

| 参数 | 类型 | 描述 |
|------|------|------|
| `value` | `boolean \| string \| number` | `1`、`true` 或 `'on'` 开启；`0`、`false` 或 `'off'` 关闭。 |

### 返回值

返回 `Promise<{ code: number, key: string, value: number, enabled: boolean }>`。

### 使用示例

```javascript
await ServiceKeyboard.setKeyTravelRender(1);
await ServiceKeyboard.setKeyTravelRender(0);
await ServiceKeyboard.setKeyTravelRender('on');
```

更多 API 文档请参考其他页面。