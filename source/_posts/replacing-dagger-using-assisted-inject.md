---
title: Assisted Injectを使って既存のViewModelのコンストラクタにIDを渡す
date: 2021-04-19 18:30
tags: Android
---

Dagger 2.31からAssisted Injectという`Injectする一部のインスタンスをDaggerの外側から注入できる`仕組みが登場しました。
これの何が便利かというと、ViewModelへIDを渡して通信を行うケースでコンストラクタでIDを渡せるため、IDをViewModelへ渡す際にセッターや通信を行うメソッドの引数に持たせる必要がなくなりました。
既にDaggerを導入済みのViewModelであれば比較的簡単に移行が行えます。
必要な実装は下記になります。

1. ViewModelのコンストラクタにIDを定義
2. ViewModel生成用のファクトリメソッド追加
3. ViewModelProvider.Factory生成用のファクトリメソッド追加
4. 2で作成したファクトリメソッドをActivity/Fragment側にInjectする
5. Daggerの古いViewModel定義を削除する

実際にどのようなコードになるのか書いてみましょう。

## 実装
1. ViewModelのコンストラクタにIDを定義

```kotlin
class HogeViewModel @AssistedInject constructor(
        private val useCase: FugaUseCase,
        @Assisted private val fugaId: Long
) : ViewModel()
```

ViewModelのコンストラクタにAssistedInjectアノテーションを指定します。
ViewModelにIDを追加するときは、Assistedアノテーション使います。

2. ViewModel生成用のファクトリメソッド追加

```kotlin
@AssistedFactory
interface Factory {
    fun create(fugaId: Long): HogeViewModel
}
```

AssistedFactoryアノテーションを使うとDaggerがいい感じに依存関係を解決してくれるらしい。

3. ViewModelProvider.Factory生成用のファクトリメソッド追加

```kotlin
companion object {
    fun provideFactory(
            assistedFactory: Factory,
            fugaId: Long
    ): ViewModelProvider.Factory = object : ViewModelProvider.Factory {
        override fun <T : ViewModel?> create(modelClass: Class<T>): T {
            return assistedFactory.create(fugaId) as T
        }
    }
}
```

HogeViewModelに必要なパラメータを含んだViewModelProvider.Factoryを作ります。

4. 2で作成したファクトリメソッドをActivity/Fragment側にInjectする

```kotlin
@Inject
lateinit var viewModelFactory: HogeViewModel.Factory
private val viewModel: HogeViewModel by viewModels {
    HogeViewModel.provideFactory(viewModelFactory, args.fugaId)
}
```

HogeViewModel用のファクトリをInjectし、それを元にViewModelをインスタンス化します。

5. Daggerの古いViewModel定義を削除する

```kotlin
//@Binds
//@IntoMap
//@ViewModelKey(HogeViewModel::class)
fun bindHogeViewModel(viewModel: HogeViewModel): ViewModel
```

地味に嵌ったのがこの処理で、最初Daggerまわりでコンパイルが通らずAssisted Injectの書き方を疑っていましたが、エラーをよくよく見ると旧ViewModel定義が解決できてないようなエラーだったので削除することで解決しました。

## 所感
今まではDagger経由で頑張ってIDを渡すよりは、セッターを使ってIDを渡した方が簡単だったので後者を採用していましたが、Assisted Injectが導入されたことで形勢逆転した感じがあります。
日々使いやすくなっていくDaggerを今後も追いかけていきたい。
すごいぞDagger！頑張れDagger！

## 参考サイト
https://dagger.dev/dev-guide/assisted-injection.html
https://qiita.com/takahirom/items/23b0f05ed3cdd6872bcb
