---
title: "忙しい人向け nvim-lspconfigのnvim v0.11対応"
emoji: "🚗"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [neovim,lsp]
published: false
---

:::message

本記事は[Vim駅伝](https://vim-jp.org/ekiden/)2025年4月23日の記事です。
前回はmonaqaさんによる[Neovim のキーマップ設定をより便利にする DSL]でした。

:::

# はじめに

Neovim 0.11でLSP周りに大きな変更が加わりました。

- runtimepath内のlspフォルダにLanguage Serverの設定ファイルを置けるようになった
- `vim.lsp.config`で設定を上書きできるようになった
- `vim.lsp.enable`でLanguage Serverを有効化できるようになった

これまではnvim-lspconfigの各Language Serverの`setup`関数で実現していましたが、Neovim本体の機能で設定と有効化が可能になりました。
さらに、nvim-lspconfigは`setup`関数の更新を凍結したため、いずれ新方式で書き換える必要があります。

急いでいない方は以下の記事を参考に設定してください。

https://zenn.dev/kawarimidoll/articles/b202e546bca344

# 急いで書き換える

## 共通の設定を書き換える

全てのLanguage Serverに共通の設定は以下のように記載できます。

```lua
vim.lsp.config('*', {
  -- 共通設定
})
```

nvim-cmpのcapabilitiesを設定する場合は以下のように記載します。

```lua
vim.lsp.config('*', {
  capabilities = require('cmp_nvim_lsp').default_capabilities(),
})
```

これで各Language Serverの`setup`でcapabilitiesを設定する必要がなくなりました。

:::message

Neovim本体の補完もLSPに対応したため、capabilitiesを更新しなくても補完が可能になりました。
ただし、nvim-cmpのdefault_capabilitiesとは差分があるため、nvim-cmpを使う場合は適用することをお勧めします。

:::

## 各Language Serverの設定を書き換える

以下のような`setup`を書き換えます。
`language_server_name`の部分は`lua_ls`や`rust_analyzer`などに適宜読み替えてください。

```lua:init.lua
require'lspconfig'.language_server_name.setup {
  settings = {
    awesome_setting = 'awesome'
  }
}
```

`setup`関数の引数を返却するファイルを作成します。場所は`.config/nvim/after/lsp/language_server_name.lua`です。

```lua:.config/nvim/after/lsp/language_server_name.lua
return {
  settings = {
    awesome_setting = 'awesome'
  }
}
```

忘れずに`vim.lsp.enable`を実行してください。

```lua:init.lua
vim.lsp.enable('language_server_name')
```

## mason-lspconfigのsetup_handlersで有効化していたLanguage Serverの書き換え

mason-lspconfigの`setup_handlers`関数で設定していた場合、設定部分はこれまでの記載内容で書き換えることが可能です。
あとは有効化ができれば書き換え完了です。
`vim.lsp.enable`はtableを受け取ることができるため、以下を追記してください。

```lua:init.lua
vim.lsp.enable(require('mason-lspconfig').get_installed_servers())
```

## lazydev.nvimを使っているけど移行しても大丈夫か

大丈夫。

## nodeとdenoを自動で切り分けて起動したい

冒頭で紹介した[kawarimidollさんの記事](https://zenn.dev/kawarimidoll/articles/b202e546bca344)を参考に、自動で切り分けてください。
もしくは`.nvim.lua`でプロジェクトごとに設定するのも良いでしょう。
`.nvim.lua`については`:h exrc`でヘルプを参照してください。

# おわり

書き換える前は新しく何かができるわけでもないので非常に億劫でした。
しかし、これまで密結合だったLSP関連のリポジトリの役割が明確になり、素晴らしい変更だと感じています。

- Neovim本体でプラグインなしでもLSPサポート
- lspconfigは各種Language Serverの共通設定(実行ファイル名やファイルタイプなど)
- masonはLanguage Serverなどのツールをインストールしてパスを通す
- mason-language-serverはmasonでインストールするLanguage Serverのためのツール群

新たな入門記事が待たれます。
