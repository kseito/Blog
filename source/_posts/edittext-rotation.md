---
title: 画面回転時のEditTextの入力内容の保持はどこで行われているのか
date: 2021-02-19 18:00:00
tags: Android
---

EditTextに文字列が入力がされた状態で画面回転を行うと何もしないでも文字列が回転後も保持されていて不思議だったのでどうやって保持しているの調査してみました。

結論からいうと、EditTextの`onSaveInstanceState`で保存されていました（より正確にいうと、EditTextはTextViewを継承しており、継承元のTextViewの`onSaveInstanceState`で保存処理が行われています）。
実際の処理を抜粋したのが以下のコード。

```java
SavedState ss = new SavedState(superState);

if (mText instanceof Spanned) {
    final Spannable sp = new SpannableStringBuilder(mText);

    if (mEditor != null) {
        removeMisspelledSpans(sp);
        sp.removeSpan(mEditor.mSuggestionRangeSpan);
    }

    ss.text = sp;
} else {
    ss.text = mText.toString();
}
```

保存していた文字列を画面回転後に再度EditTextにセットするのはEditText`onRestoreInstanceState`内の下記コード。
```java
if (ss.text != null) {
    setText(ss.text);
}
```

画面回転後にEditTextに入力されている文字列を元に再度通信クエリを実行する等の処理を行う場合は、`onRestoreInstanceState`以降の文字列がセットされたタイミングで行わないと空文字しか取得できずハマります。
