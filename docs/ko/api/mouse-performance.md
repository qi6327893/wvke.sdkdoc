# Mouse Performance

## Polling Rate

Read the supported polling-rate values and the current value with these APIs:

```javascript
const rates = await mouse.getRateOfReturnList();
const current = await mouse.getRateOfReturn();
console.log(rates.list, current.key);
```

Set the polling rate with its string key:

```javascript
await mouse.setRateOfReturn('1000Hz');
```

## Lift-off Distance

```javascript
const currentLiftOff = await mouse.getLiftOffDistance();
await mouse.setLiftOffDistance(1);
```

The returned value is measured in millimetres by the device. Supported levels depend on the mouse firmware.
