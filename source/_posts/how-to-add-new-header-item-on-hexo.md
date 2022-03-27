---
title: Hexoのヘッダーにプロフィールを追加する
date: 2021-04-24 10:53:46
tags: Hexo
---

結論からいうと`Hexoのドキュメントを読もう`になります。
やり方はいくつかあるのですが今回は記事とは別に個別のページを作ってそこにプロフィールを書く形式にしました。

## プロフィール画面を追加する
Hexo上で新しいページを作成する時は、CLI上で下記のようなコマンドを実行します。

```javascript
hexo new page "profile"
```

コマンドを実行するとプロジェクト直下の`source`ディレクトリ内に`profile/index.md`が生成されるので、
そこに対してマークダウン形式でプロフィールを書いていけばできあがりです。
今回私が作成したプロフィールは[こちら](/profile)

## 参考サイト
https://stackoverflow.com/questions/29167023/how-to-add-route-for-hexo
https://hexo.io/docs/writing
