---
title: "WezTermでハマりがちな設定"
emoji: "🏎️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [wezterm]
published: true
---

# WezTermの設定を書く

本記事ではWezTermでハマりがちな設定と、対処方法を記載します。

まだ設定ファイルを書いていない方は以下のQuick Start読んで書きましょう。

https://wezfurlong.org/wezterm/config/files.html

# IMEで日本語を入力できない

use_imeがtrueになっている事を確認してください。

```lua:wezterm.lua
config.use_ime = true
```

[use_imeの詳細](https://wezfurlong.org/wezterm/config/lua/config/use_ime.html)

# macOSでIMEが有効な時にCtrlキーがうまく動作しない

以下の設定を追加してください。

```lua:wezterm.lua
config.macos_forward_to_ime_modifier_mask = 'SHIFT|CTRL'
```

[macos_forward_to_ime_modifier_maskの詳細](https://wezfurlong.org/wezterm/config/lua/config/macos_forward_to_ime_modifier_mask.html)

# `Ctrl + Q`を2回押す必要がある

以下の設定を試してみてください。私は以下で動作しています。

```lua:wezterm.lua
config.keys = {
  { key = 'q', mods = 'CTRL', action = wezterm.action { SendString = '\x11' } },
}
```

[関連issue](https://github.com/wez/wezterm/issues/2630)

# さいごに

OSやWezTermのバージョンによって事象が発生したりしなかったりします。
問題が発生した際に本記事があなたのお役に立てれば幸いです。

## 余談

IMEのCtrlが効かない問題、ビックリしますよね。
昔は設定不可能で、どうしてもCtrlがIMEに渡らなかった時期がありました。
vim-jp Slackで[monaqaさん](https://zenn.dev/monaqa)がWezTermを自前ビルドして解決したと聞き、私も修正に挑戦してみました。

当時vim-jp SlackではWezTermが流行の兆しを見せており、入門者が現れてはCtrlキーが効かないという話になりました。
意気揚々とpatchをポストした訳ですが、PRした方が良いという話になりました。
自分のアイディアでは無いので気が引けると言ったところ、monaqaさんからも背中を押して頂きPRを作成しました。

https://github.com/wez/wezterm/pull/2435

PR自体はcloseされているのですが、作者のwezさんが意図を汲み取って、より綺麗なコードをmainブランチに入れて頂けました。
さっそく動作確認をし、喜びの報告をしました。

WezTermの修正案の発案者のmonaqaさん、PRの相談に乗っていただいたvim-jp Slackのみなさん、ありがとうございました。

### ところが

日本語IMEのための修正を入れた結果、Macの特定のキーボードレイアウトの動作を壊してしまったようです。

https://github.com/wez/wezterm/issues/2771

その結果、誕生したのが`macos_forward_to_ime_modifier_mask`です。
普通こんな設定不要でしょ、と感じるかも知れませんが、日本語IME利用者の普通は万人の普通では無いでしょう。
私も今回の対応を通して、知らない言語の入力環境、知らないので対応できないという事を痛感しました。

日本語IMEを含めた多言語環境をサポートしてくださったwezさん、設定させて頂きありがとうございます。
