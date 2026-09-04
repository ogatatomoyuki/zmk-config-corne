# Corne ZMK キーマップ設定ノート

## 現在の構成

- **コントローラー**: nice!nano v2
- **ファームウェア**: ZMK v0.3.0
- **キーマップ**: Callum式 One-Shot ベース（hold-tap 排除）

## 今後の課題

### 1. マウススクロール対応

**現状**: ZMK v0.3.0 にはマウスキー機能がない

**理想**: Atreus と同様に NAV レイヤーでスクロール（MS_WHLU/MS_WHLD）

**解決策候補**:
- ZMK main ブランチにアップグレード（マウスキー対応）
- 2026-02 時点では main ブランチにビルドエラーあり（pillbug ボード重複定義）

### 2. Swapper の改善（tri-state 対応）

**現状**: NAV + W を両方押し続ける必要がある

**理想**: Atreus と同様に NAV のみ押しっぱなしでアプリ一覧を保持

**解決策候補**:
- ZMK main ブランチ + zmk-tri-state モジュールを使用
- 参考: https://github.com/urob/zmk-tri-state

**問題点**:
- ZMK v0.3.0 では tri-state モジュールに互換性がない
- ZMK main ブランチではボード名が変更されており、ビルドが通らない可能性

### 3. Caps Word が動作しない（解決: Caps Lock で代用）

**症状**: caps_word 発動後、文字キーを押しても何も出力されない

**調査結果**:
- BASE レイヤーに配置しても同様 → レイヤー切り替えは原因ではない
- USB 接続でも同様 → Bluetooth は原因ではない
- QMK Atreus でも同じ症状（weak mods 問題）

**解決策**: Caps Lock (`&kp CAPS`) で代用
- NAV + R で Caps Lock をトグル

## Atreus との比較

| 機能 | Atreus (QMK) | Corne (ZMK) |
|------|-------------|-------------|
| 基本キー | ✅ | ✅ |
| One-Shot Mods | ✅ | ✅ |
| マクロ (Undo等) | ✅ | ✅ |
| Swapper | ✅ NAVのみ保持 | ⚠️ NAV+W保持が必要 |
| Caps Word | ✅ 自前実装 | ❌ → Caps Lockで代用 |
| マウススクロール | ✅ | ❌ ZMK v0.3.0非対応 |
| IMEコンボ | ✅ | ✅ |

## 解決済みの問題

### コンボの反応速度
- `timeout-ms` を 80ms → 50ms に短縮
- `require-prior-idle-ms = <150>` を追加してタイピング中のコンボ誤発動を防止

### マクロの修飾キー問題
- `&kp LG(Z)` のような短縮形は ZMK v0.3.0 で動作しない
- `macro_press` / `macro_tap` / `macro_release` を使用して明示的に修飾キーを操作
- `wait-ms` / `tap-ms` を 30ms に設定
- 例: `<&macro_press &kp LGUI>, <&macro_tap &kp Z>, <&macro_release &kp LGUI>`

### 右手が動作しない問題
- 原因: 左右間の Bluetooth ペアリングが壊れていた
- 解決: 両方の手に settings_reset ファームウェアをフラッシュしてから通常ファームウェアを再フラッシュ

## レイヤー構成

```
BASE: QWERTY + 親指に NAV/SPC/SYM | BSPC/ENT
NAV:  ESC, Swapper, Tabber, CapsLock, 矢印, マクロ(Undo/Cut/Copy/Paste/Redo/Zoom)
SYM:  数字, 記号, One-Shot Mods
FUN:  F1-F12, 音量, BT設定（NAV+SYM 同時押し）
```

## コンボ

| キー | 結果 |
|------|------|
| SPC + BSPC | LNG1（IME ON）|
| BSPC + ENT | LNG2（IME OFF）|
| D + F | Ctrl+B（tmux prefix）|

## 未解決タスク

### roam（RPi5）からの BLE ペアリングが失敗する

**日付**: 2026-02-21

**症状**:
- `bluetoothctl scan on` で `Corne Mini` は検出される（`F3:A0:DC:21:3E:BF`）
- `Connected: yes` まではいくが `pair` で `ConnectionAttemptFailed` または `AuthenticationFailed`
- USBドングル（TP-Link UB500, `88:A2:9E:57:CF:07`）でも内蔵BT（`10:5A:95:76:22:46`）でも同様
- `btmgmt pair` もタイムアウト

**試行済み**:
- Corne 側 BT_CLR 実行済み
- 左右とも settings_reset + 本番ファーム再フラッシュ済み（BT名は `CorneCustom` → `Corne Mini` に更新確認）
- roam 側でデバイス remove → 再スキャン → ペアリング
- `pairable on` 有効化
- bluetooth サービス再起動
- `btmgmt --index 1 pair` 直接試行

**未試行**:
- Mac からの BT 接続でキーマップ動作確認
- roam の BlueZ バージョン確認・アップデート
- roam の `/var/lib/bluetooth/` 内の古いペアリング情報を手動削除
- ドングルのファームウェア更新確認
- `journalctl -u bluetooth` でエラーログ詳細確認

## ビルド方法

GitHub Actions で自動ビルド。ファームウェアは Artifacts からダウンロード。

```bash
# ビルド状況確認
gh run list --limit 3

# ファームウェアダウンロード
gh run download <run_id>

# フラッシュ（dd を使うこと。cp は不完全な書き込みになる場合あり）
# 1. リセットボタン2回押し → NICENANO ドライブがマウント
# 2. dd で書き込み
dd if=firmware.uf2 of=/Volumes/NICENANO/firmware.uf2 bs=4096
```

## UF2 フラッシュ時の注意（2026-02-21 発見）

**`cp` ではなく `dd` を使うこと。**

- `cp` は `fcopyfile failed: Input/output error` を出し、書き込みが不完全になる場合がある
- 見かけ上エラーでも device が自動リブートするため成功に見える
- 実際には前のファームウェア（settings_reset 等）が残ったままになることがある
- `dd` なら確実に書き込める：
  ```bash
  dd if=firmware.uf2 of=/Volumes/NICENANO/firmware.uf2 bs=4096
  ```
- I/O error は device のリブートによるもので正常

## 変更履歴

### 2026-02-21
- NAV レイヤー: ZM+/ZM- を1つ左に移動（Charybdis と統一）
- tmux コンボ追加: D+F → Ctrl+B（timeout 80ms, require-prior-idle 150ms）
- Caps Word コメント削除
- Mac から BT ペアリング成功（settings_reset → 本番ファーム の手順）
