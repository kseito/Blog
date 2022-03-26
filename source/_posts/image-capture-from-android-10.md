---
title: Android 10でIntentを使ってキャプチャした画像を外部フォルダに任意のディレクトリを作って保存する
date: 2020-09-16 18:30:00
tags: Android
---

今更ながらAndroid 10以降で`Intent(MediaStore.ACTION_IMAGE_CAPTURE)`を使ってキャプチャした画像を、各メディア直下のディレクトリではなくアプリ専用のディレクトリを作って保存する方法について調べました。

## 準備
まずxmlの設定から。`AndroidManifest.xml`をいじります。
`AndroidManifest.xml`の`<application>`タグ内にFileProviderの記述を追加します。

```xml
<provider
    android:name="androidx.core.content.FileProvider"
    android:authorities="${applicationId}.provider"
    android:exported="false"
    android:grantUriPermissions="true">

        <meta-data
                android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/file_provider"/>
</provider>
```

`<meta-data>`タグ内で参照している`file_provider.xml`はres/xml配下に作成し中身は下記のようになっています。
```xml
<paths>
    <external-path name="images" path="Pictures/Hoge" />
</paths>
```

## Uri取得
次に画像の保存先をMediaStoreから取得します。

```kotlin
val fileName = "hoge.jpg"
val values = ContentValues()
values.put(MediaStore.Images.Media.DISPLAY_NAME, fileName)
values.put(
    MediaStore.Images.Media.RELATIVE_PATH,
    Environment.DIRECTORY_PICTURES + File.separator + "Hoge"
)
values.put(MediaStore.Images.Media.MIME_TYPE, "image/*")
val contentUri = MediaStore.Images.Media.getContentUri(MediaStore.VOLUME_EXTERNAL)
imageUri = contentResolver.insert(contentUri, values)
```

`RELATIVE_PATH`は`file_provider.xml`で記述した`path`の値と同じにします。
`imageUri`はプロパティとして保持しておき`onActivityResult`で再利用します。

## Intentを投げる
事前に取得しておいたUriをIntentにくっつけて投げます。

```kotlin
val intent = Intent(MediaStore.ACTION_IMAGE_CAPTURE)
intent.putExtra(MediaStore.EXTRA_OUTPUT, imageUri)
startActivityForResult(intent, REQUEST_CODE)
```

カメラアプリがいくつか候補に出てくると思うので好きなアプリを選択して写真を撮ります。

## 戻り値を受け取る
`ImageDecoder`というクラスを使って`Bitmap`を生成して、`ImageView`にセットすれば終わりです。

```kotlin
if (requestCode == REQUEST_CODE && resultCode == Activity.RESULT_OK) {
    val source = ImageDecoder.createSource(contentResolver, imageUri!!)
    val bitmap = ImageDecoder.decodeBitmap(source)
    findViewById<ImageView>(R.id.image).setImageBitmap(bitmap)
}
```

## 感想
公式動画やブログでも再三言われていたことですが、Google的にはファイルパスは極力使わないでUriを使って画像を扱って欲しいのだなと改めて思う内容でした。
近い将来、動画をファイルパスでしか読み込めないライブラリたちがUriをサポートしたらファイルパスの参照も禁止になってしまうのかもしれません。
そう考えるとできるだけファイルパスに依存しない実装にしておくのが吉なのでしょう。知らんけど。

## 参考サイト
- https://developer.android.com/about/versions/11/privacy/storage#other-apps-private-dirs
- https://developer.android.com/training/data-storage/app-specific#external
- https://developer.android.com/training/secure-file-sharing/setup-sharing?hl=ja
- https://stackoverflow.com/questions/56651444/deprecated-getbitmap-with-api-29-any-alternative-codes
