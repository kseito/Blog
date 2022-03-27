---
title: Robolectricで端末の言語設定を変更する
date: 2021-03-18 18:00
tags: Android
---

Androidで文字列リソースを正しく組み立てることができるか、みたいなテストが書きたい時とかありますよね？
Robolectricを使っているとそんな時、デフォルト設定が英語なのでアプリが複数言語に対応している場合、期待値を日本語で書けなかったりします。
Robolectricでテスト時の言語設定を変更できないか調べてみたら、下記のように記述することで実現できました。

```kotlin
@RunWith(RobolectricTestRunner::class)
@Config(qualifiers = "ja")
class HogeTest(
    ...
```

`qualifiers`では言語以外にも画面サイズやナイトモードなどいくつか変更可能なパラメータがあるようです。


## 参考サイト
http://robolectric.org/device-configuration/
