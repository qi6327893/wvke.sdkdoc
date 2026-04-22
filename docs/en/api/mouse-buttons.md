# Button Mapping

## Overview

Button APIs control button remapping, function assignments, and macro binding.

## Common APIs

- `ServiceMouse.getButtons()`
- `ServiceMouse.setButtons(config)`

## Example

```javascript
const buttons = await ServiceMouse.getButtons();
await ServiceMouse.setButtons(buttons);
```