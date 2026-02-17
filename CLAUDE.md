# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

moNa2 v2 — ZMK firmware configuration for the moNa2 split keyboard. Uses Seeeduino XIAO BLE (nRF52840) with a PMW3610 trackball sensor on the right half and an EC11 rotary encoder on the left half. Original design by sayu-hub.

## Build

ファームウェアビルドはGitHub Actions経由で実行される。ローカルビルド環境は不要。

- `git push` するだけで `.github/workflows/build.yml` が走る（ZMK公式の `build-user-config.yml@v0.3.0` を使用）
- ビルド成果物（.uf2）はGitHub ActionsのArtifactsからダウンロード
- `build.yaml` がビルドマトリクスを定義: `mona2_r`(右手・セントラル)、`mona2_l`(左手)、`settings_reset`

## Architecture

### Split構成

- **右手 (`mona2_r`)**: セントラル側。トラックボール（PMW3610, SPI接続）搭載。ZMK Studioも有効
- **左手 (`mona2_l`)**: ペリフェラル側。ロータリーエンコーダ（EC11）搭載
- マトリクス: 11列 x 4行、`col2row`ダイオード方向

### ファイル構成

| パス | 役割 |
|---|---|
| `config/mona2.keymap` | キーマップ定義（全レイヤー）。ここが一番頻繁に編集するファイル |
| `config/mona2_r.conf` | 右手側Kconfig（PMW3610, Studio, BLE, RGB LED等） |
| `config/mona2_l.conf` | 左手側Kconfig（エンコーダ, バッテリー） |
| `config/west.yml` | West manifest。ZMK本体と外部モジュールの依存定義 |
| `config/mona2.json` | ZMK Studio用レイアウト定義 |
| `boards/shields/mona2/mona2.dtsi` | 共通Devicetree（マトリクス変換, kscan, トラックボールリスナー, エンコーダ, 物理レイアウト） |
| `boards/shields/mona2/mona2_r.overlay` | 右手固有DT（SPI/PMW3610設定, トラックボールスクロール設定） |
| `boards/shields/mona2/mona2_l.overlay` | 左手固有DT（GPIOピン, エンコーダ有効化） |
| `boards/shields/mona2/Kconfig.defconfig` | 右手セントラル設定、スプリット共通設定 |

### 外部モジュール (west.yml)

- `zmkfirmware/zmk` v0.3.0 — ZMK本体
- `badjeff/zmk-pmw3610-driver` (zmk-0.3) — PMW3610トラックボールドライバ
- `caksoylar/zmk-rgbled-widget` — RGB LEDバッテリー/レイヤー表示
- `zettaface/zmk-input-processor-keybind` — 入力プロセッサキーバインド

### キーマップレイヤー

- Layer 0: デフォルト（QWERTY）
- Layer 1: 数字・括弧
- Layer 2: 記号・ファンクションキー・マウスボタン
- Layer 3: ナビゲーション（矢印、仮想デスクトップ切替）。トラックボールがスクロールモードになる
- Layer 4: Bluetooth管理・bootloader
- Layer 5 (MOUSE): マウスボタン専用
- Layer 6 (SCROLL): スクロール専用

### COROPIT対応

COROPIT版を使う場合、`mona2_r.overlay` のトラックボール設定で `invert-x` と `invert-y` のコメントアウトを外す。

## トラックボール設定のポイント

- CPIは `mona2_r.overlay` の `cpi = <600>` で変更
- 軸反転・軸入替は `mona2_r.overlay` の `invert-x`, `invert-y`, `swap-xy`
- スクロール動作は `mona2_r.overlay` の `scroller` ノード内 input-processors で制御
- オートマウスレイヤーは `mona2.dtsi` の `trackball_central_listener` で `zip_temp_layer` をアンコメントして有効化
