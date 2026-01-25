---
title: "Zshの補完をあらゆるエディタで使いたいのでLanguage Serverを自作した"
emoji: "🚗"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [zsh, lsp, vim, neovim, rust]
published: true
published_at: 2026-01-26
publication_name: vim_jp
---

<!-- textlint-disable ja-technical-writing/no-exclamation-question-mark -->

:::message

本記事は[Vim駅伝](https://vim-jp.org/ekiden/)2026年1月26日の記事です。
前回はテヘラニ ファルド スィナさんによる[ストレージ逼迫がきっかけでIDEからNeovimに移行した話](https://scriptlab.jp/neovim-migration-full-guide/)でした。

:::

# はじめに

Zshの補完機能をLSP経由で提供するツール(zshcs)を作成したので、その紹介と開発の背景を共有します。

https://github.com/yuys13/zshcs

使ったりコントリビュートしたりしてくれると嬉しいです。

# 作った経緯

VimやNeovimでは以下のプラグインを愛用してきました。

- **Neovim**
  - [deoplete-zsh](https://github.com/deoplete-plugins/deoplete-zsh)
  - [cmp-zsh](https://github.com/tamago324/cmp-zsh)
- **Vim**
  - [asyncomplete-zsh.vim](https://github.com/yuys13/asyncomplete-zsh.vim)(自作)

環境構築で`.zshrc`を編集するときや、`edit-command-line`でコマンドをテキストエディタから編集する際、Zshの補完機能が使えると便利です。
逆に言えば補完プラグインを選定する際、Zshの補完機能を実現できるという要件が制約になっていました。
もっとフットワーク軽く、様々な補完プラグインやテキストエディタを試せる状況にしたいという思いが出てきました。
最近のテキストエディタはLSP(Language Server Protocol)を利用した補完機能を持つことが多いため、これを実装すれば選択肢が増えると考えました。

fishについても同様ですが、fishは[fish-lsp](https://github.com/ndonfris/fish-lsp)がすでにありました。
作り始めた当時はZshのLSP実装が見当たらなかったので、作りたくなったのです。

# zshcsの実装について

[zshcs](https://github.com/yuys13/zshcs) はRustで実装しました。
作り始めはRustの学習とVibe Codingをやってみるという、複数の未知に同時にチャレンジしてしまったためコードは荒削りです。
それでも一旦使えるものが出来上がりました。

## 補完の仕組み

Zshの補完候補の取得方法は[zsh-capture-completion](https://github.com/Valodim/zsh-capture-completion)に大きく影響を受けています。
ポイントはZshスクリプトでzptyを用いてシェル環境を再現し、実際にZshの補完関数を実行して候補を生成していることです。

# 今後の展望と課題

とりあえず使えるものはできましたが、まだ改善の余地があります。

- サーバー化による高速化
  - [ddc-source-shell_native](https://github.com/Shougo/ddc-source-shell_native)のようにZshプロセスをサーバー化する
    - 現在は`textDocument/completion`毎にZshスクリプトのプロセスを起動している
- 様々なユーザー環境への適応
  - fpathを追加で指定可能とする

もし興味のある方がいれば、コントリビュートしていただけると非常に嬉しいです。

# おわりに

新しいテキストエディタや補完プラグインが出るたびにZshの補完ソースを作成するとタイムラグがありますし、世の中にZshのLSP実装があることには価値があると考えています。
(当然各ソフトウェアに特化した補完ソースにも価値がある)
Rustのコードをブラッシュアップ出来る人、Zshのスクリプトをブラッシュアップ出来る人などのコントリビュートをお待ちしております。
もっと良いもの作ったよ、という人も教えてくれると嬉しいです！

https://github.com/yuys13/zshcs
