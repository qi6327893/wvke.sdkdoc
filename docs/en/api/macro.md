# Macro

## Overview

Macro APIs let you create, edit, and assign macro sequences to keys/buttons.

## Common APIs

- `ServiceKeyboard.getMacroList()`
- `ServiceKeyboard.setMacro(index, macroConfig)`

## Example

```javascript
const macros = await ServiceKeyboard.getMacroList();
await ServiceKeyboard.setMacro(0, macros[0]);
```

## Notes

- Keep macro step counts within firmware limits.
- Validate delays and key events before uploading.