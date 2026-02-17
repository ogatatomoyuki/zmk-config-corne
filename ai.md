# Corne ZMK ファームウェア設定

## プロジェクト概要
Corne キーボードを ZMK ファームウェアで無線（Bluetooth）で使用。Atreus と同様の Callum 式 One-Shot ベースのキーマップを移植。

## 現在のステータス
**完了 - 基本機能動作確認済み（2026-02-17）**

---

## ハンドオフ（2026-02-17）

### 完了した作業

1. **基本設定完了**
   - ZMK v0.3.0 + nice!nano v2
   - Callum 式 One-Shot Modifier
   - Swapper / Tabber マクロ
   - IME 切り替えコンボ（SPC+BSPC / BSPC+ENT）

2. **マクロ問題の解決**
   - `&kp LG(Z)` 短縮形が動作しない問題を発見
   - `macro_press` / `macro_tap` / `macro_release` パターンで解決
   - Undo, Cut, Copy, Paste, Redo, Zoom In/Out すべて動作

3. **Caps Word → Caps Lock 代用**
   - ZMK の `&caps_word` が動作しない（文字が出力されない）
   - QMK Atreus と同じ症状（weak mods 問題）
   - `&kp CAPS` で代用

4. **Bluetooth 接続**
   - Mac との接続完了
   - Roam (Raspberry Pi) との接続完了
   - BT_SEL 0/1/2 でデバイス切り替え可能

5. **Roam 設定**
   - Bluetooth ペアリング・ボンディング完了
   - HDMI 出力設定（1920x1080@60Hz）
   - 起動時自動設定（~/.config/autostart/hdmi-resolution.desktop）

### 動作状況

| 機能 | 状態 |
|------|------|
| 基本キー入力 | OK |
| One-Shot Mods | OK |
| マクロ (Undo等) | OK |
| Swapper (Cmd+Tab) | OK（NAV+W 保持必要） |
| Tabber (Ctrl+Tab) | OK |
| Caps Lock | OK |
| IME コンボ | OK |
| Bluetooth (Mac) | OK |
| Bluetooth (Roam) | OK |
| マウススクロール | NG（ZMK v0.3.0 非対応） |

### 今後の課題

1. **マウススクロール対応**
   - ZMK main ブランチにアップグレードが必要
   - 2026-02 時点で main にビルドエラーあり（pillbug 重複定義）

2. **Swapper の tri-state 対応**
   - 現状: NAV + W を両方押し続ける必要
   - 理想: NAV のみ押しっぱなしでアプリ一覧保持
   - zmk-tri-state モジュールが必要（v0.3.0 非対応）

3. **バッテリー問題**
   - 左手のバッテリーが放電 or 接続不良
   - USB 給電なしでは動作しない

### ファイル構成

```
zmk-config-corne/
├── .github/workflows/build.yml  # GitHub Actions (v0.3.0)
├── build.yaml                   # nice_nano_v2 + corne シールド
├── config/
│   ├── west.yml                 # ZMK v0.3.0 ピン留め
│   ├── corne.conf               # Bluetooth, デバウンス設定
│   └── corne.keymap             # キーマップ
├── NOTES.md                     # 詳細な技術ノート
└── firmware/                    # ビルド済みファームウェア
```

### Bluetooth 切り替え

| キー | デバイス |
|------|----------|
| FUN + N (BT_SEL 0) | Mac |
| FUN + M (BT_SEL 1) | Roam |
| FUN + , (BT_SEL 2) | 未設定 |
| FUN + . (BT_CLR) | プロファイルクリア |

### フラッシュ手順

```bash
# ビルド確認
gh run list --limit 1

# ファームウェアダウンロード
gh run download <run_id> -D firmware

# フラッシュ（左手）
# リセット2回 → NICENANO マウント
cp firmware/corne_left-nice_nano_v2-zmk.uf2 /Volumes/NICENANO/

# フラッシュ（右手）
cp firmware/corne_right-nice_nano_v2-zmk.uf2 /Volumes/NICENANO/
```

---

## リポジトリ
- GitHub: https://github.com/ogatatomoyuki/zmk-config-corne
- ローカル: `/Users/ogatatomoyuki/Kotonoha_Mac/Keyboards/zmk-config-corne`

## ハードウェア構成
- キーボード: Corne (36キー)
- コントローラ: nice!nano v2
- ファームウェア: ZMK v0.3.0

## 関連プロジェクト
- Atreus (QMK): `/Users/ogatatomoyuki/Kotonoha_Mac/Notes/40_Projects/プライベート_キーマップ変更/qmk/atreus`
- zmk-corne-mini (カスタムシールド版): `/Users/ogatatomoyuki/Kotonoha_Mac/Keyboards/zmk-corne-mini`
