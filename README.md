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
- **1〜7 ボタン** = キーのダイアトニックコード。**♭ / ♯** で半音シフト
- **dim / sus4 / 9 / 7 / M7** の修飾 (トグル)
- **オレンジのパッド 〜**: タップでストローク、上下になぞるとダウン / アップストローク
- **テンポ** スライダー (40〜220)、**ストローク** の弾き方 7 種、**アルペジオ** の形 5 種、**ベース** 奏法 5 種
- 発音タイミング・強さ・ストロークの速さを毎回少しばらつかせて人間が弾いている風にする (`HUMAN`)

## キーボード
本家 KANTAN Play online の InputMap と同じ割り当て (テンキーでも可)。

| キー | 機能 |
|---|---|
| `1` `2` `3` / `Z` `X` `C` | コード 1 2 3 |
| `4` `5` `6` / `A` `S` `D` | コード 4 5 6 |
| `7` / `Q` | コード 7 |
| `8` / `W` | ♭ (半音下げ) |
| `9` / `E` | ♯ (半音上げ) |
| `0` / `Space` | 〜 ストローク |
| `V` `B` `N` `M` `,` | dim / sus4 / add9 / 7 / M7 |

## MIDI
- **MIDI 出力**: セレクトで PC 上の MIDI 音源 (IAC Driver 経由の DAW / ソフトシンセなど) を選ぶと内蔵音源の代わりに送る
  - コード: ch1 (楽器ごとに GM プログラムチェンジ)、ベース: ch2 (program 32 Acoustic Bass)、ドラム: ch10 (36 kick / 38 snare / 42 hi-hat)
  - AudioContext の予約時刻を `performance.now()` 基準に変換して `MIDIOutput.send(data, timestamp)` で送るので内蔵音源と同じ精度
- **MIDI 入力**: MIDI 鍵盤の白鍵 C D E F G A B → コード 1〜7、黒鍵 → 下の白鍵に ♯。ベロシティで強弱

## 由来
[GOROman/learning-jev](https://github.com/GOROman/learning-jev) の step04 (KANTAN Play クローン × Jev) から Jev 判定部分を外したもの。
