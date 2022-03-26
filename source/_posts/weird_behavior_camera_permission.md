---
title: カメラパーミッションの罠
date : 2019-10-29 09:58:14
---

先日、Androidアプリにカメラのパーミッション追加したときに罠にハマったので備忘録として書いておこうと思います。

## 事象
1. QRコード読み取り機能を実装するために`zxing-android-embedded`を導入
2. カメラパーミッション（`android.permission.CAMERA`）をAndroidManifestファイルに追加
3. 既存アプリとQRコード読み取り画面のつなぎ込みをしてリリース！
4. 前々から実装してあったIntentでカメラ呼び出ししている箇所でSecurityExceptionが発生して死ぬ

## 原因
カメラパーミッションを追加した場合、`ACTION_IMAGE_CAPTURE`を使ってカメラアプリを呼び出す場合にもカメラへのアクセス権が許可されていないといけないです。

```java
Intent i = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
i.putExtra(MediaStore.EXTRA_OUTPUT, uri);
startActivityForResult(i, REQUEST_CAMERA);
```

今回の問題が発生した箇所は、上記のように暗黙的Intentを使ってカメラアプリの呼び出しを行っている場所です。
確かに[公式ドキュメント](https://developer.android.com/reference/android/provider/MediaStore.html#ACTION_IMAGE_CAPTURE)を見ると注意書きとして書いてありました・・・。

```
Note: if you app targets M and above and declares as using the Manifest.permission.CAMERA permission which is not granted, then attempting to use this action will result in a SecurityException.
```

書いてありましたが、事前に知っていないと割とどうしようもないのではと思う。
※`ACTION_VIDEO_CAPTURE`も同様です。

## 対策
原因がわかってしまえばあとは簡単。`ACTION_IMAGE_CAPTURE'`を使ってカメラアプリを呼び出す処理の前にカメラパーミッションを取得するダイアログ表示処理を入れてあげるだけです。

## 参考にしたサイト
https://developer.android.com/reference/android/provider/MediaStore.html#ACTION_IMAGE_CAPTURE
https://developer.android.com/reference/android/provider/MediaStore.html#ACTION_VIDEO_CAPTURE
https://stackoverflow.com/questions/32789027/android-m-camera-intent-permission-bug
https://stackoverflow.com/questions/43042725/revoked-permission-android-permission-camera