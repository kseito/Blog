---
title: Android Dev Summit 2019 Extended Tokyo参加レポート
date : 2019-11-16 09:58:14
tags: Android
---

## 概要
[Android Dev Summit 2019 Extended Tokyo](https://gdg-tokyo.connpass.com/event/152665/)に参加してきたのでその内容と感想を書こうと思います。


## セッション
### Session1: Conference Overview & Keynote Session

mhidakaさんのセッションです。スライドは[こちら](https://speakerdeck.com/mhidaka/android-dev-summit-2019-conference-overview-and-keynote-session)
カンファレンスの概要とキーノートの内容が主なコンテンツです。
その中で開発者が特に気をつけることとして、OSの開発フロー変更に伴いSDK Target(targetSdkVersionと同義？)の縛りが、最新OSのバージョン−１となることを挙げていました。要はAndroid 11で自分のアプリを動かすにはtargetSdkVersionが29(30 - 1)である必要があるということです。
発表の内容からはGoogleが「開発者がユーザーの安全を考慮しつつ様々なサイズのデバイスをより早く開発できるようにする」という意思が感じられました。


### Session2: Android Studio 4.0 最新アップデート

wasabeefさんのセッションです。スライドは[こちら](https://speakerdeck.com/wasabeef/whats-new-android-studio-4-dot-0-ja)
Android Studio 4.0やエミュレーターでできることについての発表です。
個人的にはjava.utilクラスのバックポート、Multi Previewあたりの機能が恩恵にあやかれそうです。
デモとしてGoogle Mapのナビのシミュレーションを行ってましたが、ポケモンGOはやるなよ？という振りましたがあり試すしかない案件では？


### Session3: かんたんべんりなMotionEditorの使い方講座

mochicoさんのセッションです。スライドは[こちら](https://speakerdeck.com/mochico/how-to-motion-editor-report-from-android-dev-summit-2019)
Motion Layoutの紹介＋アニメーション作成のデモです。
Motiion Editorを使うとアニメーションがGUIで作れてしまうという便利ツールです。プロパティも色々あり柔軟にアニメーションが作れそうです。
ObjectAnimatorを使ってコードでゴリゴリアニメーションの処理を書いて実機で確認していた身としては涙が出る有り難さです。


### Session4: What's new in CameraX

Takasyさんのセッションです。スライドは[こちら](https://docs.google.com/presentation/d/19v5_MrMQvtlrQrE7PXy8dZi7h_iFV0kFwQaJaXt5wEg/edit#slide=id.p)
Camera Xの紹介です。
が、Camera1しか使ったことがないせいなのか話についていけませんでした。
Camera Xは使いやすさと機種依存しないことに力を入れているため使いやすいぞということだけ理解しました。


### Session5: Jetpack Composeの解説です！

Yuki Anzaiさんのセッションです。スライドは[こちら](https://speakerdeck.com/yanzm/jetpack-compose-dounafalse-android-dev-summit-2019bao-gao-hui)
Jetpack Composeの紹介です。
Kotlinとアノテーションを使ってUIを作っていくツールキットで、スライド上のコードを見る限りとてもシンプルな作りになってます。
ただ、カスタムビューをComposeに追加したい時とか既存のビューとの兼ね合いがなかなか辛そうな印象でした。
発表を聴く限り、まだまだ開発途中で最終形がどうなるのか全く読めなさそうです。


### Session6: Jetpack Roomの最新情報が届きます！

Yuichi Arakiさんのセッションです。スライドは[こちら](https://docs.google.com/presentation/d/1AYKOriaW0MhScn6ODBvxaQpbY8k9jTw8ENLudz1f6Tw/edit#slide=id.p)
Roomの最新情報の紹介です。
Roomは全くキャッチアップできていなかったのですが、最新版ではCoroutines(suspend function)やLiveDataに対応していて以前より使いやすいくなってるように感じました。
また、Flowというクラスが準備されておりDBから取り出すデータを加工してから表示したいときに使えるようです。
DBから取り出してから表示するまでの手段がいくつもあって色々なニーズに答えられそうな実装になってました。


## 所感
近年のAndroid開発はJetpackの登場やAndroid Studioの進化でとても便利で開発しやすいものになったと感じてましたが、Android Studio 4.0でさらに開発しやすくなるのではないかと思わせる内容でした。Android開発はまだまだ加速できる余地が多々ありワクワクしますね。
このような機会を設けてくださったGDG Tokyoのスタッフ、登壇者、会場提供して頂いた会社さんに感謝:pray:
