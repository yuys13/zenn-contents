---
title: "Neovim luaのテストを書こう。neotestで高速に修正🔁テストのサイクルを回す"
emoji: "🚗"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [neovim, lua, test]
published: true
published_at: 2025-03-28
publication_name: vim_jp
---

:::message

本記事は[Vim駅伝](https://vim-jp.org/ekiden/)2025年3月28日の記事です。
前回はmikoto2000さんによる[Vim のマクロを使って気持ち良くなった話 - 表のデータを TS の enum にする]でした。

:::

# はじめに

今回はNeovim luaのテストを書く方法を紹介します。
Lua製プラグインはもちろんのこと、dotfilesで便利関数を書いている人にも試してほしいです。

Neovimで初めてluaを書いた人も多いでしょうし、「正直luarocksとかよくわからないんだよなー」という方もいらっしゃることでしょう ~~(自己紹介)~~ 。
Lua製プラグインはNeovimだけで動くのだから、プラグインのテストもNeovimで動いて欲しいというのが人情です。

今回は以下のプラグインを活用する方法を紹介します。

- [plenary.nvimのtest_harness](https://github.com/nvim-lua/plenary.nvim?tab=readme-ov-file#plenarytest_harness)
  - [Testing Guide](https://github.com/nvim-lua/plenary.nvim/blob/master/TESTS_README.md)
- [neotest](https://github.com/nvim-neotest/neotest)
- [neotest-plenary](https://github.com/nvim-neotest/neotest-plenary)

日本語圏ではvustedが人気な印象です。plenaryのtest_harnessとneotestの組み合わせも、素晴らしい開発者体験が得られるので是非試してみて欲しいです。

# テストを書いてみる

## 事前準備

テストを書く前にplenary.nvimをインストールしておいてください。
遅延読み込みをしている場合は読み込んでおきましょう。

https://github.com/nvim-lua/plenary.nvim

## はじめてのテストコード

さて、早速ですがプラグインの場合はリポジトリのトップに`test`フォルダを作りましょう。
dotfilesの便利関数のテストを書く場合は`.config/nvim/`に`test`フォルダを作りましょう。
`test`の中に`hello_spec.lua`を以下の内容で作成してみましょう。

```lua:hello_spec.lua
describe("Hello", function()
  it("Test", function()
    assert.is_true(true)
  end)
end)
```

書いたらNeovimで`:PlenaryBustedDirectory test`を実行してみましょう。
floating windowで以下が表示されたら成功です。

```bash
Starting...Scheduling: test/hello_spec.lua

========================================
Testing:        /Users/yuys13/.config/nvim/test/hello_spec.lua
Success ||      Hello Test

Success:        1
Failed :        0
Errors :        0
========================================
```

あとは`lua`ディレクトリ内に書いたコードを`require`してテストコードを足すだけです。
今回はテストコードそのものについては触れません。
私は以下を参考にしてテストを書いています。
(spyとstubはソース読んでしまいました)

- plenaryのREADME.mdの[test_harness](https://github.com/nvim-lua/plenary.nvim?tab=readme-ov-file#plenarytest_harness)
- [Testing Guide](https://github.com/nvim-lua/plenary.nvim/blob/master/TESTS_README.md)
- [luassert](https://github.com/lunarmodules/luassert)

## コマンドラインでテストを動かす

Neovim内でテストが実行できることはわかりました。
しかしCIで動かすことを考えるとコマンドラインから実行したいところです。
また普段使っているNeovimではplenaryが読み込まれていますが、CI環境では用意する必要があります。

それではtestフォルダ内に`minimal_init.lua`を作成します。

```lua:minimal_init.lua
local plenary_dir =
  vim.fs.joinpath(vim.fn.fnamemodify(debug.getinfo(1, 'S').source:sub(2), ':p:h:h'), '.dependencies/plenary.nvim')

if vim.fn.isdirectory(plenary_dir) == 0 then
  vim.system({ 'git', 'clone', '--filter=blob:none', 'https://github.com/nvim-lua/plenary.nvim', plenary_dir }):wait()
end

vim.opt.rtp:append(plenary_dir)
```

このスクリプトの動作は以下です。

- `test/minimal_init.lua`の位置から`../../.dependencies/plenary.nvim`の絶対パスを得る
- ↑のパスが存在しなければplenary.nvimをcloneする
- ↑のパスを`runtimepath`に追加する

このスクリプトを読み込めばplenaryがプラグインとして追加されるので、テスト実行コマンドが使えるようになります。
以下のようにテストを実行してみましょう。

```bash
$ nvim --headless -u test/minimal_init.lua -c "PlenaryBustedDirectory test"
Starting...Scheduling: test/hello_spec.lua

========================================
Testing:        /Users/yuys13/.config/nvim/test/hello_spec.lua
Success ||      Hello Test

Success:        1
Failed :        0
Errors :        0
========================================
```

これでNeovimの知識だけでテストを書いて実行できるようになりました。

# neotestでテストを実行して結果確認して修正するサイクルを高速で回す

やっと本題です。

## neotestをインストールする

neotestとneotest-plenaryをインストールしましょう。

https://github.com/nvim-neotest/neotest

https://github.com/nvim-neotest/neotest-plenary

参考までにlazy.nvimでのインストール例です。
(plenaryは既に入っているはずなので省略)

```lua
  {
    'nvim-neotest/neotest',
    cmd = 'Neotest',
    config = function()
      ---@diagnostic disable-next-line: missing-fields
      require('neotest').setup {
        adapters = {
          require 'neotest-plenary',
        },
      }
    end,
  },
  { 'nvim-neotest/neotest-plenary' },
  { 'nvim-neotest/nvim-nio' },
```

## neotestでテストを一覧する

`:Neotest summary`を実行しましょう。

![Neotest summary](https://github.com/user-attachments/assets/2c1f7ddf-8c89-490f-8bbd-b55d7d1cf164)

一個しかないのでイマイチですね。

## neotestでテストを一括実行する

summaryのツリー上で`r`を押してみましょう。

![Neotest run](https://github.com/user-attachments/assets/b76aa5db-b03a-42a4-9269-1bcdae8ae8a7)

ツリーにテストの結果が反映され、テストコードのバッファにも結果が表示されました。

## ファイルをwatchして変更があったらテストを自動実行する

summaryのツリーでwatchしたいファイルにカーソルを合わせて`w`を押します。

![Neotest watch](https://github.com/user-attachments/assets/09e7e73b-a47b-4303-a3ee-d14b45550710)

ファイル名横のアイコンが目のようになりました。
それでは試しに失敗するテストを追加してみましょう。

![write fail test](https://github.com/user-attachments/assets/6af04717-0d23-49fd-9d06-c1597a39329a)

絶対失敗するテストが書けました。ここでファイルを保存します。

![Neotest watch save](https://github.com/user-attachments/assets/6b8ac84d-ee13-45ed-b8e1-ab4b74865bae)

情報量が多いですが、ツリーでは`Fail`❌がついており、テストコードのバッファでも❌がついています。
またテストの出力も表示されていますね。

## 失敗したテストの原因を調べる

テストの出力を見てみましょう。
まずはツリー上で確認する方法から。

出力を確認したいテストにカーソルを合わせて`o`を押します。

![Neotest tree output](https://github.com/user-attachments/assets/601bbd03-8e4a-478e-8e1e-e378360669b5)

trueが期待されていたのに、falseを渡されたことがわかります。

テストコードのバッファではテストの結果がDiagnosticsとして表示されています。
そのためLanguage ServerのDiagnosticsと同じように確認可能です。

![Neotest diagnostics](https://github.com/user-attachments/assets/a8f77d0b-3768-4b91-9563-cb99623f7d70)

Diagnosticsなのでfloating windowの中にも入れます。

https://zenn.dev/vim_jp/articles/entering-neovim-diagnosic-floating-window

他にも`:Neotest output`や`:Neotest output-panel`でも確認可能です。

## Neotest summaryのマッピングを確認する

便利なのはわかったけどキーマップ覚えられないと言いたいのですよね。わかります。
`?`を押せば表示されるので安心してください。

![Neotest mapping](https://github.com/user-attachments/assets/42bdc648-e9b9-4fa0-8088-ae8cd98c47f2)

watchだけ覚えてれば困らない気もします。

# まとめ

- plenary.nvimのtest_harnessでNeovim luaのテストが書ける
  - luarocksなどに依存せずNeovimがあればテスト可能
  - コマンドライン実行もできるのでCIも実現可能
- neotestとneotest-plenaryで実装→結果確認→修正のサイクルを高速で回すことが可能

# おまけ

vustedを使った方法は以下をご覧ください。

https://zenn.dev/notomo/articles/neovim-lua-plugin-testing

実はplenaryのtest_haernessでテストを書いておけばvustedでも実行できます。逆は動かない場合があります。
plenaryはbustedの記法の一部をサポートしていて、vustedは本物のbustedを動かしているためです。
よってplenaryからvustedへの移行は簡単に済むので、迷ったらとりあえずplenaryでテストを書き始めると良いでしょう。

カバレッジの取得はvustedの方が容易に実現できるため、カバレッジの測定のみvustedで実行する構成もありだなと考えています。

# ここまで読んでくれた方へ

dotfilesの便利関数のテストは書けそうでしょうか。
おや、もう書きましたか。さすがです。
ではそれをリポジトリ分割してプラグインとして公開してみましょう。
私にもその便利関数使わせてください。

こちらからは以上です。
