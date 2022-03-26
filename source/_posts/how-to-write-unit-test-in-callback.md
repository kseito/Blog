---
title: Androidで単体テストを書く時にコールバックの戻り値をモックする
date: 2021-08-20 18:00
tags:
- Android
- Mockito
---

あまり出番はないかもしれませんが、たまに`あるクラスからコールバックで返ってきた値を使って何か行う`というコードを書く時があります。
そのような処理が書かれたクラスを単体テストする方法が分からなかったので調べました。

## 解決方法
Androidアプリ開発において上記のようなケースで単体テストを書きたい場合はMockitoのInvocationOnMockを使います。
mockito-kotlinを使って書く場合は下記のようになります。
```kotlin
interface Hoge {
    fun getOrCreate(listener: (String?) -> Unit)
}

val hoge: Hoge = mock()
whenever(hoge.getOrCreate(any())).thenAnswer { invocation ->
    val listener = invocation.arguments[0] as ((String?) -> Unit)
    listener.invoke("test")
}
```
Hogeインターフェースをモックしメソッド実行時の挙動を定義します。
`InvocationOnMock`から`getOrCreate`の第1引数を取り出し、それを発火させます。
その結果、プロダクトコード側の`getOrCreate`メソッドがコールバックを返す挙動を単体テストで再現することができます。
Hogeの具象クラス側の実装が単体テストできない時等に有効です。

## 参考サイト
https://stackoverflow.com/questions/48204784/unit-test-for-kotlin-lambda-callback