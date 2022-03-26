---
title: Kotlin Reflectionに触れてみる
date : 2019-11-22 09:58:14
---

AndroidのAPKをアップロードする時に表示されるようになった[警告](https://developer.android.com/distribute/best-practices/develop/restrictions-non-sdk-interfaces)について調べたら、どうやらリフレクションを使ってAndroid SDKに含まれるクラスの公開されていないメソッド、プロパティを参照すると表示される警告らしいということが分かりました。
しかし、今までリフレクションは単体テストレベルでプライベートメソッドを無理やりテストするくらいしか試したことがなく、Android SDKで公開されていない情報を本当に参照できるのか疑問に思ったので試してみました。
<br/>
   
## 準備
### 依存解決
新しくAndroidプロジェクトを作成後、`implementation "org.jetbrains.kotlin:kotlin-reflect:$kotlin_version"`を追加して、Kotlinのリフレクションライブラリをインポートする。
これでリフレクションを使う準備ができました。

### クラス作成
下記のような、プライベートメソッド・プロパティを持つクラスを作成します。

```kotlin
data class User(val id: Int, val name: String, private val description: String) {

    private fun getFullInfo(): String {
        return "This user's id is $id, name is $name"
    }
}
```
<br/>
<br/>

## 自作クラスで試す
### メソッドの参照
Kotlinリフレクションを使って特定のメソッドを呼びだします。

```kotlin
cls.memberFunctions
    .filter { it.name == "getFullInfo" }
    .forEach {
        it.isAccessible = true
        println("This function name is ${it.name}. value is ${it.call(user)}")
    }
```

#### 出力
```
 This function name is getFullInfo. value is This user's id is 1, name is kseito
```

### プロパティの参照
Kotlinリフレクションを使って特定のプロパティを参照します。

```kotlin
cls.memberProperties
    .filter { it.name == "description" }
    .forEach {
        it.isAccessible = true
        println("${it.name} value is ${it.get(user)}")
    }
```

#### 出力
```
 description value is I love Splatoon!
```

<br/>
<br/>


## TextViewのプロパティ参照
最後にAndroid SDKに含まれるクラスです。
みんな大好きTextViewのプライベートプロパティに対してリフレクションを使ってみます。
本来なら`getText()`で取得するテキストをリフレクションを使ってプロパティ参照してみます。

```kotlin
val textView = findViewById<TextView>(R.id.text_view)
val cls2 = TextView::class
cls2.memberProperties
    .filter { it.name == "mText" }
    .forEach {
        it.isAccessible = true
        println("${it.name} value is ${it.get(textView)}")
    }
```

#### 出力
```
 mText value is Hello World!
```

無事取得できました。

サンプルソースは[こちら](https://github.com/kseito/ReflectionPractice)

## 参考サイト
https://qiita.com/HIkaruSato/items/d9a9b0ca4b1da77221fbjkjkjkjkaaaaa
https://qiita.com/KeithYokoma/items/9e692808095acf560bc9

