---
title: "Neovimが入れ子になるのを防ぐ"
emoji: "🚗"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [Neovim]
published: true
published_at: 2024-10-02
publication_name: vim_jp
---

:::message
本記事は[Vim駅伝](https://vim-jp.org/ekiden/)2024年10月2日の記事です。
前回はthincaさんによる[Meguro.vim #25 を開催しました](https://thinca.hatenablog.com/entry/2024/09/megurovim-25)でした。
:::

# はじめに

Noevimのターミナル便利ですよね。
しかし、うっかり`git commit`や`kubectl edit`などをしてしまい、Neovim内のTerminal内のNeovimが立ち上がり、上手くEscできないという状況に陥りませんか。
私は陥りました。

私が行なっている対策について共有します。

# 動作イメージ

![demo](https://github.com/user-attachments/assets/0c4db65a-a911-4849-bdc4-64cafee242e1)

`git commit`をすると新しいバッファが開く。通常のTerminal使用時と同じように、Neovim内のTerminalもバッファを閉じるまでブロックします。
便利ですね。
(バッファが開く位置はお好みにあわせて調整可能です)

# 事前準備

neovim-remoteを使います。

https://github.com/mhinz/neovim-remote

各々のパッケージマネージャでインストールすると良いでしょう。

Macの場合はbrewでインストール可能です。

```bash
brew install neovim-remote
```

Arch Linuxの場合はAURを利用します。

```bash
yay -S neovim-remote
```

nixはパッケージが用意されています。

https://search.nixos.org/packages?channel=24.05&show=neovim-remote&from=0&size=50&sort=relevance&type=packages&query=neovim-remote

お使いのパッケージマネージャにneovim-remoteが無い場合、[pipx](https://github.com/pypa/pipx)を使ってインストールすると良いです。

```bash
pipx install neovim-remote
```

# Neovimの設定

`init.lua`をお使いの場合は以下を追記します。

```lua:init.lua
if vim.fn.executable('nvr') == 1 then
  vim.env.EDITOR = 'nvr -cc split -c "set bufhidden=delete" --remote-wait'
end
```

`init.vim`をお使いの場合は以下を追記します。

```vim:init.vim
if executable('nvr')
  let $EDITOR = 'nvr -cc split -c "set bufhidden=delete" --remote-wait'
endif
```

`split`の部分を`vsplit`にすると分割方向を変更できます。
また現在のバッファの右側に新しいバッファを開きたい場合は以下のように設定してください。

```vim:init.vim
if executable('nvr')
  let $EDITOR = 'nvr -cc "rightbelow vsplit" -c "set bufhidden=delete" --remote-wait'
endif
```

`rightbelow`を付けると`split`や`vsplit`の位置を逆に出来るのですが、-ccのあとが複数の単語になってしまうので、ダブルクォートでくくるのがポイントです。

なお`tabnew`にしたい場合は専用のオプションがあるため、以下のように設定できます。こちらの方が通常のターミナルの挙動に近いかも知れませんね。

```vim:init.vim
if executable('nvr')
  let $EDITOR = 'nvr -c "set bufhidden=delete" --remote-tab-wait'
endif
```

# 仕上げ `vi`も`${EDITOR}`で置き換える

ここまで設定すれば、不意にNeovimが入れ子になることは防げます。
ただし自分で明示的にNeovimを起動した場合は入れ子になってしまいます。
これはshell側で対策します。

私は普段`vi`を`nvim`のaliasとして使っているので、これを`$EDITOR`に書き換えます。

zshでは以下を設定しています。

```sh:~/.zshrc
if [[ -n ${EDITOR} ]]; then
    alias vi=${EDITOR}
fi
```

fishでは以下の関数を用意しています。

```fish:~/.config/fish/functions/vi.fish
function vi --wraps='$EDITOR' --description 'alias vi $EDITOR'
    if test -z "$EDITOR"
        command vi $argv
    else
        eval $EDITOR $argv
    end
end
```

力技💪ですね。

# プラグインで解決する

ここまでプラグインを使わない方法の説明をしてきましたが、いつのまにかプラグインが沢山出来ていました。
以下の記事にまとまってます。

https://zenn.dev/notomo/articles/neovim-plugin-to-avoid-nested

guise.vimのREADMEにも類似プラグインがたくさん記載されているので、プラグイン選びの際は目を通すと良いでしょう。

https://github.com/lambdalisue/vim-guise?tab=readme-ov-file#similar-projects

# 余談

## neovim-remoteは生きていた

良かった。

https://github.com/mhinz/neovim-remote/issues/169#issuecomment-1117067808

## Neovim本体の`--remote-wait`対応状況

neovim-remoteは当初Neovimに実装されていなかった`:h client-server`を実現しています。
のちほどNeovimに`--remote`は実装されたのですが、`--remote-wait`などは実装されませんでした。そのため「新しく開いたバッファを閉じるまでTerminalをブロックする」という動作が実現できていません。

ところがNeovimでは`--remote-x-y`のようなオプションを整理しようという動きがあります。
基本的には`--remote`に統一し、デフォルトでTerminalをブロックしようとしています。
詳細は以下のPull Requestをご確認ください。

https://github.com/neovim/neovim/pull/18414

ちなみにNeovim 1.0の準備として以下のissueにまとまっています。

https://github.com/neovim/neovim/issues/20451

楽しみですね。
