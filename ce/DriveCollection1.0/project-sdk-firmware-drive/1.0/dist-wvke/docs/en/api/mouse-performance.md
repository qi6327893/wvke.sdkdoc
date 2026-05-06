# Mouse Performance

## Overview

Performance APIs configure polling rate and lift-off distance.

## Common APIs

- `ServiceMouse.getPerformance()`
- `ServiceMouse.setPerformance(config)`

## Example

```javascript
const perf = await ServiceMouse.getPerformance();
perf.pollingRate = 1000;
await ServiceMouse.setPerformance(perf);
```

## Lift-off Distance

Typical levels:

- `1`: 1mm (esports)
- `2`: 2mm (balanced)
- `3`: 3mm (office/general)
