---
title: Picassoで取得した画像をDarwableとして扱う
date: 2021-01-16 14:30:00
tags: Android
---

Androidで画像のURLをImageViewにセットする場合、PicassoやGlide等のライブラリを使うのが一般的だと思います。
しかし、何らかの理由でImageViewを参照できなかった場合どうすれば良いでしょうか。
今回は、ImageViewの参照が限定的な時にPicassoのライブラリを使って画像をセットするやり方について書きます。

## 問題
任意の画像URLから画像をダウンロードして`Toolbar`のロゴにセットしたかったのですが、
`Toolbar`のロゴ（`ImageView`）は外部に公開されておらず`Drawable`をセットするメソッドがあるだけした。
`ImageView`が参照できないと詰みでは？と思ってたのですがドキュメントを眺めたら`Target`というクラスがこの問題を解決してくれそうだったので試してみました。

## 解決策
コードは下記になります。

```kotlin
val target = object : Target {
            override fun onBitmapLoaded(bitmap: Bitmap?, from: Picasso.LoadedFrom?) {
                binding.toobar.logo = bitmap?.toDrawable(resources)
            }

            override fun onBitmapFailed(errorDrawable: Drawable?) {
                binding.toobar.logo = errorDrawable
            }

            override fun onPrepareLoad(placeHolderDrawable: Drawable?) {
                binding.toobar.logo = placeHolderDrawable
            }
        }

        Picasso.with(this)
                .load(url)
                .into(target)
```

`Target`の使い方は`ImageView`同様、`into()`メソッドのパラメータにセットするだけです。
そうすることで、プレースホルダー画像・エラー画像・URLから取得した画像のDrawableをコールバックで受け取ることができます。

## 感想
普段自分は問題にぶつかった時、自分が思いつく解決策を実現してくれるコードがライブラリにあるかという観点でコードを読んでいます。
が、square製ライブラリの場合は、そもそも自分が思いもしなかったスマートな解決が提供されてる場合がありドキュメントをしっかり読むことの重要性を再確認しました。
