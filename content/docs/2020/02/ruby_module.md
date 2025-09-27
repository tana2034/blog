---
title: "[Ruby]Moduleとは"
# bookFlatSection: false
# bookToc: 6
# bookHidden: false
---

# [Ruby]Moduleとは

Moduleの使い方に悩んだので学んでみました

## ModuleとClassの違い

- Moduleのインスタンスは作ることができない
- Moduleのサブクラスは作れない（継承できない）
- ClassはModuleのサブクラス
    - Moduleはサブクラス作れないはずなのだが…

## Moduleの用途

### 1. 名前空間として利用

```Ruby
module A
    module B
        class C
            def foo
            end
        end
    end
end

# これは下記と同じなんだけど、よくない感じ
# https://github.com/rubocop-hq/ruby-style-guide#namespace-definition
module A
    module B
    end
end
class A::B::C
    def foo
    end
end
```

### 2. mix-inとしての利用

#### moduleの機能をClassから呼べる

```Ruby
module M
    def foo
        puts "module was called"
    end
end

class C
    include M
end

i = C.new
i.foo
# => "module was called"
i::foo
# => NameError (undefined local variable or method `i' for main:Object)
```

あれ、クラスメソッドとしては使えない…と思ったけど、そもそもクラスメソッドとして定義していなかった

```Ruby
module M
    def self.bar
        puts "this is class method"
    end
end

class C
    include M
end

C::bar
# NoMethodError (undefined method `bar' for C:Class)
M::bar
# => "this is class method"
```
やっぱり無理だった
調べてみるとextendsだと[クラスメソッドとして追加されそう](http://unageanu.hatenablog.com/entry/20080413/1208083475)

```Ruby
module M
    def bar
        puts "this is class method"
    end
end

class C
    extend M
end

C::bar
# => this is class method
# できた！
i = C.new
i.bar
# => NoMethodError (undefined method `bar' for #<C:0x00007fa7cc6b70d0>)
# インスタンスメソッドとしては呼べない
```
こういうのは、特異メソッドとして追加される、というらしい
[特異メソッド定義](https://docs.ruby-lang.org/ja/latest/doc/spec=2fdef.html#singleton_method)

##### prepend

ついでにprepend
prependの意味は、メソッドを読み込む優先順位が分かっていないと多分わからない

```Ruby
module M1
    def foo
        puts "M1.foo"
    end
end
module M2
    def foo
        puts "M2.foo"
    end
end

class C
    include M1, M2
    def foo
        puts "C.foo"
    end
end

i = C.new
i.foo
# => C.foo
C.ancestors
# => [C, M1, M2...]
```
こんなふうに、同名で定義されたメソッドが合った場合、クラスで定義されているものが使われる。
もしクラスに同名の定義がなかったら、ancestorsで並んだ順の通りに使用される。

prependで読み込むとクラスでの定義を上書きできる。

```Ruby
class C
    prepend M1, M2
    def foo
        puts "C.foo"
    end
end

i = C.new
i.foo
# => M1.foo
C.ancestors
# => [C, M1, M2...]
```

#### モジュールとクラス変数、インスタンス変数

モジュールはクラスの変数にアクセスできるのか

```Ruby
module M
    def instance_variable(i)
        @i = i
    end

    def singleton_variable(s)
        @@s = s
    end
end

class C
    include M

    def print_variables
        puts @i
        puts @@s
    end
end
i = C.new
i.instance_variable("aaaa")
i.singleton_variable("bbbb")
i.print_variables
# => aaaa
# => bbbb
j = C.new
j.print_variables
# =>
# => bbbb
```

できた。
でもモジュール側でクラスの変数へアクセスするソースコード書くと密結合感ややある気がする
変数の持ち回しはしたいときありそうだけど…