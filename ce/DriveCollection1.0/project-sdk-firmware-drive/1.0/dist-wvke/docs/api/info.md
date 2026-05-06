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
| ConnectMode | number | 连接模式 | 16 |
| CheckNum | string | 校验值 | "00000000" |

示例返回结构：

```json
{
  "BoardID": 1174538260,
  "FWsize": 2,
  "RunMode": 0,
  "SN": "2493C45353E0A336",
  "Version": "004",
  "ConnectMode": 16,
  "CheckNum": "00000000"
}
```
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

### 参数

此方法不需要参数。

### 返回值

• 总体类型: `Promise<{ code: number, key: string }>`
• 描述: 返回一个 `Promise`，该 `Promise` 解析为当前回报率信息。
• 解析对象结构:

| 字段 | 类型 | 描述 | 示例 |
|------|------|------|------|
| code | number | 接口调用状态码，`0` 表示成功。 | 0 |
| key | string | 当前回报率显示值。 | "1000Hz" |

协议值映射关系：

| 协议值 | 回报率 |
|------|------|
| 1 | 125Hz |
| 2 | 250Hz |
| 3 | 500Hz |
| 4 | 1000Hz |
| 5 | 2000Hz |
| 6 | 4000Hz |
| 7 | 8000Hz |

### 使用示例

```javascript
async function fetchRateOfReturn() {
  try {
    const rateInfo = await ServiceKeyboard.getRateOfReturn();
    console.log('当前回报率:', rateInfo);
    // 示例输出: { code: 0, key: '1000Hz' }
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
| value | string \| number | 回报率值。支持协议值 `1` 到 `7`，也支持 `125Hz`、`250Hz`、`500Hz`、`1000Hz`、`2000Hz`、`4000Hz`、`8000Hz`，以及 `1KHz`、`2KHz`、`4KHz`、`8KHz` 这类写法。 | 是 |

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
// updateRateOfReturn(4);

// 使用显示值
// updateRateOfReturn('1000Hz');

// 使用 KHz 写法
// updateRateOfReturn('8KHz');
```

### 注意事项

TIP

• 键盘固件协议中，回报率使用 `1` 到 `7` 表示不同档位。
• SDK 会自动把常见字符串输入转换为对应协议值。
• 设置回报率后，建议再次调用 `ServiceKeyboard.getRateOfReturn()` 进行确认。

## 开启主动上报全局监听

ServiceKeyboard.startGlobalReportListener(options)

简要描述: 监听键盘主动上报的配置切换、键盘背光基础参数变化和 Logo 灯基础参数变化。

`startGlobalReportListener()` 的返回值只表示监听是否已开启；实时上报数据通过 `on('keyboardGlobalReport', callback)` 或下方具体事件回调获取。

### 参数

| 参数 | 类型 | 描述 | 是否必需 |
|------|------|------|------|
| options | object | 可选配置。`includeCommandResponses` 为 `true` 时也会派发 SDK 主动发命令产生的响应；默认 `false`。 | 否 |

### 事件

| 事件名 | 描述 |
|------|------|
| keyboardGlobalReport | 所有已识别的全局主动上报。 |
| keyboardConfigChange | 配置槽位切换上报，包含 `configIndex`、`configName`、`value`。 |
| keyboardLightingChange | 键盘背光基础参数上报，包含 `mode`、`childMode`、`luminance`、`speed`、`sleep`。 |
| keyboardLogoLightingChange | Logo 灯基础参数上报，包含 `mode`、`luminance`、`speed`、`sleep`。 |

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

ServiceKeyboard.startGlobalReportListener();

// 不再需要实时数据时关闭监听，并移除业务侧回调。
ServiceKeyboard.stopGlobalReportListener();
ServiceKeyboard.off('keyboardGlobalReport', handleGlobalReport);
```

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

## 关闭主动上报全局监听

ServiceKeyboard.stopGlobalReportListener()

简要描述: 关闭通过 `startGlobalReportListener()` 开启的键盘主动上报全局监听。

### 使用示例

```javascript
ServiceKeyboard.stopGlobalReportListener();
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

## 其他 API

更多 API 文档请参考其他页面。