---
title: "[Ruby]ブロックについて学ぶ"
weight: 1
# bookFlatSection: false
# bookToc: 6
# bookHidden: false
---

# [Ruby]ブロックについて学ぶ

## ブロック…？

Rubyを学び始めて、ブロックという概念が出てきて戸惑ったので、色々調べてみました

## ブロックってなんだっけ

ブロックというのは、メソッド呼び出しのときに渡すことのできる処理の塊のこと。
一番よく見るのはイテレート

```Ruby
[1, 2, 3].each do |i|
   puts i 
end
```

これの`do`から`end`の部分がブロック

```Ruby
[1, 2, 3].each { |i| puts i }
```
こういう書き方も可能、この場合は`{}`内が全てブロック

### イテレート以外でも

メソッド呼び出しの際に渡す処理なので、イテレートじゃなくてもyieldを使えば、渡したブロックを呼び出してあげられる

```Ruby
def block_test
  yield(100)
end

block_test do |num|
  puts num * num
end

# 10000
```

多分なんのメソッドに対しても渡せる、渡しても何も起きないだけと思われる

```Ruby
def aaa
  puts "Hello"
end

aaa do puts "World" end

# yieldがないのでブロックの中身は実行されない
# "Hello"
```

### ブロックがある時もあれば、ない時もありそう

`block_given?`でブロックが渡されてるかわかるので、処理を分岐させることも可能

## ブロックはProcとは違うのか

Procはオブジェクトだが、ブロックはオブジェクトでない。
[ブロックをカプセル化したものがProc](https://docs.ruby-lang.org/en/2.6.0/Proc.html)


## ブロックでのreturnの扱い

ブロック内でreturnすると、ブロックを呼び出しているメソッドを字句的に囲っているメソッドが終了する

```Ruby
def count_up_from_1_to_3
  yield(1)
  yield(2)
  yield(3)
end

def test_return
  count_up_from_1_to_3 do |i|
    return if i == 2
    puts i
  end
  puts "end"
end

test_return

# 1
# returnするとそこでtest_returnメソッド自体が終了する...

def test_next
  count_up_from_1_to_3 do |i|
    # returnをnextに変更
    next if i == 2
    puts i
  end
  puts "end"
end

test_next

# 1
# 3
# end
# test_nextの最後まで処理が終わった
```

