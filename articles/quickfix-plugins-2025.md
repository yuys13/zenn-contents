---
title: "Vim/Neovimでgrep結果をいい感じにフィルタリングして一括編集する~quickfixにプラグインを添えて~"
emoji: "🚗"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [vim, neovim]
published: true
published_at: 2025-02-19
publication_name: vim_jp
---

:::message

本記事は[Vim駅伝](https://vim-jp.org/ekiden/)2025年2月19日の記事です。

:::

# はじめに

grepした結果を一括で編集する方法はたくさんあります。
しかしgrepした結果を確認すると「編集したくない対象が紛れ込んでおり、それを除外するために正規表現を考えなおす」なんて経験はありませんか。
今回は雑にgrepした結果を直接編集して絞り込み、絞り込んだ対象を一括編集する方法を紹介します。

色々ありますが、今回はquickfixというVimの組み込み機能をプラグインで拡張して実現する方法を紹介します。

# 結論

Neovimの場合は以下のどちらかの組み合わせを使いましょう。

- [quicker.nvim](https://github.com/stevearc/quicker.nvim) + [nvim-bqf](https://github.com/kevinhwang91/nvim-bqf)
- [vim-qfreplace](https://github.com/thinca/vim-qfreplace) + [vim-qfedit](https://github.com/itchyny/vim-qfedit) + [nvim-bqf](https://github.com/kevinhwang91/nvim-bqf)

Vimの場合は[vim-qfreplace](https://github.com/thinca/vim-qfreplace)と[vim-qfedit](https://github.com/itchyny/vim-qfedit)を使いましょう。

# 操作の全体像

操作の流れは以下です。

1. grepした結果をquickfixで表示する
1. quickfixの結果から除外したいものを消す
1. quickfixの結果を編集して一括適用する

今回は私のZennの全ての記事の先頭に「車アイコンによる、」という文言を付けたくなったという想定で説明します。

それでは順番に見ていきましょう。

# grepした結果をquickfixで表示する

様々な方法がありますが、プラグインを利用するケースと利用しないケースをひとつずつ紹介します。

## `:Telescope live_grep`を利用する(Neovimのみ)

Neovimユーザーに大人気の[telescope.nvim](https://github.com/nvim-telescope/telescope.nvim)を使った方法です。
`:Telescope live_grep`を実行し、`title`という文字列を検索するとこんな感じです。

![:Telescope live_grepした結果](https://github.com/user-attachments/assets/b9b52176-d07a-4c78-a08d-152a0307ccfd)

この状態で`<C-q>`を押してみましょう。

![はじめてのquickfix](https://github.com/user-attachments/assets/b78a0832-f422-4a6d-91c3-b3c52e2520f0)

画面下部にあらわれたのがquickfixです。
quickfixでEnterを押すと該当箇所にジャンプできます。
ただquickfixは編集不可なので、このままでは編集できません。

## `:grep`を使う

Vimには`:vimgrep`と外部のgrepプログラムを利用する`:grep`があります。
2025年現在、grepと言えば[ripgrep](https://github.com/BurntSushi/ripgrep)を使いたいので、`:grep`を使います。

`:grep title | copen`を実行すると以下のようにgrepの結果が表示され、hit-enter-promptが表示されます。

![:grepの結果](https://github.com/user-attachments/assets/1549c1e6-c78a-445b-86af-1a18da35c538)

ここでEnterを押すとquickfixが表示されます。

### `:grep`で`ripgrep`を使う

`:grep`は`'grepprg`の値をgrepプログラムとして実行します。
その結果を`'grepformat'`の値に従って解析します。

つまり`'grepprg'`と`'grepformat'`を`ripgrep`用に設定すれば良いです。

Neovimは`ripgrep`がインストールされていればデフォルトで`'grepprg'`と`'grepformat'`を設定します。
を`rg --vimgrep -uu`にします。
ripgrepのオプションを変えたい場合、`'grepprg'`を変更すると良いです。
私は`-uu`の部分を変えたり、`--smartcase`を付けています。

Vimの場合は以下のように自分で設定する必要があります。

```vim:.vimrc
if executable('rg')
  let &grepprg = 'rg --vimgrep --smart-case --hidden'
  set grepformat=%f:%l:%c:%m
endif
```

`ripgrep`のオプションはお好みにあわせて編集してください。

:::message

Vimのオプションは`set`で設定可能ですが、スペースはエスケープ処理が必要です。
オプション名の先頭に`&`を付けることで変数として扱えるので、`let &grepprg =`でスペースをエスケープすることなく`grepprg`を設定しています。

:::

# `vim-qfreplace` + `vim-qfedit`構成の場合

## quickfixの結果から除外したいものを消す(`vim-qfedit`編)

quickfixは編集不可能なので、普通は消せません。
しかし`vim-qfedit`を不要な行を消すことができます。

https://github.com/itchyny/vim-qfedit

さきほどの例ではpackage-lock.jsonの結果が不要なので、試しに`dd`で一行消してみましょう。

![はじめてのquickfix編集](https://github.com/user-attachments/assets/aa355e59-5880-45cd-884a-9a15208ffaa9)

凄い、消えてます。
それではZennの記事のtitle以外を全て消してしまいましょう。

![qfeditで結果整理](https://github.com/user-attachments/assets/8d5d2f23-6526-41aa-87d9-2f44f28694d8)

これでgrepする正規表現などを工夫せず、Zennの記事タイトルの行だけの一覧を得ることができました。楽ちん。

## quickfixの結果を一括編集する(`vim-qfreplace`編)

それではZennの記事のtitleを一括で編集します。

https://github.com/thinca/vim-qfreplace

`:Qfreplace`コマンドを実行すると編集バッファが表示されます。

![はじめてのqfreplace](https://github.com/user-attachments/assets/18d295e0-9e1c-4983-ac4a-12ca1e25c4b9)

真ん中のバッファがqfreplaceバッファです。
ここを編集して保存してみましょう。

![qfreplaceで一括編集！](https://github.com/user-attachments/assets/e9223441-019f-4aff-b088-ff906d8f7cb8)

これをpushすれば、私の全ての記事タイトルが「車アイコンによる、」から初まります。
~~やりませんよ~~

# `quicker.nvim`構成の場合(Neovimのみ)

## quickfixの結果から除外したいものを消す(`quicker.nvim`編)

`quicker.nvim`はquickfixの見た目をリッチにします。また通常のバッファと同様に編集可能とし、保存時に実態へ反映させるプラグインです。
[oil.nvim](https://github.com/stevearc/oil.nvim)の作者が作っているので、思想が似ていますね。

https://github.com/stevearc/quicker.nvim

quicker.nvimをインストールするとquickfixは以下のように表情を変えます。

![はじめてのquicker.nvim](https://github.com/user-attachments/assets/f9ddeda1-ae6d-4fae-b819-1e3e7c2f17c0)

ファイル名、行数、検索結果の開始位置が揃うので見やすいですね。
qfeditと同様に、不要な行を削除していきましょう。

![quicker.nvimで候補選別](https://github.com/user-attachments/assets/db097af0-0396-4e2d-aba5-200599a41263)

できました。

## quickfixの結果を一括編集する(`quicker.nvim`編)

直接バッファを書き換えて保存します。

![quicker.nvimで一括編集](https://github.com/user-attachments/assets/29155091-d1a6-4c00-b22e-7bc78e857020)

こちらはqfreplaceと違って直接quickfix上で編集します。保存すると適用される点は同一です。
直接編集したいか、安全に別バッファで編集したいかは好みが別れる部分です。
どちらも試してみて、お好みの方をお使いください。

# `nvim-bqf`でquickfixのジャンプ先のpreviewを表示する

quickfixの結果だけではなく、周辺コードも確認したことが多いです。
`nvim-bqf`を利用するとpreviewが表示されます。

https://github.com/kevinhwang91/nvim-bqf

インストールした様子は以下。

![はじめてのnvim-bqf](https://github.com/user-attachments/assets/251ccb59-c0ea-4e6e-a1e2-3b6432e04152)

grep結果の妥当性確認の際、実際にジャンプせずに判断できることも増えました。
`vim.diagnostic.setqflist()`で診断結果をquickfixで表示した際の見通しも良くなります。

ただしデフォルトでいくつかマッピングするので注意が必要です。
特に`quicker.nvim`と組合せる場合は`<C-v>`の矩形選択は良く使うので、私は以下のように無効化しています。

```lua
{
  'kevinhwang91/nvim-bqf',
  ft = 'qf',
  config = function()
    require('bqf').setup {
      auto_enable = true,
      func_map = {
        vsplit = '',
      },
    }
  end,
},
```

# 気を付けること

`quicker.nvim`と`vim-qfedit`は完全に競合するため、どちらか一方をインストールしましょう。
`quicker.nvim`でquickfixを編集した後に`vim-qfreplace`を使うとエラーが発生します。
`quicker.nvim`を使う場合、quickfixの編集は`quicker.nvim`に統一しましょう。
という事で結論に到達しました。

`vim-qfedit`でquickfixを編集した場合、ただちに`nvim-bqf`のpreviewが更新されます。
`quicker.nvim`でquickfixを編集した場合、バッファの保存をするまで`nvim-bqf`のpreviewがズレます。
`quicker.nvim`は通常のバッファと同様の操作感を提供し、保存操作で編集した内容を適用するという思想のためと考えられます。

`nvim-bqf`を併用してquickfixを編集する場合、keymapが競合する場合はマッピングを削除するか、他のキーに移動しましょう。

# さいごに

昔からのVimmerにとってquickfixはお馴染の機能ですが、最近使い初めた人にも是非触ってみて欲しいです。
LSの診断結果の閲覧についても以前から[trouble.nvim](https://github.com/folke/trouble.nvim)を使ってリッチに確認できました。
今では`vim.diagnostic.setqflist()`と`nvim-bqf`の組み合わせも選択肢に入ると考えています。
専用プラグインの能力も魅力的ですが、quickfixという共通IFを活用することで、プラグイン毎に操作方法を覚える必要が無いことも、また魅力と感じています。
