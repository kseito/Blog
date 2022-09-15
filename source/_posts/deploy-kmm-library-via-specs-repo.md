---
title: Kotlin Multiplatform Mobileで作成したライブラリをSpecs Repoを使って配布する時の設定
date: 2022-09-13 12:00:00
tags:
- KMM
- Cocoapods
---

KMMを使ってライブラリを作成しCocoapodsで配布する際に、デフォルトのpodspecファイルだとLinterでエラーになり配布できませんでした。
試行錯誤した結果、下記のような修正を行うと無事に配布することができました。

## spec.sourceの変更
初期値のままだとエラーになります。
URLを指定しましょう。

#### Before
`{ :http=> ''}`

#### After
`{ :git => "git@github.com:ユーザー名/リポジトリ名.git", :tag => spec.version }`

## spec.preserve_pathsの追加
preserve_pathsで指定されたファイル以外はダウンロード後に削除されてしまうようなので削除されたくないファイルを指定します。
下記のように全てのファイルを残すやり方でもいけました。

`spec.preserve_paths = "**/*.*"`

## spec.vendored_frameworksの変更
プロジェクトのルートから見た相対パスを指定する必要がありました。

#### Before
`'build/cocoapods/framework/shared.framework'`

#### After
`"shared/build/cocoapods/framework/shared.framework"`

## spec.script_phasesの変更
REPO_ROOT=プロジェクトのルートになるようなので、その想定で相対パスとGradleタスクのパスを変更します。

#### Before
`"$REPO_ROOT/../gradlew" -p "$REPO_ROOT" $KOTLIN_PROJECT_PATH:syncFramework \`

#### After
`"$REPO_ROOT/gradlew" -p "$REPO_ROOT" :shared:syncFramework \`

## 参考サイト
https://satoshun.github.io/2021/02/kmm-cocoapods-external-source/
