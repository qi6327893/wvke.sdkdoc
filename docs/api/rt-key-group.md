# RT 按键编组

## 概述

设置和获取 RT（Rapid Trigger）按键编组。协议功能码 `0xFE`，格式为 `HID + 组号`（如 `04,00` 表示 HID=0x04 的按键属于 0 组）。默认全局所有按键都是编号 0 组，支持单键和批量模式。

## 获取 RT 按键编组

keyboard.getRtKeyGroup()

简要描述: 获取整键盘所有按键的 RT 编组信息。

### 参数

此方法不需要参数。

### 返回值

| 字段 | 类型 | 描述 |
|------|------|------|
| code | number | 状态码，`0` 表示成功 |
| total | number | 有效按键总数 |
| details | `Array<{ row, col, keyName, hid, group }>` | 每个按键的 RT 编组明细 |
| layout | `Array<Array<{ row, col, keyName, hid, group }>>` | 按键盘矩阵布局的编组信息 |

### 使用示例

```javascript
const result = await keyboard.getRtKeyGroup();
console.log('RT 编组总数:', result.total);
result.details.forEach(key => {
  console.log(`${key.keyName} → 组${key.group}`);
});
```

## 设置 RT 按键编组

keyboard.setRtKeyGroup(params)

简要描述: 设置按键的 RT 编组编号。支持单键和批量模式。

### 参数

| 参数 | 类型 | 描述 |
|------|------|------|
| keyName | `string` | 按键名称，如 `'A'`（单键模式） |
| hid | `string` | 按键 HID，如 `'0x04'` |
| group | `number` | RT 编组编号，默认 `0` |
| keys | `Array<{ keyName?, hid?, group? }>` | 批量模式：按键数组，每个元素可指定独立 group |

### 返回值

单键模式：

| 字段 | 类型 | 描述 |
|------|------|------|
| code | number | 状态码 |
| keyName | string | 按键名称 |
| hid | string | 按键 HID |
| group | number | 设置的编组编号 |

批量模式：

| 字段 | 类型 | 描述 |
|------|------|------|
| code | number | 状态码 |
| batch | boolean | `true` |
| total | number | 设置的按键总数 |
| details | `Array<{ keyName, hid, group }>` | 每个按键的设置结果 |

### 使用示例

```javascript
// 单键模式：将 A 键设置为 RT 编组 1
await keyboard.setRtKeyGroup({ keyName: 'A', group: 1 });

// 批量模式：将 A、S、D 设置为 RT 编组 2
await keyboard.setRtKeyGroup({
  keys: [
    { keyName: 'A', group: 2 },
    { keyName: 'S', group: 2 },
    { keyName: 'D', group: 2 }
  ]
});

// 批量模式：统一下发 group
await keyboard.setRtKeyGroup({
  keys: [{ keyName: 'A' }, { keyName: 'D' }],
  group: 1
});
```
