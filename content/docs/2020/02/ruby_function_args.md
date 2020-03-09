---
title: "[Ruby]メソッドの引数"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# [Ruby]メソッドの引数

## 通常
```Ruby
def foo(a)
    p a
end

foo("good")
# => good
```

## ブロック引数
ブロックは通常引数に指定しなくてもどんなメソッドにも渡せる
```Ruby
def foo
    yield
end

foo do puts "yield block" end
# => "yield block"
```

ただし引数の中で明示する場合は下記のように指定する
```Ruby
# &をつけた引数の指定でブロックを受け取る。ブロックの指定は必ず引数の順番として一番後ろにする
def need_block(a, b, &my_block)
    my_block.call(a + b)
end

need_block(1, 2) do |num| puts "block called! #{num}" end
# => "block called! 3"

need_block(1, 2)
# => NoMethodError (undefined method `call' for nil:NilClass)
```

ただし、&をつけても必須になるというわけじゃないので、使わなかったら使わないでも良い
```Ruby
def non_need_block(a, &b)
    # blockを呼ばない
    puts a
end

non_need_block("needless")
# => "needless"

# block以外のものを渡すとArgumentErrorになる
non_need_block(1, 2)
# => ArgumentError (wrong number of arguments (given 2, expected 1))
```

## 可変長引数

### 配列に展開する
引数に*をつけると配列として受け取れる
```Ruby
def variadic_function(a, *b) 
    puts b
end

variadic_function(1, 2, 3)
# => 2
# => 3
```

## キーワード引数
指定した変数名を使える、デフォルト値の設定も
```Ruby
def keyword_arg_function(keyword: 'default value')
    puts keyword
end

keyword_arg_function(keyword: 'new value')
# => new value
keyword_arg_function()
# => default value

# デフォルト値がない場合…？
def no_default_function(keyword: )
    puts keyword
end

no_default_function(keyword: 'no default')
# => no default
no_default_function()
# => ArgumentError (missing keyword: keyword)
```

## 変数の展開
ハッシュ値を引数として展開できる

[Ruby 2.7: ハッシュからキーワード引数への自動変換が非推奨に](https://techracho.bpsinc.jp/hachi8833/2019_10_31/82588)
```Ruby
def hash_func(key1:, key2:)
    puts key1
    puts key2
end

hash_words = { key1: 'first keyword', key2: 'second keyword' }
# **で展開
hash_func(**hash_words)
# => first keyword
# => second keyword
```

## 参考
https://techracho.bpsinc.jp/hachi8833/2017_04_06/38498
