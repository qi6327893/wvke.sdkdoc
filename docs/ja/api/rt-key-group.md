# RT キーグループ

## 概要

RT（Rapid Trigger）キーグループの設定と取得。プロトコルファンクションコード `0xFE`、形式は `HID + グループ番号`。

## RT キーグループの取得

keyboard.getRtKeyGroup()

### 戻り値

| フィールド | 型 | 説明 |
|------|------|------|
| code | number | ステータスコード |
| total | number | 有効キー数 |
| details | `Array<{ row, col, keyName, hid, group }>` | キーごとのグループ |
| layout | `Array<Array<...>>` | キーボードマトリックスレイアウト |

### 使用例

```javascript
const result = await keyboard.getRtKeyGroup();
```

## RT キーグループの設定

keyboard.setRtKeyGroup(params)

### パラメータ

| パラメータ | 型 | 説明 |
|------|------|------|
| keyName | `string` | キー名（単一キーモード） |
| group | `number` | グループ番号、デフォルト `0` |
| keys | `Array<{ keyName?, group? }>` | バッチモード |

### 使用例

```javascript
await keyboard.setRtKeyGroup({ keyName: 'A', group: 1 });
```
