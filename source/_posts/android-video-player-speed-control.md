---
title: Androidで動画の再生速度を変更する
date: 2020-08-21 21:00:00
tags: Android
---

`MediaPlayer`の動画再生速度をいじりたかったけど、アプリの`minSdkVersion`が21で[setPlaybackParams](https://developer.android.com/reference/android/media/MediaPlayer#setPlaybackParams(android.media.PlaybackParams))を使えない（左記メソッドはAPI level 23以上必要）時の対処法について書きます。

結論から言うと[ExoPlayer](https://github.com/google/ExoPlayer)を使いました。
既に画面がある場合は辛いかもですが新規で動画プレイヤー画面を作る場合は、MediaPlayer・ExoPlayerどちらを使っても実装工数は変わらない印象です。
機能的にはExoPlayerの方が柔軟にカスタマイズできて便利そうです。詳しくは[こちら](https://exoplayer.dev/pros-and-cons.html)
ExoPlayerの場合は[setPlaybackParameters](https://exoplayer.dev/doc/reference/com/google/android/exoplayer2/testutil/Action.SetPlaybackParameters.html)を使って動画の再生速度を変更できました。



## 参考サイト
https://stackoverflow.com/questions/10849961/speed-control-of-mediaplayer-in-android
https://codelabs.developers.google.com/codelabs/exoplayer-intro/#0
