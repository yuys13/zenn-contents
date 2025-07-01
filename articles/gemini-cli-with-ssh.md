---
title: "SSH先でgemini-cliを使うために認証をどうにかする"
emoji: "🚗"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [AI, gemini, cli, ssh]
published: true
---

# はじめに

`gemini-cli` を利用する際、通常はブラウザが起動し、Googleアカウントでの認証が求められます。
しかし、SSH接続先のサーバーなど、GUI環境がない場所で `gemini-cli` を使いたい場合、このブラウザ認証が問題となります。

こんな経験はありませんか。

- 認証用のURLがわからない
- 認証用のURLはわかったけど、ログイン後アクセスできないページにリダイレクトされる

私はあります。

以下に解決方法が書いてありましたので、内容を共有します。

https://github.com/google-gemini/gemini-cli/issues/1437

# 認証手順

SSH接続先のサーバーで `gemini-cli` を認証するには、以下の手順を実行します。

## 1. 認証URLの取得

まず、SSH接続先のターミナルで `gemini-cli` を実行し、認証用のURLを表示させます。

```bash
npx https://github.com/google-gemini/gemini-cli --debug
```

または、適当なプロンプトを付けて実行することでも認証URLが表示されます。

```bash
npx https://github.com/google-gemini/gemini-cli -p "Hello"
```

このコマンドを実行すると、以下のようなメッセージが表示され、ブラウザで開くべきURLが提示されます。

```text
To continue, open this URL in your browser:
https://accounts.google.com/o/oauth2/auth?...
```

## 2. ブラウザでの認証とコールバックURLの取得

ローカルマシンのブラウザで、上記で表示されたURLを開きます。Googleアカウントでの認証を完了させると、最終的に以下のような `localhost` を含むコールバックURLにリダイレクトされます。

```text
http://localhost:41725/oauth2callback?...
```

この `http://localhost:41725/oauth2callback?...` で始まるURL全体をコピーしてください。

## 3. `curl` を使った認証の完了

SSH接続先の別のターミナルを開き、先ほどコピーした `localhost` のコールバックURLを `curl` コマンドで実行します。

```bash
curl "http://localhost:41725/oauth2callback?code=..."
```

`curl` コマンドの実行後、最初の `gemini-cli` を実行していたターミナルに戻ると、認証が完了し、`gemini-cli` が利用可能になっているはずです。

# おわり

`--debug`で起動した場合、そのまま使い続けると凄い勢いでログが出力されるので、一旦終了して起動しなおすことをお勧めします。
(おかげでログが流れてしまって記事を書く時に困りました)

これで出先でもiPadからLinuxにSSHして開発できます。
