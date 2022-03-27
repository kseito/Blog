---
title: Kotlinのインライン関数のパフォーマンスを測ってみた
date: 2020-01-14 09:00:00
tags: Kotlin
---

インライン関数の存在自体は前から知っていたのですが、使うことでどれくらいパフォーマンスが向上するのかわからなかったので調べてみました。

## 測定環境
- OS -> Mac 10.14.6
- CPU -> Intel Core i9(2.9GHz)
- メモリ -> 32GB

## インライン関数とは
そもそもインライン関数とは、インライン展開を指示するような記述のある関数のことです。
インライン展開とは、呼び出す側に対象の関数の中身を記述することで、関数呼び出しにかかるオーバーヘッドを無くすようなコンパイラの動作を指します。

## 実験
インライン関数と非インライン関数の速度を比較するために下記のようなコードを準備しました。
３回計測して平均値を取得してみます。

```kotlin
fun hoge(x: Int, y: Int, function: (a: Int, b: Int) -> Int) {
    function(x, y)
}
```

この関数をまずはこのまま1億回呼んでみます。

```kotlin
(1..100_000_000).map {
    hoge(it, it) { a, b -> a + b }
}
```

速度は、(1009+963+945)/3=972.3msでした。
次に`inline`修飾子をつけて試してみます。

```kotlin
inline fun hoge(x: Int, y: Int, function: (a: Int, b: Int) -> Int) {
    function(x, y)
}
```

結果は、(872+878+848)/3=866msでした。

## 結論
今回はMacbook Proを使ったこともあり1億回呼び出しして100ms程度の差に留まりましたが、格安スマホなどで試せば差はさらに顕著になるかと思います。
何れにせよ、ループ処理の中で高階関数をパラメータに持つ関数を呼びだす場合はインライン関数を使っておいた方がパフォーマンスが良くなることがわかりました。


## 参考URL
https://dogwood008.github.io/kotlin-web-site-ja/docs/reference/lambdas.html
https://qiita.com/sekitaka_1214/items/749f824e04d6fda4733c
https://qiita.com/satoru_takeuchi/items/5d5eacfd805bd5289311
