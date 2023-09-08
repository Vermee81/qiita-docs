---
title: gitLogConfig
tags:
  - 'Git'
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
---

![git-2.39.2](https://img.shields.io/badge/git-2.39.2-brightgreen)

手順を踏んでgit logをカスタマイズしていきます。

--onelineと--graphオプションではこんな見た目になります。


相対時間、作成者などの情報を付与して、色をつけて以下のような見た目にしていきます。


## 必要な項目を追加する

[git logのpretty formatオプションの公式ドキュメント](https://git-scm.com/docs/git-log?ref=justinjoyce.dev#_pretty_formats)を参考に必要な項目をつけていきます。

最初の状態として、onelineオプションと、graphオプションを指定した状態を見せます。

pyenvのリポジトリでコミット数5個を表示しています。

```bash
git log --oneline --graph -n 5
```

:::note alert
ここにgit log --oneline --graph -n 5の実行結果の画像をつける
:::


ここから必要な項目を追加するにはformatオプションを使います。
onelineオプションには省略されたコミットハッシュと件名が表示されているので、`%h`（省略されたコミットハッシュ）と`%s`（件名）を追加します。

```bash
git log --format='%h %s' --graph -n 5
```

:::note alert
ここにgit log --format='%h %s' --graph -n 5の実行結果の画像をつける
:::


色がなくなると辛いですが、色は次の節でつけていきます。
コミットハッシュの後にある参照情報 `(HEAD -> master, origin/master, origin/HEAD)` がないので、`%d`もつけます。


```bash
git log --format='%h %d %s' --graph -n 5
```

:::note alert
ここにgit log --format='%h %d %s' --graph -n 5の実行結果の画像をつける
:::

コミットした時刻を追加します。2日前など相対的な時間も追加します。
コミット時刻は `%ch` 、相対時刻は `%cr` です。
相対時刻は () で囲みました。

```bash
git log --format='%h %d %s %ch(%cr)' --graph -n 5
```

:::note alert
ここにgit log --format='%h %d %s %ch(%cr)' --graph -n 5の実行結果の画像をつける
:::

最後にコミットした人 `%an` も追加します。
見やすくなるように <> で囲みました。

```bash
git log --format='%h %d %s %ch(%cr) <%an>' --graph -n 5
```

:::note alert
ここにgit log --format='%h %d %s %ch(%cr) <%an>' --graph -n 5の実行結果の画像をつける
:::


## 色をつける

[gitのテキストカラーの公式ドキュメント](https://git-scm.com/docs/git-config#Documentation/git-config.txt-color) を参考に色をつけていきます。

`%C` と `%Creset` で囲んだ範囲を、 `%C` の直後に指定した色で装飾します。

```bash
%C(色の指定) 〜〜〜 %Creset
```

()は見やすくするためにつけていますが必須ではありません。`bold white`など複数単語を入力する場合は必要になります。

まずはgitにお任せする auto という指定があるので、autoを使ってみます。

```bash
git log --format='%C(auto)%h %d %s %ch(%cr) <%an>' --graph -n 5
```

:::note alert
ここにgit log --format='%C(auto)%h %d %s %ch(%cr) <%an>' --graph -n 5の実行結果の画像をつける
:::

参照情報まではいい感じですが、その後の項目はノータッチという印象です。
件名を太字の白 `bold white` にします。

```bash
git log --format='%C(auto)%h %d %C(bold white)%s%Creset %ch(%cr) <%an>' --graph -n 5
```

:::note alert
ここにgit log --format='%C(auto)%h %d %C(bold white)%s%Creset %ch(%cr) <%an>' --graph -n 5の実行結果の画像をつける
:::

少し見やすくなった気がします。
同様に時刻を明るい赤 `brightred` 、作成者を明るいシアン `brightcyan`にします。


```bash
git log --format='%C(auto)%h %d %C(bold white)%s%Creset %C(brightred)%ch(%cr)%Creset %C(brightcyan)<%an>' --graph -n 5
```

:::note alert
ここにgit log --format='%C(auto)%h %d %C(bold white)%s%Creset %C(brightred)%ch(%cr)%Creset %C(brightcyan)<%an>' --graph -n 5の実行結果の画像をつける
:::


今回は色の名前を指定していますが、RGBのカラーコードで自由に設定できます。
参考までに作成者名を `#ff0ab3` の色をつけてみます。

```bash
git log --format='%C(auto)%h %d %C(bold white)%s%Creset %C(brightred)%ch(%cr)%Creset %C(#ff0ab3)<%an>' --graph -n 5
```



## エイリアスの設定

毎回入力していられないので、エイリアスを設定します。
今回はホームディレクトリ直下の.gitconfigファイルにエイリアスを設定しました。
log以降のオプションをlgに短縮する設定です。

```.gitconfig
[alias]
lg = log --format='%C(auto)%h %d %C(bold white)%s%Creset %C(brightred)%ch(%cr)%Creset %C(brightcyan)<%an>' --graph
```

参考
https://git-scm.com/docs/git-log?ref=justinjoyce.dev#_pretty_formats

使える色
https://git-scm.com/docs/git-config#Documentation/git-config.txt-color


