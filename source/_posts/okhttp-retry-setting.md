---
title: OkHttpのリトライ無効化
date : 2019-12-03 17:52:05
---

## 状況
Retrofitで通信失敗した時にリトライしてくれてることに最近気がつきました。デフォルトで2回リトライしてくれるようです。
しかし、通信箇所によってはリトライしたくない時もあります。そんな時の対処法について調べました。

## 実装
OkHttpClient作成時に下記のように`retryOnConnectionFailure(false)`を設定してあげるとOkHttpClientを使って通信を行う時にリトライしなくなります。

```java
public static OkHttpClient createOkHttpClient(Context context) {
    return new OkHttpClient.Builder()
            .connectTimeout(10, TimeUnit.SECONDS)
            .readTimeout(10, TimeUnit.SECONDS)
            .retryOnConnectionFailure(false)
            .build();
}
```

## 補足
どんな通信エラーの時でもリトライする訳ではなく、タイムアウトなどの特定のエラーの時のみリトライするようです。
詳しくは[この辺りのコード](https://github.com/square/okhttp/blob/02958768ed9f19f78fa3060ea22c8025c0232242/okhttp/src/main/java/okhttp3/internal/http/RetryAndFollowUpInterceptor.kt#L163)を見ると分かります。

## 参考URL
https://github.com/square/okhttp/pull/1259