# 液晶表示 資材一式（素のnice_view_gem版・育成キャラなし）

daiya-f/zmk-keyboard-torabo-tsuki-lp に追加する液晶表示用ファイルのセット。
**育成キャラ・成長要素は含まれていません。** 標準のnice-view-gem v0.3.0のステータス画面
（電池残量・接続状態・WPMグラフ・レイヤー番号・プロファイル）のみです。

## なぜこの版があるか

フル機能版（育成キャラ入り）は実測 UF2 856KB で、お使いのブートローダー（BLEMICROPRO、
実効書き込み上限は683KB〜876KBの間）で書き込みエラーになりました。

この素の版は **UF2 683,008バイト（667KB）で、実機書き込み成功済み**です。
まずはこれで確実に動かし、キャラ機能は容量に余裕がありそうなら段階的に追加していく方針です。

## 追加・上書きするファイル

```
リポジトリルート/
├─ build.yaml                     ← 上書き（central 2エントリに nice_view_gem + display を追加済み）
├─ zephyr/
│   ├─ module.yml                 ← 上書き（Kconfig二重読み込み対策）
│   └─ Kconfig.workspace_stub     ← 新規（module.ymlが参照する空スタブ）
├─ snippets/
│   └─ display/                   ← 新規（SPI1ピン定義・CS active-high・P0.24電源ホグ）
│       ├─ snippet.yml
│       └─ display.overlay
└─ boards/shields/
    └─ nice_view_gem/             ← 新規（nice-view-gem v0.3.0そのまま＋ビルド修正2点のみ）
        ├─ CMakeLists.txt         ← libmリンク追加のみ（1行）
        ├─ nice_view_gem.overlay  ← &spi1 直接参照に修正（1行）
        └─ （他は完全にオリジナルのnice-view-gem v0.3.0）
```

## ビルド確認済み

クリーンな`west init`からのフルビルドで確認済み。

```
FLASH:  341424 B / 712 KB  (46.83%)
RAM:     97962 B / 256 KB  (37.37%)
UF2:    683,008 バイト（667KB）
```

**実機書き込み成功済み**（このサイズで書き込めることを確認済み）。

## プッシュ手順

```bash
cd zmk-keyboard-torabo-tsuki-lp
# このzipの中身をリポジトリルートに展開（build.yaml, zephyr/module.ymlは上書き）
git add build.yaml zephyr/ snippets/ boards/shields/nice_view_gem/
git commit -m "Add memory LCD support (stock nice_view_gem status screen, no growth pet)"
git push
```

Actionsが回ったら `torabo_tsuki_lp_double_ball_right_central`（または`torabo_tsuki_lp_right_central`）
のuf2をcentral側に書き込み。

## keymapに追加する表示操作キー

このスタンダード版は画面が1つだけなので、切り替えキーは不要です。

## 今後：育成キャラ機能を足したい場合

書き込みできる容量の実測データが揃ってきています（683KB成功、856KB失敗）。
これを踏まえて、コマ数を間引いた中間サイズ版（現在764KBのものをテスト中）を段階的に
試すことで、入る範囲まで機能を戻していけます。進捗があれば教えてください。
