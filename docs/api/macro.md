# 宏

## 概述

本章节介绍键盘宏功能相关的API。

## 获取宏列表

ServiceKeyboard.getMacros()

简要描述: 获取已配置的宏列表。

### 参数

此方法不需要参数。

### 返回值

• 总体类型: `Promise<Macro[]>`
• 描述: 返回宏数组

### 使用示例

```javascript
const macros = await ServiceKeyboard.getMacros();
console.log('宏列表:', macros);
```

## 设置宏

ServiceKeyboard.setMacro()

简要描述: 配置键盘宏。

### 参数

| 字段 | 类型 | 描述 | 是否必需 |
|------|------|------|----------|
| index | number | 宏索引 | 是 |
| macro | Macro | 宏配置 | 是 |

### 使用示例

```javascript
await ServiceKeyboard.setMacro(0, macroConfig);
```