---
title: "NeovimのDiagnosticのfloating windowの中に入る"
emoji: "🚗"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [neovim]
published: true
published_at: 2025-01-03
publication_name: vim_jp
---

:::message

本記事は[Vim駅伝](https://vim-jp.org/ekiden/)2025年1月3日の記事です。

<!-- 前回はKlehaさんによる[NeoVimで書き初め(という名の書類作成)をする]()でした。 -->

:::

# はじめに

NeovimのDiagnosticはLanguage Serverの診断情報等を表示してくれる機能です。
[none-ls](https://github.com/nvimtools/none-ls.nvim)や[nvim-lint](https://github.com/mfussenegger/nvim-lint)を使ってlinterの結果を表示している人も多いでしょう。
そんな人達向けのTipsを紹介します。

この記事はNeovim v0.10.2時点で動作確認をしています。

# 入り方

`vim.diagnostic.open_float`関数を実行すると診断結果をfloating windowで表示できます。
デフォルトでは`<C-w>d`にマッピングされています。

このfloating window表示中にもう一度`<C-w>d`を押すとfloating windowの中に入ることができます。
`q`でfloating windowをcloseできます。
(`<C-w><C-w>`などのwindow移動コマンドで脱出しても自動でcloseします)

以上です。

# 応用

以下がfloating windowの中に入った時の様子です。

![foating windowに入った様子](https://github.com/user-attachments/assets/4d03e85f-d925-40ec-924a-8379812ccbfe)

この時、`vi[`を押すとどうなるでしょうか。  
こうなります。

![診断コードを選択した様子](https://github.com/user-attachments/assets/126abf3c-3374-4ead-a4dd-6f40ec139a05)

`i[`は`[]`の中を示すテキストオブジェクトなので、診断コードを選択できます。  
たとえばtextlintでこのルールを無効化する場合、以下の記載が必要です。

```md
<!-- textlint-disable ja-technical-writing/no-exclamation-question-mark -->
```

長い。
長いですが、無効化するルールについてはfloating windowの中に入って`yi[`でコピーできるので、簡単に記載することが出来ます。

Code Actionで無効化できると楽ですが、Code Actionが提供されていない場合も便利に無効化できます。

:::message

診断結果の無効化は用法容量を守ってお使いください。

:::

また[open-browser.vim](https://github.com/tyru/open-browser.vim)と組み合わせることで、診断コードをwebで検索可能です。
診断コードに対する修正方法を検索できて便利です。

# おまけ

## Diagnosticのfloating windowの表示をカスタマイズする

`vim.diagnostic.config()`を使って表示方法をカスタマイズできます。  
設定可能な項目は`:h vim.diagnostic.Opts`で調べる事ができます。

私は記事執筆時点で以下の設定をしています。

```lua:init.lua
vim.diagnostic.config {
  severity_sort = true,
  float = {
    -- source = 'if_many',
    border = 'single',
    title = 'Diagnostics',
    header = {},
    suffix = {},
    format = function(diag)
      if diag.code then
        return string.format('[%s](%s): %s', diag.source, diag.code, diag.message)
      else
        return string.format('[%s]: %s', diag.source, diag.message)
      end
    end,
  },
}
```

↑の設定で表示は↓となります。

![設定させていただきありがとうございます](https://github.com/user-attachments/assets/d9f9dad4-cd73-4798-8e7d-bc0d9e339c27)
