---
title: minSdkVersion 24未満のプロジェクトでSimpleDateFormatを使ってISO-8601に対応してはいけない
date: 2020-12-18 18:30:00
tags: Android
---

## 問題
```kotlin
SimpleDateFormat("yyyy-MM-dd'T'00:00:00XXX", Locale.getDefault())
```

WEB APIに送る日付書式をISO-8601の拡張形式に準拠した形式にするため、上記のようなコードを書いていたらAndroid 6未満で`IllegalArgumentException`が発生してました。
メッセージは下記のようなもので`X`が認識されてないようでした。

```
Unknown pattern character 'X'
```

ソースコードを読んだところ、Android 6に含まれる`SimpleDateFormat`の実装ではStringで渡される日付フォーマットに`X`が入ることが考慮されていないようでした。
Android 7以降では問題なく動作しているので、Android 6ではJava6ベースの`SimpleDateFormat`が、Android 7ではJava7ベースの`SimpleDateFormat`が使われてるのではないかと推測します。（調べ方がわからない。。。）

## 対策
[ThreeTenABP](https://github.com/JakeWharton/ThreeTenABP)を使いました。
ThreeTenABPはAndroidでJava8のDate and Time APIの一部をバックポートできるライブラリです。
Java8のDate and Time API使えば間違いないやろ！と考えてましたが、AndroidでJava8を使うにはAPIレベル 26が必要らしくもう数年待つ必要がありました。

## まとめ
DroidKaigiアプリでThreeTenABPが使われてて、何か便利なのだろうけど何が便利なのかわからんという状態でしたが今回の件でありがたみを理解できました。

## 参考サイト
- https://developer.android.com/reference/java/text/SimpleDateFormat#timezone
- https://takerpg.hatenablog.jp/entry/20170921/1505977665
- https://github.com/JakeWharton/ThreeTenABP
- https://qiita.com/opengl-8080/items/d0dc57d64d3a871a8463
