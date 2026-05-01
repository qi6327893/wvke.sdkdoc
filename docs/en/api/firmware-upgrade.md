# General Custom Upgrade

## Overview

`ServiceFirmwareUpgrade` is an independent firmware-upgrade service. The caller first switches the app device into BOOT mode, waits for the device to reboot, selects and opens the BOOT device again, then passes that BOOT device and a `.bin` firmware path to start the formal upgrade. The SDK handles the legacy upgrade flow internally, including signing unlocks, erase, packet writes, CRC verification, and jump-to-app.

The service exposes these APIs:

- `firmwareUpgrade.enterBoot(device, options?)`
- `firmwareUpgrade.upgrade(device, binFilePath, options?)`

The debug tool does not expose extra parameters for `enterBoot`; the SDK internally sends the enter-BOOT report padded to 64 bytes.

## enterBoot

ServiceFirmwareUpgrade.enterBoot(device, options?)

Brief: Send the enter-BOOT command to an opened app-mode device. The device reboots automatically; after the reboot, call `navigator.hid.requestDevice()` again and open the BOOT device.

### Parameters

| Field | Type | Description | Required |
|------|------|-------------|----------|
| device | HIDDevice | An opened app-mode WebHID device | Yes |
| options | object | Optional settings such as `reportId` and `onProgress` | No |

### Return Value

Overall type: `Promise<{ code: number, success: boolean, requiresReconnect: boolean, message: string }>`

## upgrade

ServiceFirmwareUpgrade.upgrade(device, binFilePath, options?)

Brief: Upgrade firmware for a reconnected and opened BOOT device.

### Parameters

| Field | Type | Description | Required |
|------|------|-------------|----------|
| device | HIDDevice | An opened BOOT-mode WebHID device. Usually this is the device selected again with `navigator.hid.requestDevice()` after `enterBoot()` | Yes |
| binFilePath | string \| Blob | Firmware `.bin` path or browser file object. In browsers, use a site-accessible path, HTTPS URL, or a user-selected `File`. In Node, local file paths and `file://` are also supported | Yes |
| options | object | Upgrade options, timeout settings, and progress callback | No |

Common `options` fields:

| Field | Type | Description |
|------|------|-------------|
| onProgress | `(progress) => void` | Upgrade progress callback |
| timeout | number | Normal command response timeout. Default: `5000` ms |
| eraseTimeout | number | Erase phase timeout. Default: `15000` ms |
| writePacketDelay | number | Delay between firmware packet groups. Default: `2` ms |
| commandDelay | number | Delay between signing-chain commands. Default: `20` ms |
| deviceInfoTimeout | number | Device-info read timeout. Default: `3000` ms |
| crcTimeout | number | Device CRC read timeout. Default: `10000` ms |
| serialNumber | string \| number[] \| Uint8Array | Optional device SN. If omitted, the SDK first uses cached `SNDatas` and otherwise reads device info automatically |
| sn / SNDatas / snDatas | string \| number[] \| Uint8Array | Compatible aliases for `serialNumber` |

Progress `phase` values can be: `boot`, `bootConnected`, `loading`, `loaded`, `sign`, `erase`, `write`, `crc`, `complete`.

### Return Value

Overall type: `Promise<{ code: number, success: boolean, source: string, size: number, paddedSize: number, packetCount: number, crc: number, deviceCrc: number }>`

| Field | Type | Description |
|------|------|-------------|
| code | number | `0` means the upgrade flow completed |
| success | boolean | Whether the upgrade succeeded |
| source | string | Firmware source path |
| size | number | Original firmware size |
| paddedSize | number | Size after padding to 512 bytes |
| packetCount | number | Number of firmware packet groups written |
| crc | number | Locally calculated CRC |
| deviceCrc | number | CRC returned by the device |

If the device CRC does not match the local CRC, the SDK throws and stops before jumping to app.

## Example

```javascript
import { ServiceFirmwareUpgrade } from 'wvke-sdk';

const filters = [{ vendorId: 0x1caa, productId: 0x0806, usagePage: 0xffa0, usage: 0x01 }];

const [device] = await navigator.hid.requestDevice({ filters });
if (!device.opened) {
	await device.open();
}

const firmwareUpgrade = new ServiceFirmwareUpgrade();

await firmwareUpgrade.enterBoot(device);

// The device reboots into BOOT mode. Select and open the BOOT device again with getDevices-style filters.
const [bootDevice] = await navigator.hid.requestDevice({ filters });
if (!bootDevice.opened) {
	await bootDevice.open();
}

const result = await firmwareUpgrade.upgrade(bootDevice, '/firmware/example.bin', {
	onProgress(progress) {
		console.log(progress.phase, progress.progress, progress.current, progress.total);
	}
});

console.log('Upgrade complete:', result);
```

Browser local-file example:

```javascript
const fileInput = document.querySelector('input[type="file"]');
const firmwareFile = fileInput.files[0];
await firmwareUpgrade.upgrade(bootDevice, firmwareFile);
```

Remote firmware example:

```javascript
await firmwareUpgrade.upgrade(bootDevice, 'https://example.com/firmware/example.bin');
```

Pass SN explicitly when it is already known:

```javascript
await firmwareUpgrade.upgrade(bootDevice, '/firmware/example.bin', {
	serialNumber: '2493C45353E0A336'
});
```

## Debug Tool

In the SDK debug page, use the General Custom Upgrade group in three steps:

1. Under `Getting Started / Device Initialization`, call keyboard `getDevices` and `init`, then click `enterBoot` so the device enters BOOT and reboots automatically.
2. After the reboot, click `connectBootDevice`; the debug page uses getDevices-style filters when selecting the BOOT device in the browser prompt.
3. Click `upgrade`; the debug page uses the BOOT device from step 2 and displays upgrade progress.

## Notes

TIP

- Browsers cannot read arbitrary local disk paths. Pass a user-selected `File`, or put local firmware under a path the current site can fetch, such as `/firmware/example.bin`.
- Remote firmware must use HTTPS. Localhost HTTP URLs are allowed only for development.
- Do not disconnect the device or refresh the page during upgrade.
- Make sure the firmware file matches the target device model.
