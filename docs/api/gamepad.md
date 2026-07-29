# 手柄功能

## 概述

本章节介绍当前 SDK 中与手柄模式、映射设置、曲线选项和线性曲线相关的 API。

## 获取手柄模式

ServiceKeyboard.getGamepadMode()

简要描述: 获取当前手柄模式。

### 参数

此方法不需要参数。

### 返回值

• 总体类型: `Promise<{ code: number, key: string, value: number }>`
• 描述: 返回一个 `Promise`，解析为当前手柄模式结果。

| 字段 | 类型 | 描述 | 示例 |
|------|------|------|------|
| code | number | 接口调用状态码，`0` 表示成功。 | 0 |
| key | string | 当前手柄模式描述。 | "Xbox手柄模式" |
| value | number | 当前协议值。`0`=纯键盘模式，`1`=Xbox手柄模式，`2`=经典手柄模式。 | 1 |

### 使用示例

```javascript
async function fetchGamepadMode() {
  try {
    const result = await ServiceKeyboard.getGamepadMode();
    console.log('当前手柄模式:', result);
  } catch (error) {
    console.error('获取手柄模式失败:', error);
  }
}
```

## 设置手柄模式

ServiceKeyboard.setGamepadMode(value)

简要描述: 设置当前手柄模式。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| value | string \| number | 手柄模式值。支持 `0/1/2`，也支持 `纯键盘模式`、`Xbox手柄模式`、`经典手柄模式` 等写法。 | 是 |

### 返回值

• 总体类型: `Promise<{ code: number, key: string, value: number }>`
• 描述: 返回一个 `Promise`，设置成功时解析为当前生效的手柄模式状态。

### 模式枚举

| 协议值 | 含义 |
|------|------|
| 0 | 纯键盘模式 |
| 1 | Xbox手柄模式 |
| 2 | 经典手柄模式 |

### 使用示例

```javascript
async function updateGamepadMode(value) {
  try {
    const result = await ServiceKeyboard.setGamepadMode(value);
    console.log('设置手柄模式结果:', result);
  } catch (error) {
    console.error('设置手柄模式失败:', error);
  }
}

// updateGamepadMode(0);
// updateGamepadMode(1);
// updateGamepadMode(2);
```

## 手柄测试

调试工具右下角的测试浮窗支持实时手柄功能测试：

- **模式识别**：通过 `getGamepadMode()` 自动识别当前设备模式（0=纯键盘 / 1=XBOX / 2=经典手柄），并在浮窗徽标中显示。
- **经典手柄**：动态列出浏览器暴露的全部轴（轴 3~N），支持 Z 轴/X 旋转/Y 旋转/Z 旋转/油门语义标签，油门使用 0→1 单向映射自动归零。按钮按原始编号显示，与系统测试界面完全对齐。
- **XBOX 手柄**：显示 A/B/X/Y/LB/RB/Back/Start/LS/RS 按钮和 LT/RT 触发器，Z 轴渲染方向为 LT - RT。
- **POV 方向帽**：支持按键式和单轴 Hat 两种编码自动识别。

测试数据通过 `navigator.getGamepads()` 轮询获取，不依赖设备协议，切换到手柄测试标签页即可实时查看。

## 获取手柄映射设置

ServiceKeyboard.getGamepadMappingSettings()

简要描述: 获取当前手柄映射设置。

### 参数

此方法不需要参数。

### 返回值

• 总体类型: `Promise<{ code: number, enableMappedKeyboardKeys: number, disableMappedKeyInput: number }>`
• 描述: 返回一个 `Promise`，解析为当前手柄映射设置。

| 字段 | 类型 | 描述 | 示例 |
|------|------|------|------|
| code | number | 接口调用状态码，`0` 表示成功。 | 0 |
| enableMappedKeyboardKeys | number | 是否启用映射键盘按键。`0` 表示关闭，`1` 表示启用。 | 1 |
| disableMappedKeyInput | number | 是否禁用映射按键的原始键盘输入。`0` 表示不禁用，`1` 表示禁用。 | 0 |

### 使用示例

```javascript
async function fetchGamepadMappingSettings() {
  try {
    const result = await ServiceKeyboard.getGamepadMappingSettings();
    console.log('当前手柄映射设置:', result);
  } catch (error) {
    console.error('获取手柄映射设置失败:', error);
  }
}
```

## 设置手柄映射设置

ServiceKeyboard.setGamepadMappingSettings(settings)

简要描述: 设置当前手柄映射设置。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| settings.enableMappedKeyboardKeys | string \| number \| boolean | 是否启用映射键盘按键。 | 是 |
| settings.disableMappedKeyInput | string \| number \| boolean | 是否禁用映射按键的原始键盘输入。 | 是 |

### 返回值

• 总体类型: `Promise<{ code: number, enableMappedKeyboardKeys: number, disableMappedKeyInput: number }>`

### 使用示例

```javascript
async function updateGamepadMappingSettings() {
  try {
    const result = await ServiceKeyboard.setGamepadMappingSettings({
      enableMappedKeyboardKeys: 1,
      disableMappedKeyInput: 0,
    });
    console.log('设置手柄映射结果:', result);
  } catch (error) {
    console.error('设置手柄映射失败:', error);
  }
}
```

## 获取手柄曲线选项

ServiceKeyboard.getGamepadCurveOptions()

简要描述: 获取当前手柄曲线选项。

### 参数

此方法不需要参数。

### 返回值

• 总体类型: `Promise<{ code: number, enableAngleAdjustment: number, enableSquareStickOutput: number, enableExtremePriority: number, angleAdjustmentValue: number }>`
• 描述: 返回一个 `Promise`，解析为当前手柄曲线选项。

| 字段 | 类型 | 描述 | 示例 |
|------|------|------|------|
| code | number | 接口调用状态码，`0` 表示成功。 | 0 |
| enableAngleAdjustment | number | 是否启用角度调整。`0` 表示关闭，`1` 表示启用。 | 0 |
| enableSquareStickOutput | number | 是否启用方档摇杆输出。`0` 表示关闭，`1` 表示启用。 | 1 |
| enableExtremePriority | number | 是否启用极值优先。`0` 表示关闭，`1` 表示启用。 | 0 |
| angleAdjustmentValue | number | 角度调整值，单位度；旧固件未返回该字段时默认为 `45`。 | 45 |

### 使用示例

```javascript
async function fetchGamepadCurveOptions() {
  try {
    const result = await ServiceKeyboard.getGamepadCurveOptions();
    console.log('当前手柄曲线选项:', result);
  } catch (error) {
    console.error('获取手柄曲线选项失败:', error);
  }
}
```

## 设置手柄曲线选项

ServiceKeyboard.setGamepadCurveOptions(settings)

简要描述: 设置当前手柄曲线选项。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| settings.enableAngleAdjustment | string \| number \| boolean | 是否启用角度调整。 | 是 |
| settings.angleAdjustmentValue | string \| number | 角度调整值，单位度；不传时默认 `45`，支持 `0` 到 `90`。 | 否 |
| settings.enableSquareStickOutput | string \| number \| boolean | 是否启用方档摇杆输出。 | 是 |
| settings.enableExtremePriority | string \| number \| boolean | 是否启用极值优先。 | 是 |

### 返回值

• 总体类型: `Promise<{ code: number, enableAngleAdjustment: number, enableSquareStickOutput: number, enableExtremePriority: number, angleAdjustmentValue: number }>`

### 使用示例

```javascript
async function updateGamepadCurveOptions() {
  try {
    const result = await ServiceKeyboard.setGamepadCurveOptions({
      enableAngleAdjustment: 0,
      angleAdjustmentValue: 45,
      enableSquareStickOutput: 1,
      enableExtremePriority: 0,
    });
    console.log('设置手柄曲线选项结果:', result);
  } catch (error) {
    console.error('设置手柄曲线选项失败:', error);
  }
}
```

## 获取手柄线性曲线

ServiceKeyboard.getGamepadLinearCurve()

简要描述: 获取当前手柄线性曲线，返回 A、B、C、D 四个曲线点的行程值和响应精度值。

### 参数

此方法不需要参数。

### 返回值

• 总体类型: `Promise<{ code: number, pointA: number, pointAValue: number, pointB: number, pointBValue: number, pointC: number, pointCValue: number, pointD: number, pointDValue: number }>`
• 描述: 返回一个 `Promise`，解析为当前手柄线性曲线数据。

| 字段 | 类型 | 描述 | 示例 |
|------|------|------|------|
| code | number | 接口调用状态码，`0` 表示成功。 | 0 |
| pointA | number | A 点，单位 mm。 | 0.1 |
| pointAValue | number | A 点响应精度值，范围 0 到 255。 | 20 |
| pointB | number | B 点，单位 mm。 | 0.2 |
| pointBValue | number | B 点响应精度值，范围 0 到 255。 | 60 |
| pointC | number | C 点，单位 mm。 | 3.0 |
| pointCValue | number | C 点响应精度值，范围 0 到 255。 | 200 |
| pointD | number | D 点，单位 mm。 | 3.3 |
| pointDValue | number | D 点响应精度值，范围 0 到 255。 | 255 |

### 使用示例

```javascript
async function fetchGamepadLinearCurve() {
  try {
    const result = await ServiceKeyboard.getGamepadLinearCurve();
    console.log('当前手柄线性曲线:', result);
  } catch (error) {
    console.error('获取手柄线性曲线失败:', error);
  }
}
```

## 设置手柄线性曲线

ServiceKeyboard.setGamepadLinearCurve(curve)

简要描述: 设置当前手柄线性曲线，传入 A、B、C、D 四个曲线点的行程值和响应精度值。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| curve.pointA | string \| number | A 点，单位 mm。 | 是 |
| curve.pointAValue | string \| number | A 点响应精度值，范围 0 到 255，默认 20。 | 否 |
| curve.pointB | string \| number | B 点，单位 mm。 | 是 |
| curve.pointBValue | string \| number | B 点响应精度值，范围 0 到 255，默认 60。 | 否 |
| curve.pointC | string \| number | C 点，单位 mm。 | 是 |
| curve.pointCValue | string \| number | C 点响应精度值，范围 0 到 255，默认 200。 | 否 |
| curve.pointD | string \| number | D 点，单位 mm。 | 是 |
| curve.pointDValue | string \| number | D 点响应精度值，范围 0 到 255，默认 255。 | 否 |

### 返回值

• 总体类型: `Promise<{ code: number, pointA: number, pointAValue: number, pointB: number, pointBValue: number, pointC: number, pointCValue: number, pointD: number, pointDValue: number }>`

### 使用示例

```javascript
async function updateGamepadLinearCurve() {
  try {
    const result = await ServiceKeyboard.setGamepadLinearCurve({
      pointA: 0.1,
      pointAValue: 20,
      pointB: 0.2,
      pointBValue: 60,
      pointC: 3.0,
      pointCValue: 200,
      pointD: 3.3,
      pointDValue: 255,
    });
    console.log('设置手柄线性曲线结果:', result);
  } catch (error) {
    console.error('设置手柄线性曲线失败:', error);
  }
}
```
