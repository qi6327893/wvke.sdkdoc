# Mouse Device Information

## Overview

Mouse info APIs provide connection state and basic hardware details.

## Common APIs

- `mouse.getDevices()`
- `mouse.init(id)`
- `mouse.getDevicesInfo()`

## Example

```javascript
import { ServiceMouse } from 'wvke-sdk';

const mouse = new ServiceMouse();
const devices = await mouse.getDevices();

if (devices.length === 0) {
	throw new Error('No mouse devices found');
}

await mouse.init(devices[0].id);
const info = await mouse.getDevicesInfo();
console.log(info);
```