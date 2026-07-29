# 机械轴键盘接口

本组文档记录传统机械轴键盘对应的 `ServiceKeyboardMK` 接口。文档结构与[线性轴键盘接口](./info.md)保持一致，按 SDK 调试器中的接口分类拆分。

## 初始化设备

机械轴键盘使用统一的初始化入口。请先参阅[初始化设备](./initialize.md)，完成设备发现和 `init()` 后，再调用以下分类中的接口。

```javascript
import { ServiceKeyboardMK } from 'wvke-sdk';

const keyboard = new ServiceKeyboardMK({
  configs: [{ vendorId: 0x1caa, productId: 0x0806, usagePage: 0xffa0, usage: 0x01 }],
  usage: 0x01,
  usagePage: 0xffa0,
});

const { devices } = await keyboard.getDevices();
await keyboard.init(devices[0].id);
```

## 接口分类

- [全局功能](./mechanical-info.md)
- [按键功能](./mechanical-key.md)
- [万花筒灯效](./mechanical-lighting.md)
- [高级按键功能](./mechanical-higherKey.md)

## 不支持的线性轴专用功能

`ServiceKeyboardMK` 不提供以下线性轴键盘专用接口：

- RT 全行程开关和全局行程设置
- 按键行程实时渲染开关
- RT 按键编组
- 轴体行程、ADC 采样和校准
- 轴体详情、轴体微调和自定义轴体
- 手柄功能
- DKS、MPT 高级按键

需要这些能力时，请使用线性轴键盘的 `ServiceKeyboard` 接口。
