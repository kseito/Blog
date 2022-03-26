---
title: GroovyでSystem.getenvの戻り値がnullだった時の対処法
date: 2021-08-17 18:00
tags:
- Android
- Groovy
---

`build.gradle`において下記のように環境変数から値を取り出してごにょごにょする時が稀によくあるのですが、取得できたりできなかったりする環境変数の時に対処に困ったのでメモを残します。

```groovy
hoge = System.getenv("HOGE")
```

## 解決方法
Groovyではエルビス演算子が使えます。

```groovy
hoge = System.getenv("HOGE") ?: "Default Value"
```

知らんかった…