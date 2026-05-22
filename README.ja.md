# hammerspoon-keystroke-macro

[English](./README.md) | [日本語](./README.ja.md)

[Hammerspoon](https://www.hammerspoon.org) 用のシンプルなキーストロークマクロモジュール。
一連のキー操作を記録してホットキーで再生できます。

## 特徴

- トグル式の記録（同じホットキーで開始/停止）
- 録音/停止のホットキー自体が混入しないよう自動除去
- 再生時は固定間隔で連続 post するため、記録時のタイピング速度に影響されない

## 動作要件

- macOS
- [Hammerspoon](https://www.hammerspoon.org)
- Hammerspoon に「アクセシビリティ」権限を付与（System Settings > Privacy & Security > Accessibility）

## インストール

```sh
curl --create-dirs -o ~/.hammerspoon/keystroke_macro/init.lua \
  "https://raw.githubusercontent.com/ttskch/hammerspoon-keystroke-macro/main/keystroke_macro/init.lua?$(date +%s)"
```

末尾の `?$(date +%s)` は GitHub の CDN キャッシュを回避し、常に最新版を取得するためのものです。

## 設定例

`~/.hammerspoon/init.lua` に以下を追記します。

```lua
require('keystroke_macro').setup()
```

これでデフォルトのホットキーが登録されます：

- `Cmd + Shift + Option + R` — 録音 開始/停止
- `Cmd + Shift + Option + P` — 記録したマクロを再生（長押しで連続再生）

ホットキーをカスタムしたい場合は `setup()` に渡します：

```lua
require('keystroke_macro').setup({
    toggleHotkey = {{"ctrl", "alt"}, "F1"},
    playHotkey   = {{"ctrl", "alt"}, "F2"},
})
```

書き終わったらメニューバーの槌アイコン → `Reload Config` で反映してください。

## 使い方

デフォルト設定の場合：

| 操作 | キー |
|---|---|
| 録音 開始/停止 | `Cmd + Shift + Option + R` |
| 再生（長押しで連続再生） | `Cmd + Shift + Option + P` |

### 例

1. `Cmd + Shift + Option + R` → 「● REC」が表示される
2. 記録したい一連のキー操作を行う（例: 定型文の入力、カーソル移動、削除など）
3. `Cmd + Shift + Option + R` → 「■ Saved」が表示され記録完了
4. 別の場所にカーソルを移動
5. `Cmd + Shift + Option + P` → 記録した操作が再生される

再度録音すると前回のマクロは上書きされます。

## API

| 関数 | 説明 |
|---|---|
| `setup(opts)` | ホットキーを登録。`opts.toggleHotkey` / `opts.playHotkey` でデフォルトを上書きできる |
| `toggle()` | 録音を開始 / 停止（トグル）。再録音すると前回のマクロは上書きされる |
| `play()` | 保存されたマクロを再生 |

## カスタマイズ

`keystroke_macro/init.lua` 冒頭の定数で挙動を調整できます。

| 定数 | デフォルト | 説明 |
|---|---|---|
| `TRIM_WINDOW` | `0.15` (秒) | 録音開始/停止のホットキー由来イベントを除去する閾値。誤って混入する場合は値を上げる |
| `REPLAY_INTERVAL_US` | `3000` (μs) | 再生時の各イベント間隔。小さくすると高速化するが、モディファイア取りこぼしのリスクが上がる。Slack や IME 経由で取りこぼしが起きる場合は `5000`〜`10000` まで上げる |

## 既知の制約

- マクロは **Hammerspoon 再起動で消える**（メモリ上のみ保持）
- IME 経由の入力は環境によって再現性が落ちる場合あり（IME イベントは別レイヤー）
- Vim mode のマクロ機構とは別系統。両方を併用すると混乱する可能性あり

## License

[MIT](./LICENSE)
