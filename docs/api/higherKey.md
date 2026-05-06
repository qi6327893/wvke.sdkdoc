# 高级按键功能

## 概述

本章节介绍高级按键功能相关的 API，包含获取高级按键布局详情、删除高级键以及 DKS、MPT、MT、TGL、END、SOCD 等高级键配置接口。

## 获取高级按键布局详情

ServiceKeyboard.getAdvancedKeyLayout()

简要描述：读取当前键盘高级按键布局与详细配置列表。

### 参数

此方法不需要参数。

### 返回值

• 总体类型：`Promise<{ code: number, total: number, details: AdvancedKeyLayoutItem[], layout: AdvancedKeyLayoutItem[][] }>`
• 描述：返回高级按键总数、高级按键详情列表，以及完整键盘布局数据。若条目存在 `custom` 配置，则其中会额外包含 `image` 字段，表示该自定义功能对应的图标资源地址。高级功能详情会放在 `data` 与 `custom.data` 中，便于直接读取结构化配置。

### 字段说明

| 字段 | 含义 |
|------|------|
| code | 接口状态码，0 表示成功 |
| total | 当前识别到的高级按键数量 |
| details | 高级按键详情列表（按模式过滤后，`custom.image` 为自定义功能图标地址，`data` 为结构化详情） |
| layout | 当前键盘完整布局（含 working_mode 与 custom 信息；若存在 `custom`，则附带 `custom.image` 与 `custom.data`） |

### 高级功能详情

SOCD 条目的 `data` / `custom.data` 包含以下字段：

| 字段 | 含义 |
|------|------|
| mode | SOCD 模式值，`0` 后输入优先，`1` 绝对优先，`2` 中性模式 |
| delay | SOCD 延迟值 |
| keyName / keyName2 | 两个配对按键名称 |
| hid / hid2 | 两个配对按键 HID |
| keys | 配对按键列表 |

DKS 条目的 `data` / `custom.data` 包含以下字段：

| 字段 | 含义 |
|------|------|
| keyName / hid | 触发键名称与 HID |
| kcs | 4 个任务键 HID 数值 |
| trps | 4 个任务键的触发粒度数值 |
| dbs | DKS 附加行程数据 |
| tasks | 4 个任务键详情，每项包含 `keyName`、`hid`、`trps`、`trpsBits`、`trpsText` |

### 使用示例

```javascript
const advancedKeyLayout = await ServiceKeyboard.getAdvancedKeyLayout();
console.log('高级按键布局:', advancedKeyLayout);
```

## 删除高级键

ServiceKeyboard.delAdvancedKey(params)

简要描述：删除指定位置的高级键设置。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| keyName | string | 按键名称，例如 `A` | 是 |
| modeName | string | 模式名称，可选（例如 `SOCD`） | 否 |
| task | string | SOCD 配对按键，可使用 `A|B` 形式 | 否 |

### 使用示例

```javascript
await ServiceKeyboard.delAdvancedKey({
	keyName: 'A'
});
```

## 设置 DKS 高级键

ServiceKeyboard.setAdvancedKeyDKS(params)

简要描述：设置指定位置的 DKS 高级键配置。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| keyName | string | 按键名称，例如 `A` | 是 |
| task | string | 4 段任务串，格式为 `任务键1|7位触发串,任务键2|7位触发串,任务键3|7位触发串,任务键4|7位触发串` | 是 |

### trps7 位说明

每个任务段中的 7 位触发串，从左到右依次对应：

| 位序 | 含义 |
|------|------|
| 1 | 首次触发 |
| 2 | 下压过程 |
| 3 | 触底动作 |
| 4 | 触底按住 |
| 5 | 抬起动作 |
| 6 | 抬起过程 |
| 7 | 抬起完毕 |

例如 `1111111` 表示上述 7 个阶段全部启用；`1000000` 表示仅在首次触发时生效。

### 使用示例

```javascript
await ServiceKeyboard.setAdvancedKeyDKS({
	keyName: 'A',
	task: 'A|1|1|1|1|1|1|1,B|1|0|1|0|1|0|1,C|1|1|0|0|1|1|0,D|1|1|1|1|1|1|1'
});
```

## 设置 MPT 高级键

ServiceKeyboard.setAdvancedKeyMPT(params)

简要描述：设置指定位置的 MPT 高级键配置。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| keyName | string | 按键名称，例如 `A` | 是 |
| task | string | 格式为 `任务键1|行程1|任务键2|行程2|任务键3|行程3` | 是 |

### 使用示例

```javascript
await ServiceKeyboard.setAdvancedKeyMPT({
	keyName: 'A',
	task: 'A|0.5|B|1|C|1.5'
});
```

## 设置 MT 高级键

ServiceKeyboard.setAdvancedKeyMT(params)

简要描述：设置指定位置的 MT 高级键配置。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| keyName | string | 按键名称，例如 `A` | 是 |
| mode | string | 固定为 `MT` | 否 |
| time | number | 延迟时间，单位 ms | 是 |
| task | string | 格式为 `任务键1|任务键2|延迟ms`，例如 `B|C|200` | 是 |

### 使用示例

```javascript
await ServiceKeyboard.setAdvancedKeyMT({
	keyName: 'A',
	mode: 'MT',
	task: 'B|C|200'
});
```

## 设置 TGL 高级键

ServiceKeyboard.setAdvancedKeyTGL(params)

简要描述：设置指定位置的 TGL 高级键配置。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| keyName | string | 按键名称，例如 `A` | 是 |
| mode | string | 固定为 `TGL` | 否 |
| task | string | 任务按键名称，例如 `B` | 是 |
| delay | number | 延迟时间，单位 ms | 否 |

### 使用示例

```javascript
await ServiceKeyboard.setAdvancedKeyTGL({
	keyName: 'A',
	mode: 'TGL',
	task: 'B',
	delay: 200
});
```

## 设置 END 高级键

ServiceKeyboard.setAdvancedKeyEND(params)

简要描述：设置指定位置的 END 高级键配置。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| keyName | string | 按键名称，例如 `A` | 是 |
| task | string | 任务按键名称，例如 `B` | 是 |
| delay | number | 延迟时间，单位 ms | 否 |

### 使用示例

```javascript
await ServiceKeyboard.setAdvancedKeyEND({
	keyName: 'A',
	task: 'B',
	delay: 200
});
```

## 设置 SOCD 高级键

ServiceKeyboard.setAdvancedKeySOCD(params)

简要描述：为两个按键设置 SOCD 高级键配置。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| keyName | string | 第一个按键名称，例如 `A` | 是 |
| keyName2 | string | 第二个按键名称，例如 `B` | 是 |
| mode | number \| string | SOCD 工作模式，支持 `0/1/2` 或 `socdLaterInputPriority/socdAbsolutePriority/socdNeutral` | 是 |
| delay | number | 延迟时间，单位 ms | 是 |

### 使用示例

```javascript
await ServiceKeyboard.setAdvancedKeySOCD({
	keyName: 'A',
	keyName2: 'B',
	mode: 0,
	delay: 0
});
```

