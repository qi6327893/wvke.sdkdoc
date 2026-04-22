# 按键映射

## 获取按键映射

```javascript
const mapping = await mouse.getButtonMapping();
console.log(mapping);
```

返回示例：
```json
{
  "left": "left-click",
  "right": "right-click",
  "middle": "middle-click",
  "dpi": "dpi-cycle",
  "forward": "forward",
  "backward": "backward"
}
```

## 设置按键映射

```javascript
const newMapping = {
  left: "left-click",
  right: "right-click",
  middle: "middle-click",
  dpi: "dpi+",
  forward: "browser-forward",
  backward: "browser-back"
};

await mouse.setButtonMapping(newMapping);
console.log('按键映射已更新');
```

### 支持的按键功能

- `left-click`: 左键点击
- `right-click`: 右键点击
- `middle-click`: 中键点击
- `dpi-cycle`: DPI循环切换
- `dpi+`: DPI增加
- `dpi-`: DPI减少
- `forward`: 前进
- `backward`: 后退
- `browser-forward`: 浏览器前进
- `browser-back`: 浏览器后退
- `macro-1` 到 `macro-5`: 宏录制1-5
