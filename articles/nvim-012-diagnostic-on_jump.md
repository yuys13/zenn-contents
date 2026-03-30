---
title: "Neovim 0.12でDeprecatedになったDIAGNOSTICSの設定を更新する方法"
emoji: "🚗"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [neovim]
published: true
---

:::message

本記事は[Vim駅伝](https://vim-jp.org/ekiden/)2026年3月25日の記事です。
3月30日に書いていますが、3月25日の記事です。いいですね。

:::

# はじめに

`:h deprecated-0.12`に以下の記載がありました。

<!-- markdownlint-disable MD010 -->

```txt
DIAGNOSTICS

• "float" in |vim.diagnostic.JumpOpts|.		Use `on_jump` instead.
• "float" in |vim.diagnostic.Opts.Jump|.	Use `on_jump` instead.
```

<!-- markdownlint-enable MD010 -->

なお`:checkhealth vim.deprecated`を実行すると、非推奨になった項目を利用しているか確認できます。

上記の対応をしようとhelpを辿っていきましたが、`float = true`にしていた場合、どうすれば同じ動作になるかわかりませんでした。  
調べた結果を共有します。

# 結論

```lua
vim.diagnostic.config {
  jump = {
    float = true,
  },
}
```

これ(↑)をこう(↓)。

```lua
vim.diagnostic.config {
  jump = {
    on_jump = function(_, bufnr)
      vim.diagnostic.open_float { bufnr = bufnr, scope = 'cursor', focus = false }
    end,
  },
}
```

元々`float`にtrueではなくオプションを指定していた人はわかってる人でしょうから、`open_float`の引数を調整してください。

# どうやって調べたか

実装されたPR見に行きました。
diagnostic.luaで`float`が指定された時の動作が実装されてますね。

https://github.com/neovim/neovim/pull/33850/files

# おまけ

`:LspInfo`が無くなって困っている人へ  
`:checkhealth vim.lsp`になりました。  
どうしても`:LspInfo`が良い人は自前で用意すると良いでしょう。
