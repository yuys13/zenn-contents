---
title: "VimConf2024で登壇してきました"
emoji: "🚗"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: [vim, neovim, vimconf]
published: true
publication_name: vim_jp
---

<!-- お気持ちなのでビックリもするわ！許せ -->
<!-- textlint-disable ja-technical-writing/no-exclamation-question-mark -->

# これは何？

[VimConf2024で登壇してきた人間](https://vimconf.org/2024/sessions/#link-yuys13)の発表直後のお気持ち表明です。
こういうイベントに参加したことすら無いのに、勢いで発表してきました。
伝えたいことは以下です。

- 全ての人に感謝を
- イベントに参加しよう
- 新しいことにチャレンジしよう

伝えたいことは以上です。ここから先、伝えたいことではなくて言いたいことです。
VimConf2024に参加した熱を吐き出さないと、VimConf2024を終われないのです。
ここまで読んでくれてありがとうございました。

# 感謝

貴重な経験をできた事が嬉しく、みなさまには感謝しかありません。

## [スポンサーのみなさま](https://vimconf.org/2024/#menu-sponsors)

我々の活動に理解を示し、金銭的な支援をして頂いたことに感謝いたします。
我々のようなプライベートな時間をエンジニアリングに費す人々に対して理解がある素晴しい会社と理解しています。
VimConfのスポンサーをしたことで、会社のファンが増えて更なる発展をすることを願っています。
私はファンになりました！
VimConfが開催できたのはスポンサーのみなさまのおかげです。
ありがとうございました。

## [個人スポンサーのみなさま](https://vimconf.org/2024/#menu-ind-sponsors)

チケット買ったのにさらにスポンサーしてくれた人も、参加しないのにスポンサーしてくれた人もありがとうございました。
どうしてもお金はかかってしまうので、個人で直接貢献することは素晴しいことだと考えています。
ありがとうございました。

## [他の発表者のみなさま](https://vimconf.org/2024/sessions/)

感極まる話から爆笑もんの話まで、各々が自分にしか出来ない話をしてくれました。
参加者の交流が目的といえど、やはり発表がメインコンテンツです。
VimConf2024の魅力を高めてくれてありがとうございます。

## [運営スタッフのみなさま](https://vimconf.org/2024/#menu-staff)

素晴しい場を作り上げていただき本当にありがとうございます。
準備段階でVimConfをより良いものにしたい！という情熱がヒシヒシと伝わってきました。
発表資料へのアドバイスもありがとうございました。
微力ながらVimConf2024の成功のために力を尽くせた経験は私の宝物です。

## VimConf2024参加者のみなさま

一緒に参加してくれてありがとうございます！
参加者がいるからこそのVimConfです。
私の発表にSNS等でリアクションくれたり、懇親会で交流してくれた方は本当にありがとうございました。
みなさん私の発表を聞いてくれてありがとうございました。

## 家族

家族とすごす時間を削ってしまったけれど、趣味の活動に理解を示してくれて本当にありがとう。

# 今回チャレンジしたこと

読者のみなさんからすると大したことないかも知れませんが、個人的には今回色んなチャレンジをしました。

- はじめて技術カンファレンスに参加した
  - そういえば1人で行った
- はじめて登壇した
- はじめてGoogleスライド使った
- はじめてiPadで発表してみた
- はじめて会った人と話せた

チャレンジは良いですね。
刺激になって考え方にも影響するし、まだ伸びしろあるんだと実感できます。

みなさんも是非コンフォートゾーンから飛び出してみましょう。

当日もう1つチャレンジしているのですが、別の記事にします。

# 発表内容

正直感謝を書いたら満足してしまいましたが、読んでいる人が何も得しないと思ったので体験記も書きます。

## 発表資料(公開準備中)

公開準備中。

## プロポーザル

下書きを↓に公開しています。これをフォームにコピペしました。
https://github.com/yuys13/vimconf2024/blob/main/proposal.md

Abstractは1つ目が最初に出したやつで、2つ目が締切前に修正したものです。

プロポーザルを書くコツは？なんて訊かれたりしましたが、一度しか書いてないのでよくわかりません。
心掛けていたことは、以下です。

- 伝えたいことを明確にする
- 聴衆にどういう行動変容を期待するかを明確にする
- 上記に必要な説明を加える

この文章は言いたいことを言っているだけなので、こういうのでは無いということです。

## スライドに埋め込んだgifのソース

↓で公開しています。
https://github.com/yuys13/vimconf2024/tree/main/vhs

vhsについては↓が凄くわかりやすいです。
https://zenn.dev/vim_jp/articles/2024-07-15-vim-vhs-and-neovim

## 発表のネタになったプラグイン

発表資料に記載したNeovimのAPIについては、`:help`を引くのと実装を見てしまうのが早いでしょう。
以下にあります。

https://github.com/yuys13/collama.nvim

スターくれたら無敵にはなれませんが、励みになります。
ただし、まだ設定のためのI/Fも、内部のI/Fも固まってません。
I/Fが固まっていないので、documentも書けてません。

LLM部分とUI部分は別の専門性が必要と考えています。
各位の専門分野に特化した変更ができるよう、綺麗なI/Fを作りたいと考えていましたが夢半ばです。
まだ手を動かすのが足りてない。
自分の用途には合わないと思っていても、とりあえず作ってみないと感覚が掴めないなと実感しています。
I/Fの設計は本質的に難しい。

自分のペースで開発していきます。

技術解説はまたの機会に。

## 発表の狙い

- 私のより凄いプラグインを誰かに作ってもらうために説明しよう
  - collama.nvimを一緒に作ってくれる人が出てきても良い
- vim-jp SlackでFIMの話しても反応薄かったので、VimConfで話して聞かせたろ
  - FIMの仕組みおもろいやん。おもろいよね？
  - 私は調べた時「その発想は無かった」って声出た
- Local LLMを布教したかった。Ollama便利
  - 便利なモデルあるよー等の情報交換が活発になれば良いなと
    - こういう感度はプラグイン作成とはまた別のスキルが必要だし、そういうのが得意な人にリーチしたい

## 発表準備はどうだった？

私の発表でみんな楽しんでくれるだろうか？と不安になりました。
プロポーザルに内容は全部書いて選んでもらったので、スタッフを信じれば大丈夫。と自らを奮い立たせていました。

Ollamaのアップデートでソースを修正したり発表資料の修正が必要になったりもしました。

結構な工数を投入しました。
登壇者になればチケット代がタダになるというネタがありますが、単金考えたら割にあいません。
私は身をもって体験しました。色んなイベントの発表者のみなさま、本当にありがとうございます。

## 発表自体はどうだった？

めっちゃ緊張しました。
息吸いすぎて苦しくなるという身体的反応は出てましたが、
吸いすぎたなら吐けば良いしな。とか時間危いからちょっとネタ飛ばそ。
など頭はハッキリしてたので、練習しておいて良かったなと思いました。
用意したネタ結構飛ばしました。
どうしてもiPadを見がちで、パフォーマンスとしての改善の余地はまだまだありました。
前見たらカメラマンがシャッターチャンス！となっていて、マジ下向いててスマンって思ってました。
練習時点でわかっていたことですが、もっと経験が必要ですね。
しかし人間としての営みも必要ですし、可能な限り準備に時間を投入できたので、悔いはありません。
(私はテキストエディタではないんですよ)

# おまけ

この記事を書いている途中にcodellama:7b-codeが面白いこと言いだしました。
3行目以降の薄いオレンジ色の文字がLLMの創作です。コード用モデルなのに日本語も話すの凄い。内容は面白いけど。
![スクリーンショット](https://github.com/user-attachments/assets/67410082-3a0f-4339-bbb4-385f66af9e08)

暇つぶししてくれる仕事は流石にひどすぎ。

# おまけ2

後日こんなこと言われました。

![まるであずきバー](https://github.com/user-attachments/assets/8da4275c-4638-4515-8239-30eb4a9190c0)

マジか。そんな固く無いはずだよなぁ。
普段からこんなですよ。

![人間だよ](https://github.com/user-attachments/assets/0009ea0c-f9bc-40d6-b6cd-aed4d0584d9f)

とは言え、思ってたより話したかった人と話せてないので、次回はもっと色んな人と話したいです。
ここまでこんな文章を読んでくれた人は私の事が好きなはずなので、次回はよろしくお願いします。