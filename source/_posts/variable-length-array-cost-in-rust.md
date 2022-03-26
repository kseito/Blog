---
title: Rustの可変長配列を使用するときのコストについて
date: 2022-02-26 16:00:00
tags: Rust
---

Rustで[アルゴリズム](https://github.com/E869120/math-algorithm-book)の問題を解いていた時に、計算量的には通るはずのコードが通らず…配列の操作が怪しそうだったので配列の追加・削除の速度について調査しました。
前提として、今取り組んでいるアルゴリズムの問題は実行時間を1sに収める必要があるかつ計算回数が約350万回です。
100万回操作を行うコードを書いて処理にかかった時間に3.5をかけ、1000msを超えてるケースがないか確認しました。

## 環境
IntelliJ IDEA 2021.1.3
rustc 1.46.0 (04488afe3 2020-08-24)

## 固定長配列の要素追加
実行したコードは下記になります。

```rust
let mut array = [0; 1_000_000];
let start_time = SystemTime::now();
for i in 0..1_000_000 {
    array[i] = i
}
println!("{}", SystemTime::now().duration_since(start_time).unwrap().as_millis());
```

計測結果は
1回目→42ms
2回目→51ms
3回目→43ms
で平均は45.3msでした。
45.3 * 3.5 = 約160msなので原因にはなりづらそうです。

## 可変長配列の要素追加
実行したコードは下記になります。

```rust
let mut array = Vec::new();
let start_time = SystemTime::now();
for i in 0..1_000_000 {
    array.push(i);
}
println!("{}", SystemTime::now().duration_since(start_time).unwrap().as_millis());
```

計算結果は
1回目→62ms
2回目→63ms
3回目→63ms
で平均は62.7msでした
62.7 * 3.5 = 約220msでこれも原因ではなさそうです。

## 可変長配列の要素削除（先頭）
実行したコードは下記になります。

```rust
let mut array = Vec::new();
for i in 0..1_000_000 {
    array.push(i);
}
let start_time = SystemTime::now();
for i in 0..1_000_000 {
    array.remove(0);
}
println!("{}", SystemTime::now().duration_since(start_time).unwrap().as_millis());
```

計算結果は
1回目→124906ms
2回目→107748ms
3回目→99213ms
で平均は110622.3msでした。
完全にこれが原因でした。
VectorでQueueのような挙動を実現しようとして`remove()`を使っていたのですが悪かったようです。

## 【おまけ】可変長配列の要素削除（末尾）
Vectorはスタックとして扱うことができ`push()`と対となる`pop()`が準備されています。
それを使ったときの速度が`remove()`と同じになるか気になったので試してみました。
実行したコードは下記になります。

```rust
let mut array = Vec::new();
for i in 0..1_000_000 {
    array.push(i);
}
let start_time = SystemTime::now();
for i in 0..1_000_000 {
    array.pop();
}
println!("{}", SystemTime::now().duration_since(start_time).unwrap().as_millis());
```

計算結果は
1回目→82ms
2回目→72ms
3回目→72ms
で平均は75.3msでした。
この速度の違いはいったい…
Rustのコードを見てみたら納得しました。
`pop()`は単純に今持っている配列のサイズ-1番目の要素を削除するだけですが、`remove()`はindexで指定した位置の要素を削除した後に、指定した位置より後ろにある要素たちをコピーして削除した要素を埋める形でずらしているようです。
今回の場合、indexの指定が常に0なのでremoveとの相性がかなり悪かったです。