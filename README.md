# KANTAN Play clone

[KANTAN Play online](https://kantan-play.com/play/) (Godot 製) を素の HTML + JS + Web Audio で再現したクローン。
1 ファイル (`index.html`) だけで動き、サーバー不要。Web MIDI で外部音源への出力と MIDI 鍵盤からの入力に対応。

```sh
open index.html            # そのまま開ける (soundfont は CDN から取得)
# または
python3 -m http.server 8000   # → http://localhost:8000
```

Web MIDI はセキュアコンテキスト (localhost / https / file) が必要。

## 画面
- **Key** ♭ / ♯ でキー変更 (相対マイナー併記)
- **楽器**: Ac.Guitar / Piano / E.Piano / Strings / Organ / Brass (soundfont-player の FluidR3_GM)。名前をクリックで順送り
- **リズムパターン**: 手弾き / Ballad / 8beat / 16beat / Waltz / Bossa / Rock。手弾き以外は自動演奏 (ドラム + ベース付き)
- **1〜7 ボタン** = キーのダイアトリックコード (本家と同じく 7 は m)。**♭ / ♯** で半音シフト (シフト中はメジャー扱い)
- **dim / sus4 / 9 / 7 / M7** の修飾。画面のボタンはトグル、キーボードは押している間だけ
- **オレンジのパッド 〜**: 押している間メジャー↔マイナー入れ替え (本家の Swap)。ボタンのコード名も m が付け外しされる
- **アルペジオ 1〜5** (パターン行の下、`F1`〜`F5`): 本家の 6 弦 × 8 ステップのパターン。ギター系の楽器では 1〜5、それ以外 (Pf スタイル) では 6〜10 が出る
  - コードボタンを「押す」「離す」たびに 1 ステップ進み、その列で印の付いた弦を弾く。下の帯の色がストロークの種類 (緑=同時 / 赤=低い弦から / 青=高い弦から / 灰=消音)
  - 離すとノートオフ (最低 0.3 秒は鳴らす)。ボタンやキーを変えるとステップは先頭へ戻る
- **オクターブ** (-2〜+2)、**テンポ** スライダー (40〜220)
- コードのボイシング (6 弦の音高) とコード表は本家 Godot 版の `KantanMusic` テーブルをそのまま使用
- **自動演奏** (Ballad / 8beat …) はクローン独自: **ストローク** の弾き方 7 種、**自動アルペジオ** の形 5 種、**ベース** 奏法 5 種。発音タイミング・強さをばらつかせて人間風にする (`HUMAN`)

## キーボード
本家 KANTAN Play online の InputMap と同じ割り当て (テンキーでも可)。

| キー | 機能 |
|---|---|
| `1` `2` `3` / `Z` `X` `C` | コード 1 2 3 |
| `4` `5` `6` / `A` `S` `D` | コード 4 5 6 |
| `7` / `Q` | コード 7 |
| `8` / `W` | ♭ (押している間) |
| `9` / `E` | ♯ (押している間) |
| `0` | 〜 メジャー↔マイナー入れ替え (押している間) |
| `V` `B` `N` `M` `,` | dim / sus4 / add9 / 7 / M7 (押している間) |
| `-` / `=` `+` | キー下げ / 上げ |
| `F1`〜`F5` | アルペジオ 1〜5 |
| `Space` | ストローク (クローン独自) |

## MIDI
- **MIDI 出力**: セレクトで PC 上の MIDI 音源 (IAC Driver 経由の DAW / ソフトシンセなど) を選ぶと内蔵音源の代わりに送る
  - コード: ch1 (楽器ごとに GM プログラムチェンジ)、ベース: ch2 (program 32 Acoustic Bass)、ドラム: ch10 (36 kick / 38 snare / 42 hi-hat)
  - AudioContext の予約時刻を `performance.now()` 基準に変換して `MIDIOutput.send(data, timestamp)` で送るので内蔵音源と同じ精度
- **MIDI 入力**: MIDI 鍵盤の白鍵 C D E F G A B → コード 1〜7、黒鍵 → 下の白鍵に ♯。押す / 離すでアルペジオが進み、ベロシティで強弱
- 起動時と出力切り替え時に全 16ch へ All Notes Off / All Sound Off を送る (鳴りっぱなし対策)

## 由来
[GOROman/learning-jev](https://github.com/GOROman/learning-jev) の step04 (KANTAN Play クローン × Jev) から Jev 判定部分を外したもの。
キー割り当て・アルペジオパターン・ボイシングテーブルは本家 KANTAN Play online (Godot 3) の `index.pck` から InputMap / シーン / GDScript バイトコードを読み取って移植した。
