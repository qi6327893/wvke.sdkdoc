# DPI设置

## 获取DPI列表

```javascript
const dpiList = await mouse.getDPIList();
console.log(dpiList);
```

返回示例：
```json
{
  "list": [400, 800, 1600, 3200, 6400, 12800, 16000]
}
```

## 获取当前DPI

```javascript
const currentDPI = await mouse.getDPI();
console.log(currentDPI);
```

返回示例：
```json
{
  "value": 1600
}
```

## 设置DPI

```javascript
await mouse.setDPI(3200);
console.log('DPI设置为3200');
```
