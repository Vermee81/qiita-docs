---
title: ステップバイステップでgit logを自分流にカスタマイズする
tags:
  - Git
private: false
updated_at: '2023-09-08T23:38:06+09:00'
id: ecead0b4f12a362323f8
organization_url_name: null
slide: false
---
![git-2.39.2](https://img.shields.io/badge/git-2.39.2-brightgreen)

以下の手順を踏んでgit logをカスタマイズしていきます。
- git logに項目を追加
- それぞれの項目に色を設定
- エイリアスの設定

--onelineと--graphオプションではこんな見た目になります。
![スクリーンショット 2023-09-06 11.54.30.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2818/5b35eb9b-72df-5b9b-a77b-367c5c9a6d99.jpeg)

相対時間、作成者などの情報を付与して、色をつけて以下のような見た目にしていきます。
```bash
git log --format='%C(auto)%h %d %C(bold white)%s%Creset %C(brightred)%ch(%cr)%Creset %C(brightcyan)<%an>' --graph -n 5
```
![スクリーンショット 2023-09-08 23.15.57.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2818/bb88476c-42ce-b9c4-965d-c22cbc2f7c28.jpeg)


## 必要な項目を追加する

[git logのpretty formatオプションの公式ドキュメント](https://git-scm.com/docs/git-log?ref=justinjoyce.dev#_pretty_formats)を参考に必要な項目をつけていきます。

最初の状態として、onelineオプションと、graphオプションを指定した状態を見せます。

pyenvのリポジトリでコミット数5個を表示しています。

```bash
git log --oneline --graph -n 5
```

![スクリーンショット 2023-09-06 11.54.30.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2818/d2bc786b-b363-dcb9-f4e8-6e6c0721868f.jpeg)


ここから必要な項目を追加するにはformatオプションを使います。
onelineオプションには省略されたコミットハッシュと件名が表示されているので、`%h`（省略されたコミットハッシュ）と`%s`（件名）を追加します。

```bash
git log --format='%h %s' --graph -n 5
```

![スクリーンショット 2023-09-06 12.04.33.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2818/0e34d50d-1be7-ad8c-5750-76734590ad5f.jpeg)


色がなくなると辛いですが、色は次の節でつけていきます。
コミットハッシュの後にある参照情報 `(HEAD -> master, origin/master, origin/HEAD)` がないので、`%d`もつけます。


```bash
git log --format='%h %d %s' --graph -n 5
```

![スクリーンショット 2023-09-08 22.58.32.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2818/55b071a1-65d5-60e6-785e-4f343669cd4c.jpeg)


コミットした時刻を追加します。2日前など相対的な時間も追加します。
コミット時刻は `%ch` 、相対時刻は `%cr` です。
相対時刻は () で囲みました。

```bash
git log --format='%h %d %s %ch(%cr)' --graph -n 5
```

![スクリーンショット 2023-09-08 22.59.24.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2818/a766f15d-f2cc-5604-40be-72ab1ea1f076.jpeg)


最後にコミットした人 `%an` も追加します。
見やすくなるように <> で囲みました。

```bash
git log --format='%h %d %s %ch(%cr) <%an>' --graph -n 5
```

![スクリーンショット 2023-09-08 23.00.09.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2818/8193ddd9-d383-7d72-0f05-673c9135d562.jpeg)


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

![スクリーンショット 2023-09-08 23.00.38.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2818/f5d4e4a1-6701-5a13-69ce-9f86c6eb598f.jpeg)


参照情報まではいい感じですが、その後の項目はノータッチという印象です。
件名を太字の白 `bold white` にします。

```bash
git log --format='%C(auto)%h %d %C(bold white)%s%Creset %ch(%cr) <%an>' --graph -n 5
```

![スクリーンショット 2023-09-08 23.05.03.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2818/79004f0a-eb6d-a1c6-153e-5e3022b4601e.jpeg)


少し見やすくなった気がします。
同様に時刻を明るい赤 `brightred` 、作成者を明るいシアン `brightcyan`にします。


```bash
git log --format='%C(auto)%h %d %C(bold white)%s%Creset %C(brightred)%ch(%cr)%Creset %C(brightcyan)<%an>' --graph -n 5
```

![スクリーンショット 2023-09-08 23.15.57.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2818/77af1366-1a61-fd1a-f840-cb9168e1ff88.jpeg)


今回は色の名前を指定していますが、RGBのカラーコードで自由に設定できます。
参考までに作成者名を `#ff0ab3` の色をつけてみます。

```bash
git log --format='%C(auto)%h %d %C(bold white)%s%Creset %C(brightred)%ch(%cr)%Creset %C(#ff0ab3)<%an>' --graph -n 5
```

![スクリーンショット 2023-09-08 23.17.33.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2818/001a5607-96f9-5c1d-fef1-477d49409f53.jpeg)


## エイリアスの設定

毎回入力していられないので、エイリアスを設定します。
今回は全リポジトリに適用されるようにホームディレクトリ直下の.gitconfigファイルにエイリアスを設定しました。
log以降のオプションをlgに短縮する設定です。

```.gitconfig
[alias]
lg = log --format='%C(auto)%h %d %C(bold white)%s%Creset %C(brightred)%ch(%cr)%Creset %C(brightcyan)<%an>' --graph
```

最後までお付き合いありがとうございました。

## 参考リンク

git logのフォーマット
https://git-scm.com/docs/git-log?ref=justinjoyce.dev#_pretty_formats

使える色
https://git-scm.com/docs/git-config#Documentation/git-config.txt-color


