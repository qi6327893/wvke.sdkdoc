# 性能设置

## 获取回报率列表

```javascript
const rateList = await mouse.getRateOfReturnList();
console.log(rateList);
```

返回示例：
```json
{
  "list": ["125Hz", "250Hz", "500Hz", "1000Hz"]
}
```

## 获取当前回报率

```javascript
const currentRate = await mouse.getRateOfReturn();
console.log(currentRate);
```

返回示例：
```json
{
  "key": "1000Hz"
}
```

## 设置回报率

```javascript
await mouse.setRateOfReturn("1000Hz");
console.log('回报率设置为1000Hz');
```

## 获取抬升高度

```javascript
const liftOff = await mouse.getLiftOffDistance();
console.log(liftOff);
```

返回示例：
```json
{
  "value": 2
}
```

## 设置抬升高度

```javascript
await mouse.setLiftOffDistance(1);
console.log('抬升高度设置为1mm');
```

### 抬升高度说明

- 1: 1mm (适合游戏玩家)
- 2: 2mm (标准设置)
- 3: 3mm (适合办公使用)
