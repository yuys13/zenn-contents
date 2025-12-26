---
title: "手元のGeminiにGemini Code Assistのようにレビューをしてもらう"
emoji: "🚗"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [gemini, review, antigravity, ai]
published: true
---

# はじめに

Geminiにコードを書いてもらって、PullRequestを出したらGemini Code Assistにレビュー指摘される。
そんな経験ありませんか。
最初は面白かったのですが、修正と指摘が3往復くらいすると面倒臭くなりました。

同じGeminiなんだから自分でレビューしてくれ、と。

# GeminiにGemini Code Assistと同じようにレビューさせる

ということで、手元のGeminiにもGemini Code Assistのようにレビューしてもらいましょう。

## gemini-cliの場合

gemini-cli向けには公式拡張があります。

https://github.com/gemini-cli-extensions/code-review

Gemini Code Assistと同じ作者によって提供されているようです。

以下でインストールできます。

```bash
gemini extensions install https://github.com/gemini-cli-extensions/code-review --auto-update
```

あとはgemini-cliの作業中に`/code-review`すると差分を見てレビューしてくれます。
PullRequest出す前に一定の品質が保てそうですね。

## Antigravityの場合

AntigravityにはWorkflowsという仕組みがあります。
これを使って`/code-review`を作れば同じことができます。
詳しくはドキュメントを見てください。

https://antigravity.google/docs/rules-workflows

WorkflowsはAntigravity自身に作らせることができます。
AntigravityにAntigravity用の`gemini-cli-extensions/code-review`を作ってもらいましょう。
`gemini-cli-extensions/code-review`の本体は`commands/code-review.toml`です。

https://github.com/gemini-cli-extensions/code-review/blob/main/commands/code-review.toml

これを元にワークフロー作ってと言うだけです。

これでAntigravityでも`/code-review`できるようになりました。

便利。

# まとめ

Geminiに作業をさせたら、PullRequest前にセルフレビューしてもらいましょう。

~~作者が同じだけど、同じように作ったとは言ってないよね~~
