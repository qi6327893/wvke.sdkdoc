# Cloud API

## Overview

Cloud APIs are used for open-platform operation capabilities and cloud data exchange. They are suitable for driver clients, web apps, or business backends that need to connect accounts, devices, configuration sync, operational content, and related services.

This category currently acts as the entry point for cloud capabilities. Specific APIs can be added here by business module later.

## Tool APIs

- Cloud queries for device and product information.
- Cloud sync for driver-client settings, profiles, or user data.
- Reading operational content such as campaigns, assets, and notices.
- Data APIs related to the open-platform business backend.

## Recommendations

TIP

- Cloud APIs usually depend on service endpoints, authentication, and business permissions. Confirm the available scope before integration.
- Clients should only store necessary session data. Sensitive secrets should be handled on the server side.
- For real-time hardware communication, start with the keyboard, mouse, and firmware-upgrade API categories.

## Generate SN

CloudService.generateSn(options)

Brief description: Generate a range of SN strings based on start index and count (or end index).

### Parameters

| Field | Type | Description | Required |
|------|------|------|----------|
| options | object | Generation options. | No |

`options` properties:

| Field | Type | Default | Description |
|------|------|--------|------|
| format | string | `"0001"` | Format template, use `0` for digit placeholders. Set to `"weekly"` to enable weekly SN format. |
| start | number | `1` | Starting serial number. |
| end | number | — | End serial number (inclusive), alternative to `count`. |
| count | number | `1` | Number of SNs to generate, alternative to `end`. |
| maxLength | number | `20` | Maximum output length in ASCII bytes. |
| weekly | boolean | — | Set to `true` to enable weekly SN format (mutually exclusive with `format`). |
| manufacturer | string | `"C"` | Manufacturer code (weekly only). `C`=CIY, `S`=Sunrex. |
| sku | string | `"K1"` | SKU/PN code (weekly only). `K1` maps to part `K001`, `M1` maps to part `M001`. |
| year | number | current | Calendar year (weekly only). |
| week | number | current | ISO week number 1–53 (weekly only). |

### Return Value

• Overall type: `string[]`
• Description: Returns an array of generated SN strings.

### Basic Format Examples

```javascript
import { CloudService } from 'wvke-sdk';

// Default: generate 1 SN starting from 0001
CloudService.generateSn();                          // ['0001']

// Specify start and count
CloudService.generateSn({ start: 1, count: 5 });
// ['0001', '0002', '0003', '0004', '0005']

// Specify start and end
CloudService.generateSn({ format: 'SN-0000', start: 10, end: 12 });
// ['SN-0010', 'SN-0011', 'SN-0012']
```

### Weekly SN Format

The weekly SN format is suitable for week-based product identification. The output follows this structure:

```text
C M1 39 89 0001
│ │  │  │  └─ Unique consecutive ID (4 digits)
│ │  │  └─ Week code
│ │  └─ Year code
│ └─ SKU/PN code
└─ Manufacturer code
```

#### Unique Consecutive ID

Uses a 4-digit decimal counter from `0000` to `9999`. If the 0000–9999 capacity is insufficient within one week, a base-36 encoding segment is used, ranging from `AAAA` to `ZZZZ` (0–9 + A–Z).

Sequence example: `0001~9999`, `A001~Z999`, `AA00~ZZ99`…

#### Week Code

Values from 01 to 53, taken from the standard ISO calendar week number plus a fixed offset of 37.

Example: calendar week 52 encodes to `89` (52+37=89), calendar week 01 encodes to `38`.

#### Year Code

Last two digits of the calendar year plus a fixed offset of 13.

Example: year 2026 encodes to `39` (26+13=39), year 2027 encodes to `40`.

#### SKU / Part Number

`M1` maps to part `M001`, `K1` maps to part `K001`. Other SKU codes are TBD.

#### Manufacturer Code

`S` = Sunrex, `C` = CIY. Other manufacturer codes are TBD.

```javascript
import { CloudService } from 'wvke-sdk';

// Enable weekly format (uses current date, manufacturer C, SKU K1 by default)
CloudService.generateSn({ weekly: true, count: 3 });
// ['C K1 39 65 0001', 'C K1 39 65 0002', 'C K1 39 65 0003']
// 39=2026 year code, 65=current week (28+37)

// Quick enable via format option
CloudService.generateSn({ format: 'weekly', manufacturer: 'S', count: 2 });
// ['S M1 39 65 0001', 'S M1 39 65 0002']

// Specify manufacturer, SKU, year, and week
CloudService.generateSn({ weekly: true, manufacturer: 'C', sku: 'K1', year: 2026, week: 52, start: 1, count: 3 });
// ['C K1 39 89 0001', 'C K1 39 89 0002', 'C K1 39 89 0003']
// 89=week 52 code (52+37)

// Auto-switch to base-36 encoding beyond 9999
CloudService.generateSn({ weekly: true, count: 5, start: 9998 });
// ['...9998', '...9999', '...A7PS', ...]
```

