# 万花筒灯效

## 概述

本页记录 SDK 调试器“机械轴键盘 → 万花筒灯效”分类中的 9 个接口，包含键盘背光、Logo 灯基础配置、自定义灯光和实时上报控制。

灯效模式和子模式应根据设备支持的灯效数据选择，再传给设置接口。

## 获取键盘背光基础配置

`ServiceKeyboardMK.getLightingBase()`

简要描述：读取键盘背光当前的主模式、子模式、亮度和速度。

### 参数

此方法不需要参数。

### 返回值

返回包含 `mode`、`childMode`、`luminance`、`speed` 及对应可读标签的对象。

```javascript
const lighting = await keyboard.getLightingBase();
console.log(lighting.mode, lighting.luminance);
```

## 设置键盘背光基础配置

`ServiceKeyboardMK.setLightingBase(params)`

简要描述：设置键盘背光基础模式、子模式、亮度和速度。

### 参数

| 参数 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| params | object | 灯光设置对象，通常通过 `data` 包含 `mode`、`childMode`、`luminance`、`speed`。 | 是 |

### 使用示例

```javascript
await keyboard.setLightingBase({
  data: {
    mode: 1,
    childMode: 1,
    luminance: 3,
    speed: 3,
  },
});
```

## 获取 Logo 灯基础配置

`ServiceKeyboardMK.getLightingLogoBase()`

简要描述：读取 Logo 灯的模式、亮度和速度配置。没有 Logo 灯的设备可以忽略此接口。

### 参数

此方法不需要参数。

```javascript
const logo = await keyboard.getLightingLogoBase();
```

## 设置 Logo 灯基础配置

`ServiceKeyboardMK.setLightingLogoBase(params)`

简要描述：设置 Logo 灯的基础灯光配置。

### 参数

| 参数 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| params | object | Logo 灯配置，字段结构以设备支持的灯光模式为准。 | 是 |

```javascript
await keyboard.setLightingLogoBase({
  data: { mode: 1, luminance: 3, speed: 3 },
});
```

## 获取键盘自定义背光

`ServiceKeyboardMK.getKeyboardBacklightCustom()`

简要描述：读取键盘按键自定义背光数据，可用于恢复或编辑当前自定义灯光布局。

### 参数

此方法不需要参数。

### 返回值

返回自定义背光数据和布局信息；具体矩阵尺寸由设备键盘布局决定。

```javascript
const custom = await keyboard.getKeyboardBacklightCustom();
```

## 设置键盘自定义背光

`ServiceKeyboardMK.setKeyboardBacklightCustom(params)`

简要描述：写入键盘按键自定义背光颜色。

### 参数

| 参数 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| params | object | 自定义灯光数据，通常通过 `data` 传入 HID、红、绿、蓝数据。 | 是 |

```javascript
await keyboard.setKeyboardBacklightCustom({
  data: [['04', 'ff', '00', '00']],
});
```

## 获取 Logo 自定义灯光

`ServiceKeyboardMK.getLightingLogoCustom()`

简要描述：读取 Logo 灯自定义灯光数据。

### 参数

此方法不需要参数。

```javascript
const custom = await keyboard.getLightingLogoCustom();
```

## 设置 Logo 自定义灯光

`ServiceKeyboardMK.setLightingLogoCustom(params)`

简要描述：写入 Logo 灯自定义灯光数据。

### 参数

| 参数 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| params | object | Logo 自定义灯光数据。 | 是 |

```javascript
await keyboard.setLightingLogoCustom({
  data: [['00', '00', 'ff']],
});
```

## 停止灯光实时上报

`ServiceKeyboardMK.stopLightingRealtimeReport(options?)`

简要描述：停止当前自定义灯光实时上报，并清理 SDK 本地实时渲染状态。

### 参数

| 参数 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| options | object | 可选停止配置。 | 否 |

```javascript
await keyboard.stopLightingRealtimeReport();
```
