---
title: "[Ruby]Procとlambda"
weight: 1
# bookFlatSection: false
# bookToc: 6
# bookHidden: false
---

# [Ruby]Procとlambdaについて学ぶ

## Procとは

ブロックをオブジェクト化したもの。

```Ruby

p1 = Proc.new { |x, y| x + y }
# こういう書き方もできる
p2 = proc { |x, y| x + y }

# callで呼び出せる
p1.call(1, 2)
p2.call(3, 4)

p1.class
# => Proc < Object
```

## lambdaとは

同じようにProc型のオブジェクト。

```Ruby
l1 = lambda { |x, y| x + y}
# こういう書き方もできる
l2 = ->(x, y) {x + y}

l1.call(1, 2)
l2.call(3, 4)

l1.class
# => Proc < Object
```

## Procとlambdaの違いとは

### 1. 引数の制限があるかないか

#### Procは引数の厳密な制限はない

```Ruby
p = Proc.new { |x, y| print x, y}
p.call(1)
# => 1nil
p.call(1, 2, 3)
# => 12
# 3は無視される
```

#### lambdaは引数の個数が違うと使えない
```Ruby
p = lambda { |x, y| print x, y}
p.call(1)
p.call(1,2,3)
# どちらもだめ
# ArgumentError: wrong number of arguments (given 3, expected 2)
```

### 2. return, break時の挙動が違う

#### Procではreturn, breakはブロック同様、字句的に囲われている処理が終了する

```Ruby
def test_proc
    p = Proc.new { puts "doing"; return }
    puts "start"
    p.call
    puts "end"
end

test_proc
# => start
# => doing
# endは出力されない
```

#### lambdaではreturnするとlambda自身から戻る

```Ruby
def test_lambda
    p = lambda { puts "doing"; return }
    puts "start"
    p.call
    puts "end"
end

test_lambda
# => "start"
# => "doing"
# => "end"
# ちゃんとendまで出力される！
```

## 結局どちらを使うのが良いのか

どちらがよいという話は検索しても見当たらなく、どちらかというと性質の違いに触れたものが多かった。
特徴を大きくまとめると、Procはブロック的で、lambdaはメソッド的。
ブロックを持ち運べるオブジェクトを作るならProc,他の言語で期待されるようなラムダ式を作るときにはlambdaを使う方が近そう
