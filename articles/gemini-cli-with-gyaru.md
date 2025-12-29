---
title: "ギャルで学ぶgemini-cli-extensions ~かわいいとextensionsは作れる！~"
emoji: "🚗"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [gemini, cli, AI]
published: true
---

# はじめに

<!--textlint-disable ja-technical-writing/no-exclamation-question-mark -->

みなさん、`~/.gemini`をカスタマイズしてますか？
`~/.gemini/GEMINI.md`を育てている人も多いですよね？
ではextensionsはどうでしょうか？

extensionsはGEMINI.mdやカスタムコマンド等をひとまとめにして、拡張機能として提供できます。
また、拡張機能は個別に有効/無効を切り替えることも可能です。

自分の設定をextensionsに切り出せば、再利用性が高まり、意味のある単位で管理できます。
この手法はVimやshellのプラグインでワークしている仕組みと同等と考えています。

今日はそんな可能性に溢れた、gemini-cliのextensionsについて説明します。
公式の説明が読みたい人は以下を読んでください。

https://geminicli.com/docs/extensions/

# gemini-cli-extensionsの利用方法

という訳でギャル化拡張機能作ったので、これを例に説明していきます。

https://github.com/yuys13/gemini-cli-extension-gyaru

あ、ギャル好きな人はスター付けてくれると嬉しいです。

## インストール

以下だけでgemini cliがギャルになります。

```bash
gemini extensions install https://github.com/yuys13/gemini-cli-extension-gyaru
```

## 一時的に無効化する

```bash
gemini extensions disable gyaru
```

これでアンインストールせずに、ギャル拡張を無効化できます。
すぐ切り替えられるの便利ですね。

またギャルに会いたくて会いたくて震えてきたら、以下で有効化できます。

```bash
gemini extensions enable gyaru
```

enable/disableは`--scope workspace`でワークスペース毎に有効化も可能です。

## アンインストール

ギャルと別れたくなったら以下でアンインストールです。

```bash
gemini extensions uninstall gyaru
```

# gemini-cli-extensionsの作り方

基本的にはテンプレートを展開して、`GEMINI.md`とカスタムコマンドを書くだけです。
(MCPは公式ドキュメント読んでください)

公式サイトは以下です。

https://geminicli.com/docs/extensions/getting-started-extensions/

テンプレートはさっと展開できます。

```bash
gemini extensions new <path> [template]
```

`<path>`はテンプレートを展開したい場所を指定してください。
`[template]`は記事執筆時点で以下から選択できます。

- context
- custom-commands
- exclude-tools
- mcp-server

# おわりに

`~/.gemini/GEMINI.md`に記載した言語毎のルールを拡張機能として切り出して、必要なものだけ有効にしてはどうでしょうか？
無造作に増えていった`~/.gemini/commands`内のコマンドを、意味のあるまとまりで管理してみては如何でしょうか？

あと、面白い拡張作ったら教えてください。

以下から色々な拡張機能を探せます。

https://geminicli.com/extensions/
