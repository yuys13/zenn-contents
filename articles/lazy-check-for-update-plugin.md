---
title: "lazy.nvimで差分を確認してからアップデートする"
emoji: "🚗"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [neovim]
published: true
published_at: 2025-12-12
publication_name: vim_jp
---

:::message

本記事は[Vim駅伝](https://vim-jp.org/ekiden/)2025年12月12日の記事です。
前回はtaniさんによる[生成AI時代だからこそ、Vim as an IME](https://zenn.dev/dog/articles/vim-as-an-ime)でした。

:::

# はじめに

プラグインをアップデートしたら動かなくなった、という経験はありませんか。
特に仕事等の作業前にアップデートして動かなくなると焦りますよね。
万が一動かなくなってしまった場合は `lazy-lock.json` を使って復元できますが、今回はトラブルを未然に防ぐ方法を紹介します。

# `:Lazy check`で更新内容を確認する

`:Lazy check` を実行してください。もしくは既に lazy.nvim の画面を開いている場合は `C` をタイプします。
すると、以下のようにアップデート可能なコミットの一覧が表示されます。

![:Lazy checkの画面](https://storage.googleapis.com/zenn-user-upload/f413d1d5161d-20251210.png)

プラグインが[Conventional Commits](https://www.conventionalcommits.org/ja/v1.0.0/)に従っている場合、画像のように破壊的変更（Breaking Changes）が含まれているとそれを明示してくれます。
コミットにカーソルを合わせて `d` をタイプすると、コミットの内容（差分）が確認できます。

![commitの確認](https://storage.googleapis.com/zenn-user-upload/c55d54b4ff6e-20251210.png)

このように、アップデートによって適用される変更内容を事前に確認できます。

# プラグインを一つずつアップデートする

`:Lazy check` で更新内容を確認し、変更内容に問題なさそうであれば、プラグインにカーソルを合わせて `u` をタイプして更新します。
設定の変更が必要そうなプラグインは後回しにし、時間のある時に更新すると良いでしょう。

# lazy.nvimの操作方法を忘れたら

`?` をタイプするとキーマップ（操作方法）が表示されます。

![:Lazy helpの画面](https://storage.googleapis.com/zenn-user-upload/3a4880860d7a-20251210.png)

# それでもアップデートして壊れたら

`lazy-lock.json` を正常に動作していた時点の状態に戻し、`:Lazy restore` しましょう。
もしくは気合で修正しましょう。

# 余談

なお、この記事を書いてる最中に、何も考えずEmacsをアップデートしようとして、以下にハマりました。

https://github.com/d12frosted/homebrew-emacs-plus/issues/854

確認は大事。
