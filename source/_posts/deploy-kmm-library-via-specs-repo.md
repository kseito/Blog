---
title: Kotlin Multiplatform Mobileで作成したライブラリをSpecs Repoを使って配布する時の設定
date: 2022-09-13 12:00:00
tags:
- KMM
- Cocoapods
---

KMMを使ってライブラリを作成し配布する際に、デフォルトのpodspecファイルだとLinterでエラーになり配布できませんでした。
試行錯誤した結果、下記のような修正が必要でした。

## spec.sourceの変更
### Before
`{ :http=> ''}`

### After
`{ :git => "git@github.com:ユーザー名/リポジトリ名.git", :tag => spec.version }`

## spec.preserve_pathsの追加

`spec.preserve_paths = "**/*.*"`

## spec.vendored_frameworksの変更
### Before
`'build/cocoapods/framework/shared.framework'`

### After
`"shared/build/cocoapods/framework/shared.framework"`

## spec.script_phasesの変更
### Before
`"$REPO_ROOT/../gradlew" -p "$REPO_ROOT" $KOTLIN_PROJECT_PATH:syncFramework \`

### After
`"$REPO_ROOT/gradlew" -p "$REPO_ROOT" :shared:syncFramework \`

## 参考サイト
https://satoshun.github.io/2021/02/kmm-cocoapods-external-source/