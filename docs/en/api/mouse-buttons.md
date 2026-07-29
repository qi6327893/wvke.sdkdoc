# Button Mapping

## Overview

Use the mouse button mapping APIs to read and update the mapping for the left, right, middle, DPI, forward, and backward buttons.

## Read the Mapping

```javascript
const mapping = await mouse.getButtonMapping();
console.log(mapping);
```

## Update the Mapping

```javascript
await mouse.setButtonMapping({
	left: 'left-click',
	right: 'right-click',
	middle: 'middle-click',
	dpi: 'dpi-cycle',
	forward: 'forward',
	backward: 'backward'
});
```