# レイヤー設計ルール

## レイヤー構成 (固定)

| Layer | #define | 内容 | 発動方法 |
|-------|---------|------|---------|
| 0 | — | デフォルト (QWERTY) | — |
| 1 | — | 数字 + 矢印 | `&lt 1 SPACE` |
| 2 | — | 記号 | `&lt 2 ENTER` |
| 3 | — | メディア / Rectangle | `&mo 3` / `&lt 3 SPACE` |
| 4 | — | ブラウザタブ + BT管理 | `&lt 4 SEMICOLON` |
| 5 | MOUSE | マウスボタン | オートマウス自動発動 (`zip_temp_layer 5 500`) |
| 6 | SCROLL | スクロール | MOUSEレイヤーのK位置 `&mo 6` |

## ルール

- Layer 5 (MOUSE) と Layer 6 (SCROLL) は必ず末尾に配置し、番号を変えない
- MOUSE / SCROLL は `#define` で定義し、keymap内では数字リテラルではなくマクロ名を使う
- オートマウスレイヤーは `mona2.dtsi` の `trackball_central_listener` → `zip_temp_layer` で指定。レイヤー番号は MOUSE (5) と一致させること
- スクロールレイヤーは `mona2_r.overlay` の `scroller` → `layers` で指定。レイヤー番号は SCROLL (6) と一致させること
- MOUSEレイヤー内の `&mo 6` が SCROLL を指す。レイヤー追加・削除で番号がずれたら3箇所 (keymap define, dtsi, overlay) を必ず同時に更新する
- BT管理 (BT_SEL, BT_CLR, BT_CLR_ALL) は Layer 4 に集約。独立BTレイヤーは作らない
- Layer 0 の RC(2,5) 位置 (左手 Row2 の右端キー) は `&trans` とする（旧BTレイヤー直アクセスは廃止済み）
