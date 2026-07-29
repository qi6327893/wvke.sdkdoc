# Cloud API

## Overview

Cloud APIs are used for open-platform operation capabilities and cloud data exchange. They are suitable for driver clients, web apps, or business backends that need to connect accounts, devices, configuration sync, operational content, and related services.

This category currently acts as the entry point for cloud capabilities. Specific APIs can be added here by business module later.

## Scope

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
| format | string | `"0001"` | Format template; `0` for digit placeholders, `"weekly"` for weekly format. |
| start | number | `1` | Starting serial number. |
| end | number | — | End serial number (inclusive), alternative to `count`. |
| count | number | `1` | Number of SNs to generate, alternative to `end`. |
| maxLength | number | `20` | Maximum output length in ASCII bytes. |
| weekly | boolean | — | Enable weekly SN format (mutually exclusive with `format`). |
| manufacturer | string | `"C"` | Manufacturer code. `C`=CIY, `S`=Sunrex. |
| sku | string | `"K1"` | SKU/PN code. `K1`→part `K001`, `M1`→part `M001`. |
| year | number | current | Calendar year. |
| week | number | current | ISO week number 1–53. |

### Return Value

• `string[]` – Array of generated SN strings.

### Basic Format Examples

```javascript
CloudService.generateSn();                          // ['0001']
CloudService.generateSn({ start: 1, count: 5 });   // ['0001','0002',...]
CloudService.generateSn({ format: 'SN-0000', start: 10, end: 12 });
// ['SN-0010', 'SN-0011', 'SN-0012']
```

### Weekly SN Format

Output structure:

```text
C M1 39 89 0001
│ │  │  │  └─ Unique consecutive ID (4 digits)
│ │  │  └─ Week code
│ │  └─ Year code
│ └─ SKU/PN code
└─ Manufacturer code
```

**Unique consecutive ID:** 4-digit decimal 0000–9999; beyond 9999 switches to base-36 (AAAA–ZZZZ).
Sequence: `0001~9999`, `A001~Z999`, `AA00~ZZ99`…

**Week code:** ISO week number + 37. Week 52 → `89`, week 01 → `38`.

**Year code:** Last 2 year digits + 13. 2026 → `39`, 2027 → `40`.

**SKU/PN:** `M1`→M001, `K1`→K001.

**Manufacturer:** `S`=Sunrex, `C`=CIY.

```javascript
CloudService.generateSn({ weekly: true, count: 3 });
// ['C M1 39 65 0001', 'C M1 39 65 0002', 'C M1 39 65 0003']

CloudService.generateSn({ weekly: true, manufacturer: 'C', sku: 'K1', year: 2026, week: 52, start: 1, count: 3 });
// ['C K1 39 89 0001', 'C K1 39 89 0002', 'C K1 39 89 0003']

CloudService.generateSn({ weekly: true, count: 5, start: 9998 });
// ['...9998', '...9999', '...A7PS', ...]
```
