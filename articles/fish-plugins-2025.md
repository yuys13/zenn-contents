---
title: "fishプラグイン紹介2025年1月版"
emoji: "🚗"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [fish]
published: true
---

# はじめに

fishの設定をしたので情報共有します。
[ドキュメント](https://fishshell.com/docs/current/tutorial.html#startup-where-s-bashrc)の思想通りにファイル分割していると、プラグイン化されました。

一通り紹介していくので、欲しい機能があったら使ってください。

プラグインマネージャーはfisherを使っています。

https://github.com/jorgebucaran/fisher

# プラグイン紹介

## `cd`したら自動で`ls`する

個人的な必須機能その1。
私がshellを設定するキッカケとなった機能です。

https://github.com/yuys13/fish-autols

`ls`の代わりに`eza`を使いたい場合は以下で変更可能です。

```fish
set -U autols_cmd eza
```

:::details zshでの実現方法。

zshだとこんな感じに設定した内容を実現するプラグインです。

```zsh
__list_directory_contents () {
    if [[ -o interactive ]]; then
        ls
    fi
}
add-zsh-hook chpwd __list_directory_contents
```

:::

## `ghq list`した結果を`fzf`で絞り込んで`cd`する

個人的な必須機能その2。

https://github.com/yuys13/fish-ghq-fzf

`Ctrl + g`でリポジトリ一覧が`fzf`で表示されるので、選択したら`cd`します。
これが無いと生きていけない。

元々以下を使っていたのですが、autolsの描画とpromptの描画が干渉してしまうので、必要な部分のみ自作することにしました。

https://github.com/decors/fish-ghq

## `$fish_user_paths`を$EDITORで編集する

fishインストール直後しか使わないけど、お気に入りプラグイン。

https://github.com/yuys13/fish-pathed

所謂PATHを通す所作、fishでは`$fish_user_paths`で指定しますよね。
`set`で順番入れ替えたりするの大変じゃないですか。
`pathed`を実行すると`$VISUAL`もしくは`$EDITR`が開きます。
内容は`$fish_user_paths`の要素を改行区切りで表示しますので、自由に編集して保存して閉じてください。
私はファイルパスの補完を有効にしたvimで編集します。

最近は`$fish_user_paths`限定ではなく、汎用配列操作プラグインが欲しくなってきました。

## 過去に訪ずれたことのあるディレクトリを`fzf`で選択して`cd`

`cdf`で実行する。名付けセンス無くてすみません。ドラクエで言うルーラです。
たまに使う。無いと結構困る。

https://github.com/yuys13/fish-cdf

:::details zshでの実現方法。

組込みでディレクトリを記録する機能がある。

```zsh
autoload -Uz chpwd_recent_dirs cdr
add-zsh-hook chpwd chpwd_recent_dirs
zstyle ':chpwd:*' recent-dirs-default true
zstyle ':chpwd:*' recent-dirs-file ${XDG_DATA_HOME}/zsh/chpwd-recent-dirs
zstyle ':chpwd:*' recent-dirs-max 500
# zstyle ':chpwd:*' recent-dirs-prune parent
zstyle ':chpwd:*' recent-dirs-pushd false
zstyle ':completion:*:*:cdr:*:*' menu selection
zstyle ':completion:*:*:cdr:*:*' recent-dirs-insert both
```

すると`cdr -l`で一覧が出せるので`fzf`で絞り込んで`cd`すれば良い。
(番号も一緒で出てくるので`cdr`にその番号渡しても良い)
↓な感じ。

```zsh
function cdf () {
    clean-chpwd-recent-dirs
    local dir=($(cdr -l | fzf --preview 'f() { sh -c "$FZF_CDR_PREVIEW_OPTS ${@}" }; f {2..}'))
    if [[ -n $dir ]]; then
        cdr "${dir[1]}"
    fi
}
```

ちなみに履歴を綺麗にする関数として以下を用意しているが、ちょっと何やってるかわからない。

```zsh
clean-chpwd-recent-dirs () {
    emulate -L zsh
    setopt extendedglob
    local -aU reply
    integer history_size
    autoload -Uz chpwd_recent_filehandler
    chpwd_recent_filehandler
    history_size=$#reply
    reply=(${^reply}(N))
    (( $history_size == $#reply )) || chpwd_recent_filehandler $reply
}
```

:::

## 親(の親(の親(……)))ディレクトリを`fzf`で絞り込んで`cd`

あまり使わないけど無いとストレス。

https://github.com/yuys13/fish-fzf-bd

`bd`関数を提供しています。
階層構造のどこまで戻るのかを視覚的に把握しやすいのがお気に入りポイントです。

![fish-bd画像](https://github.com/user-attachments/assets/be03b386-3010-4c66-9bf4-ff0972af4cd0)

これはzshで自作していたのでそのままfishに移植しました。

別解として以下もあります。`bd`の補完で移動先を選べるのですが、私にはどこまで戻るのか判断が難しかったので↑を使うことにしました。

https://github.com/0rax/fish-bd

## `git`のリポジトリルートに`cd`

うん、「また」なんだ。済まない。
`cd`ネタが続きます。
基本的にはリポジトリルートから動かざること山の如しなのですが、稀に使います。

https://github.com/yuys13/fish-gcd

## `fzf`のshell連携をする

fzfの[Setting up shell integration](https://github.com/junegunn/fzf?tab=readme-ov-file#setting-up-shell-integration)を実行するだけです。
流石に不要ではと思いましたが、引っ込みつかなくなったので自作しました。
将来別のシェル統合プラグインを使う際、容易に無効化するためプラグイン化したフシがあります。

https://github.com/yuys13/fish-fzf

なんと中身は以下だけです。

```fish
command -q fzf
and fzf --fish | source
```

以前は↓を使ってました。使いはじめた頃、fzf側にfishのintegrationは無かった気がします。

https://github.com/jethrokuan/fzf

色々ついてるけど持て余しているのでfzf本体の機能を使う事にしました。

最近は↓の方が更新されていてスターも多いです(使った事はない)。

https://github.com/PatrickF1/fzf.fish

## プロンプト

今はtideを使っています。[powerlevel10k](https://github.com/romkatv/powerlevel10k/)のfish版です。

https://github.com/IlanCosman/tide

`tide configure`で設定できるのも`p10k`と一緒ですね。
こちらはfishらしくユニバーサル変数に設定が保持されます。
Leanや尖った部分を消したClassicが好みです。

fishっぽいテーマと言えばbobthefishでしょうか。

https://github.com/oh-my-fish/theme-bobthefish

pureも好きです。

https://github.com/pure-fish/pure

# おわり

fishはデフォルトで使いやすいので、痒いところに手が届くプラグインを探していたら自作していました。
結局プロンプト以外は全て自作してしまいました。

今は`fish_hybrid_key_bindings`が気になっています。
Vimmerですがshellはemacsキーバインド派です。
デフォルトemacsキーバインドで必要な時だけnormalモードを使える部分に期待しています。
