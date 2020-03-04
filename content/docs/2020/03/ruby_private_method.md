---
title: "[Ruby]Rubyのprivateメソッド"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# [Ruby]Rubyのprivateメソッド

## Rubyのprivateメソッドが思っていたのと違った

元々Javaをやっていたので、
- public -> 外部からアクセス可能
- protected -> 外部からはアクセスできないが、サブクラスからアクセス可能
- private -> クラス内のみ利用可能

と思っていたのですが、コーディングしていると、privateメソッドにサブクラスからアクセスできていて、あれ？となったので、改めてRubyのprivateメソッドについて調べました。

### 確認

```Ruby

class A
    def public_method
        puts "public"
    end

    protected

    def protected_method
        puts "protected"
    end

    private

    def private_method
        puts "private"
    end
end

class B < A
    def test
        public_method
        protected_method
        private_method
    end
end

b = B.new
b.public_method
# => public_method
b.protected_method
# => NoMethodError
b.private_method
# => NoMethodError
b.test
# => public_method
# => protected_method
# => private_method
```

やっぱりサブクラスから`private`メソッド呼べている

## Rubyのpublic, protected, privateメソッド

と思ったらやっぱり同じことが気になった人がいたようで、
Rubyのpublic, protected, privateメソッドについて書いてある記事があった。

https://blog.jnito.com/entry/20120315/1331754912
https://docs.ruby-lang.org/ja/2.1.0/doc/spec=2fdef.html#limit

現状としては、protectedはレシーバ経由でも使えるけど、privateメソッドは関数形式でしか呼べないというのが大きな違いな模様で、サブクラスからアクセスできるというポイントでは同じ。

```Ruby

class B < A
    def test_protected_receiver(a)
        a.protected_method
    end

    def test_private_receiver(a)
        a.private_method
    end
end


a = A.new
b = B.new
b.test_protected_receiver(a)
# => "protected_method"
# protectedはレシーバ経由でも呼び出せる
b.test_private_receiver(a)
# NoMethodError
# privateメソッドはレシーバ経由では呼び出せない
```

そういえば、privateメソッドは多く使われてるなーと会社のソースコード見てても思いました。あんまりprotectedの出番はないのかもしれない。

参考）https://docs.ruby-lang.org/ja/2.1.0/doc/spec=2fdef.html#limit