---
title: "Neovimのコメンティングプラグインの選び方"
emoji: "🚗"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [Neovim]
published: true
published_at: 2024-09-16
publication_name: vim_jp
---

:::message

本記事は[Vim駅伝](https://vim-jp.org/ekiden/)2024年9月16日の記事です。
前回はkawarimidollさんによる[Vimで使っている簡単キーマッピングたちを共有](https://zenn.dev/vim_jp/articles/43d021f461f3a4)でした。

:::

# コメンティングプラグインとは

この記事ではコメントアウト/アンコメントを手軽に実現するプラグインをコメンティングプラグインと呼びます。

動いている様子を見た方がわかりやすいので、[Reactでよく見かけるソースコード](https://github.com/facebook/create-react-app/blob/dd420a6d25d037decd7b81175626dfca817437ff/packages/cra-template-typescript/template/src/App.tsx)でのデモをご覧ください。

![demo](https://github.com/user-attachments/assets/e0ec4563-ccc7-478a-8944-80e72a23b7a8)

便利そうですね。

# 必要なプラグイン

デモと同じ動作をするためには、2種類のプラグインが必要です。
1つ目はコメンティングプラグイン。
もう1つは文脈に応じてコメント用の文字列(`:h 'commentstring'`)を更新するプラグインです。
デモではTypeScript部分を`//`でコメントアウトし、JSX部分を`{/* */}`でコメントアウトしていることがわかります。

# 結論

解説を始めると長くなるので、先に結論を書きます。

行コメントができれば十分な場合、Neovim組み込みの機能(`:h commenting`)と[ts-comments.nvim](https://github.com/folke/ts-comments.nvim)を組み合わせて使うと良いでしょう。

ブロックコメントなど、より高度な機能が必要な場合は、[nvim-ts-context-commentstring](https://github.com/JoosepAlviste/nvim-ts-context-commentstring)と、対応しているコメンティングプラグイン([Comment.nvim](https://github.com/numToStr/Comment.nvim)など)を選びましょう。

執筆時点の私の設定は以下です。

https://github.com/yuys13/dotfiles/blob/7f414819fac660641f1e7ebd8c2b7d31f38c7e56/home/XDG_CONFIG_HOME/nvim/lua/rc/plugins/nvim-treesitter.lua#L130-L151

<!-- textlint-disable ja-technical-writing/ja-no-mixed-period -->
<!-- textlint-disable ja-technical-writing/sentence-length -->

:::details 私の設定の補足
私はlazyのsetup時に`defaults = { lazy = true }`を指定しているため、 nvim-ts-context-commentstringは`lazy = true`です。
:::

<!-- textlint-enable ja-technical-writing/sentence-length -->
<!-- textlint-enable ja-technical-writing/ja-no-mixed-period -->

# コメンティングプラグインの選び方

プラグインの選び方と言いましたが、まずは`:h commenting`を読みましょう。ここに書いてある機能で十分ならば、プラグインは不要です。
[ts-comments](https://github.com/folke/ts-comments.nvim)をインストールして完成です。

上記で満足できない場合、nvim-ts-context-commentstringの[Configuration](https://github.com/JoosepAlviste/nvim-ts-context-commentstring?tab=readme-ov-file#configuration)に挙げられているプラグインから選びましょう。
私はプロジェクトによって行コメントとブロックコメントを使い分ける必要があったので、[Comment.nvim](https://github.com/numToStr/Comment.nvim)を愛用しています。

## ts-comments.nvimとnvim-ts-context-commentstringの違い

どちらもtreesitterを用いてcommentstringを計算する点は同じです。
ts-comments.nvimは`vim.filetype.get_option`という関数を上書きすることにより、commentstringを取得する際に動的に計算しています。

一方のnvim-ts-context-commentstringはcommentstringの計算機能を提供しています。
デフォルトではautocmdによってcommentstringを更新します。
このプラグインが真価を発揮するのはautocmdを無効化してコメンティングプラグインと組み合わせた時です。
様々なコメンティングプラグインに対する連携機能が用意されており、コメンティングプラグインの豊富な機能と文脈によるcommentstringの変更を両立します。
Neovim組み込みのコメント機能との連携方法も記載されていますが、ts-comments.nvimの一部を自分で実装するのと同義なので、ts-comments.nvimを使う方が良いでしょう。
