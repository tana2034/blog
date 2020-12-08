---
title: "[ruby]begin"
date: 2020-12-08T18:54:54+09:00
---

# [ruby]begin

rubyの例外処理について学ぶ

```Ruby
begin
  do_something
rescue
  recover
else 
  xxx
ensure
  must_to_do
end
```

`rescue`の後ろには、エラータイプが指定できる。
エラータイプが省略された場合は、StandardErrorのサブクラスである全ての例外を補足する。


## else節

本体の実行でエラーが発生しなかった場合に評価される

## ensure節

begin 式を終了する直前に必ず ensure 節の本体を評価する
begin式全体の評価値は、**本体／rescue節／else節のうち最後に評価された文の値**となる
各節において文が存在しなかったときの値はnilで、いずれにしてもensure節の値は無視される

## beginはなしでもできる

クラスやmoduleの定義の中では、begin句がなくてもrescue句を書くことができる