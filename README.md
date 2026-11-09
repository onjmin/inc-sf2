# inc-sf2

淫ク☆サウンドフォント。淫夢・クッキー☆の声から作ったシンセ4音色を、.sf2 と WebAudioFont（JS）で配る。

## 方針

- 淫ク☆サウンドフォントは、このリポジトリの持ち主が作って保守する。
- 素材は **淫夢とクッキー☆だけ** にする。レスリング・エア本・ヒカマニなどの素材は、拡張としても今後入れない。
- 名前が近いものとして、虹.氏のレ淫棒サウンドフォント（2017年からの版）と、お天気雨氏の 2020年版（RaInBow SoundFont 20200424）がある。どちらも別の作者のもので、ここで作るものとは別物。

- 試し弾き: https://onjmin.github.io/inc-sf2/
- SF2: https://onjmin.github.io/inc-sf2/inc.sf2
- WebAudioFont: `https://onjmin.github.io/inc-sf2/sound/<番号>0_Inc_sf2_file.js`（一覧は [sound/list.txt](sound/list.txt)）

## 音色

| 番号（GM） | 名前 | 素材 | 元の高さ |
|---|---|---|---|
| 80 | YMN | 初代クッキー☆ SIK「わたしも食べさせるぅ＾～」の「るぅ＾～」 | C5 |
| 81 | UDK | 初代クッキー☆ UDK「あーっ・・・」 | A#3 |
| 82 | 野獣 | INMシンセ「野獣シンセ G4」 | G4 |
| 83 | MUR | 第三章 MUR「いいゾ～これ」の「ゾ～」 | C#3 |

どれも C2〜C6 を短3度おきに 17 本置き、押している間はループで鳴り続ける。32 kHz・16bit。

## 使い方

dtm（[onjmin/dtm](https://github.com/onjmin/dtm)）で .sf2 を読む。楽器は GM の番号で選ぶ（80〜83）。

```
#sf2=https://onjmin.github.io/inc-sf2/inc.sf2 #t0font=ExtSF2 #t0inst=Lead 1 (square)
@0 t132 o5 c4 d4 e4 g4;
```

[surikov/webaudiofont](https://github.com/surikov/webaudiofont) のプレイヤーで読む。

```js
const player = new WebAudioFontPlayer();
player.loader.startLoad(ctx, "https://onjmin.github.io/inc-sf2/sound/0800_Inc_sf2_file.js", "_tone_0800_Inc_sf2_file");
player.loader.waitLoad(() => player.queueWaveTable(ctx, ctx.destination, window._tone_0800_Inc_sf2_file, 0, 60, 1));
```

WebAudioFont のファイルは surikov の配信元と同じ形（`sound/PPPV_<バンク>.js`、変数 `_tone_PPPV_<バンク>`）。ゾーンは `sample`（16bit PCM の base64）で、ループ点は標本の番号。

## 作り方

声の伸ばしを WORLD で響きと息に分け、目標の高さで組み立て直す（再生速度で高さを変えないので、声色が動かない）。

- 息を ×0.25 にする。
- 響きを時間方向に 25ms ならす。
- 4.5 kHz から上を 8 dB 下げて、かすれを抑える。
- ループは周期の整数倍の長さにし、継ぎ目を 30ms クロスフェードする。

作り直すときは、dtm の `scripts/otomad/tools/inc_soundfont.py` を実行する。細かい数値と経緯は dtm の `docs/otomad/techniques/voice-instrument.md` に書いてある。

声の素材の権利は、それぞれの元の作品と作者にある。

## ほかの作者のサウンドフォント

淫ク☆とは別に、ほかの作者のサウンドフォントを作者ごとのフォルダに置き、web から呼べるようにしている（淫ク☆の素材の方針はこれらには当てはめない。中身は元の配布のまま）。

| フォルダ | 名前 | 作者 | 版 | 音色 |
|---|---|---|---|---|
| `niji/20171022/` | レ淫棒サウンドフォント | 虹.（逃亡者） | 20171022（2017/09/10 公開の版を直したもの） | 102 |
| `otenkiame/20200424/` | RaInBow SoundFont（レ淫棒 2020年版、レ要素なし） | お天気雨 | 20200424（元は sm36367282 で配布） | 39 |

- .sf2: `https://onjmin.github.io/inc-sf2/niji/20171022/Rainbow_SoundFont.sf2`、`https://onjmin.github.io/inc-sf2/otenkiame/20200424/RaInBow_SoundFont.sf2`（URL に空白を入れないよう、お天気雨版はファイル名の空白を `_` にした）
- Domino の音源定義: 同じフォルダの `.xml`。お天気雨版は説明書・更新履歴・音色一覧も置いた。
- WebAudioFont: `<フォルダ>/sound/<バンク3桁><プログラム3桁>_<名前>.js`、変数は `_tone_` を頭に付けたもの。名前は虹.版が `Rainbow_20171022`、お天気雨版が `RaInBow_20200424`。どちらもキャラごとにバンクを分けた作りで GM の番号では選べないので、バンクもファイル名に入れた。一覧（バンク・プログラム・元の名前・日本語名）は `sound/list.txt`。

```js
player.loader.startLoad(ctx, "https://onjmin.github.io/inc-sf2/niji/20171022/sound/033000_Rainbow_20171022.js", "_tone_033000_Rainbow_20171022");
```

WebAudioFont は dtm の `scripts/otomad/tools/sf2-to-webaudiofont.ts` で .sf2 から書き出した（読むのは dtm の外部 SF2 と同じパーサー。同じ鍵に重なるベロシティ違いのゾーンは最初のものだけ残す）。

```
npx tsx scripts/otomad/tools/sf2-to-webaudiofont.ts <in.sf2> <フォルダ>/sound <名前> <フォルダ>/<音源定義>.xml
```

権利はそれぞれの作者にある。**作者様に限らず、どなたからでも、問題をご指摘いただければ、ほかの作者のサウンドフォントは速やかに削除します。** ご連絡は [GitHub の Issues](https://github.com/onjmin/inc-sf2/issues) へお願いします。

## 公開

main へ push すると、`.github/workflows/gh-pages.yml` が `index.html`・`inc.sf2`・`sound/`・`niji/`・`otenkiame/` を GitHub Pages へ出す（dtm と同じ作り）。リポジトリの Settings → Pages → Source を「GitHub Actions」にしておく。
