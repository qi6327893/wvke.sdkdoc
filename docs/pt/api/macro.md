# Macro

## Overview

Macro functions are represented as `type: "MACRO"` entries in a keyboard custom layer. Read the layer with `getKeyCustomLayer()` and assign a macro function with `setKeyConfig()`.

## Read Macro Functions

```javascript
const result = await keyboard.getKeyCustomLayer(0x01);
const macroKeys = result.list.filter(item => item.type === 'MACRO');
```

## Assign a Macro Function

```javascript
await keyboard.setKeyConfig('A', 0x01, {
	id: '0xA1',
	type: '0x06'
});
```