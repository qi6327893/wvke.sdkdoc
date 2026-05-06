# Advanced Keys

## Overview

This section covers advanced key APIs, including reading advanced-key layout details, deleting advanced keys, and configuring DKS, MPT, MT, TGL, END, and SOCD advanced key behaviors.

## Get Advanced Key Layout Details

ServiceKeyboard.getAdvancedKeyLayout()

Brief: Reads the current advanced-key layout and detailed configuration list.

### Parameters

This method does not require parameters.

### Returns

- Overall type: `Promise<{ code: number, total: number, details: AdvancedKeyLayoutItem[], layout: AdvancedKeyLayoutItem[][] }>`
- Description: Returns total advanced keys, the advanced-key detail list, and the full keyboard layout data. If an item contains `custom` configuration, that object now also includes an `image` field with the icon resource URL. Advanced-key details are exposed on both `data` and `custom.data` for direct structured access.

### Fields

| Field | Meaning |
|------|------|
| code | API status code, 0 means success |
| total | Number of advanced keys detected |
| details | Advanced-key detail list (filtered by mode, with `custom.image` and structured `data` when custom config exists) |
| layout | Full keyboard layout including working_mode and custom info; if `custom` exists, it also carries `custom.image` and `custom.data` |

### Advanced Detail Data

SOCD items expose these fields on `data` / `custom.data`:

| Field | Meaning |
|------|------|
| mode | SOCD mode value: `0` later-input priority, `1` absolute priority, `2` neutral |
| delay | SOCD delay value |
| keyName / keyName2 | The paired key names |
| hid / hid2 | The paired key HID values |
| keys | Paired key list |

DKS items expose these fields on `data` / `custom.data`:

| Field | Meaning |
|------|------|
| keyName / hid | Trigger key name and HID |
| kcs | Four task-key HID values |
| trps | Four task trigger granularity values |
| dbs | Additional DKS travel data |
| tasks | Four task details, each with `keyName`, `hid`, `trps`, `trpsBits`, and `trpsText` |

### Example

```javascript
const advancedKeyLayout = await ServiceKeyboard.getAdvancedKeyLayout();
console.log('Advanced key layout:', advancedKeyLayout);
```

## Delete Advanced Key

ServiceKeyboard.delAdvancedKey(params)

Brief: Deletes the advanced key setting for the specified key name.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| keyName | string | Key name, e.g. `A` | Yes |
| modeName | string | Optional mode name, e.g. `SOCD` | No |
| task | string | Optional paired key for SOCD, supports `A|B` format | No |

### Example

```javascript
await ServiceKeyboard.delAdvancedKey({
	keyName: 'A'
});
```

## Set DKS Advanced Key

ServiceKeyboard.setAdvancedKeyDKS(params)

Brief: Sets a DKS advanced key configuration for the specified key name.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| keyName | string | Key name, e.g. `A` | Yes |
| task | string | Four task segments in the format `task1|7-bit-trigger-string,task2|7-bit-trigger-string,task3|7-bit-trigger-string,task4|7-bit-trigger-string` | Yes |

### trps 7-bit Mapping

For each task segment, the 7-bit trigger string is ordered from left to right as follows:

| Bit Position | Meaning |
|------|------|
| 1 | First trigger |
| 2 | Press travel |
| 3 | Bottom action |
| 4 | Bottom hold |
| 5 | Release action |
| 6 | Release travel |
| 7 | Release complete |

For example, `1111111` means all seven stages are enabled, while `1000000` means the task only fires on the first trigger.

### Example

```javascript
await ServiceKeyboard.setAdvancedKeyDKS({
	keyName: 'A',
	task: 'A|1|1|1|1|1|1|1,B|1|0|1|0|1|0|1,C|1|1|0|0|1|1|0,D|1|1|1|1|1|1|1'
});
```

## Set MPT Advanced Key

ServiceKeyboard.setAdvancedKeyMPT(params)

Brief: Sets an MPT advanced key configuration for the specified key name.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| keyName | string | Key name, e.g. `A` | Yes |
| task | string | `task1|journey1|task2|journey2|task3|journey3` | Yes |

### Example

```javascript
await ServiceKeyboard.setAdvancedKeyMPT({
	keyName: 'A',
	task: 'A|0.5|B|1|C|1.5'
});
```

## Set MT Advanced Key

ServiceKeyboard.setAdvancedKeyMT(params)

Brief: Sets an MT advanced key configuration for the specified key name.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| keyName | string | Key name, e.g. `A` | Yes |
| mode | string | Fixed as `MT` | No |
| time | number | Delay time in ms | Yes |
| task | string | `task1|task2|delayMs`, for example `B|C|200` | Yes |

### Example

```javascript
await ServiceKeyboard.setAdvancedKeyMT({
	keyName: 'A',
	mode: 'MT',
	task: 'B|C|200'
});
```

## Set TGL Advanced Key

ServiceKeyboard.setAdvancedKeyTGL(params)

Brief: Sets a TGL advanced key configuration for the specified key name.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| keyName | string | Key name, e.g. `A` | Yes |
| mode | string | Fixed as `TGL` | No |
| task | string | Task key name, e.g. `B` | Yes |
| delay | number | Delay time in ms | No |

### Example

```javascript
await ServiceKeyboard.setAdvancedKeyTGL({
	keyName: 'A',
	mode: 'TGL',
	task: 'B',
	delay: 200
});
```

## Set END Advanced Key

ServiceKeyboard.setAdvancedKeyEND(params)

Brief: Sets an END advanced key configuration for the specified key name.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| keyName | string | Key name, e.g. `A` | Yes |
| task | string | Task key name, e.g. `B` | Yes |
| delay | number | Delay time in ms | No |

### Example

```javascript
await ServiceKeyboard.setAdvancedKeyEND({
	keyName: 'A',
	task: 'B',
	delay: 200
});
```

## Set SOCD Advanced Key

ServiceKeyboard.setAdvancedKeySOCD(params)

Brief: Sets an SOCD advanced key configuration for two related keys.

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| keyName | string | First key name, e.g. `A` | Yes |
| keyName2 | string | Second key name, e.g. `B` | Yes |
| mode | number \| string | SOCD mode, supports `0/1/2` or `socdLaterInputPriority/socdAbsolutePriority/socdNeutral` | Yes |
| delay | number | Delay time in ms | Yes |

### Example

```javascript
await ServiceKeyboard.setAdvancedKeySOCD({
	keyName: 'A',
	keyName2: 'B',
	mode: 0,
	delay: 0
});
```

