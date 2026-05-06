# DPI Settings

## Overview

DPI APIs are used to read and update DPI levels and sensitivity profiles.

## Common APIs

- `ServiceMouse.getDPI()`
- `ServiceMouse.setDPI(config)`

## Example

```javascript
const dpi = await ServiceMouse.getDPI();
dpi.value = 1600;
await ServiceMouse.setDPI(dpi);
```