---
title: "[Rspec] 定数のスタブ"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# [Rspec] 定数のスタブ

RSpecでは定数をスタブすることができる
テストの時のみ、変更したい定数がある時など有効だと思う

https://relishapp.com/rspec/rspec-mocks/docs/mutating-constants
これを読む

## stubする

### 基本

```Ruby
stub_const("Foo", fake_foo)
Foo # => fake_foo
```

#### ネスト

moduleのネストは考慮されないので、ネストされている場合はそれを書く必要がある

```Ruby
module A
    class B
    end
end

module A
    describe do
        it { stub_const('B', Class.new) }
        # B classが対象にならない

        it { stub_const('A::B', Class.new) }
        # ちゃんとフルで書く
    end
end

```

### スタブしたClassの定数だけ参照する `:transfer_nested_constants`

これは、サイトに載っているサンプルがわかりやすいのでそのまま参照しているのだけど、
スタブしたClassについて、定数を参照したい場合、`:transfer_nested_constants`を利用するとその定数が使えるようになる。
例えば、外部ライブラリをモックした時などに使えそう

```Ruby
class CardDeck
  SUITS = [:Spades, :Diamonds, :Clubs, :Hearts]
  NUM_CARDS = 52
end

fake_class = Class.new
stub_const("CardDeck", fake_class)
CardDeck # => fake_class
CardDeck::SUITS # => raises uninitialized constant error
CardDeck::NUM_CARDS # => raises uninitialized constant error

stub_const("CardDeck", fake_class, :transfer_nested_constants => true)
CardDeck::SUITS # => [:Spades, :Diamonds, :Clubs, :Hearts]
CardDeck::NUM_CARDS # => 52

stub_const("CardDeck", fake_class, :transfer_nested_constants => [:SUITS])
CardDeck::SUITS # => [:Spades, :Diamonds, :Clubs, :Hearts]
CardDeck::NUM_CARDS # => raises uninitialized constant error
```

### 定数を隠す `hide_const`

環境変数が存在する場合/しない場合のテストに使えそう

```Ruby
FOO = 42
hide_const("FOO")
FOO # => NameError: uninitialized constant FOO
```