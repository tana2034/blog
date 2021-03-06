---
title: "[Ruby]メモ化"
weight: 1
# bookFlatSection: false
# bookToc: 6
# bookHidden: false
---

# [Ruby]メモ化とは

たまにメモ化という言葉を聞くので、なんだろうと思って調査してみました

## メモ化とは

関数呼び出しの結果をキャッシュすることを指す関数プログラミング用語

- [Wikipedia メモ化](https://ja.wikipedia.org/wiki/%E3%83%A1%E3%83%A2%E5%8C%96)

Rubyの機能ではなく、実装方針というか、開発手法の一つのようです

### lambdaを使ったメモ化

```Ruby
# すごく雑にかくとこういう感じと思う

cache = {}

cubed = lambda { |x|
    if cache.has_key?(x)
        puts "use cache"
    else
        cache[x] = x * x * x
    end
    cache[x]
}

cubed.call(3)
# この時点でキャッシュはないので、計算された値が帰ってくる
# => 27
cubed.call(3)
# キャッシュが既に作られているため、計算せずに帰ってくる
# => "use cache"
# => 27
cubed.call(4)
# この値はキャッシュがないので、また計算された値が帰ってくる
# => 64
```

### モジュールでメモ化

[こちら](https://mickey24.hatenablog.com/entry/20100906/1283769623)でやっていたのが参考になった

## Railsにおけるメモ化

### ActiveSupport::Memoizable

- こちらは現在は[非推奨のモジュール](https://apidock.com/rails/ActiveSupport/Memoizable)になっており、代わりに `@var ||= pattern instead...` のような書き方を推奨している
- モジュールとしてどうしても使用したい場合、[おすすめされている](https://apidock.com/rails/ActiveSupport/Memoizable/memoize#1317-this-has-been-deprecated-replace-with-Memoist)のは、[memoist](https://github.com/matthewrudy/memoist)

```Ruby
# memoistを使用した実装例
require 'memoist'
class Person
  extend Memoist

  def social_security
    puts "execute!"
    decrypt_social_security
  end
  memoize :social_security
end
```
こんな感じで使える

## メモ化のメリット/デメリットはなんなのか

### メリット

キャッシュしておくことによる高速化と思われる

上記の例だと計算量が少なくてあまりメリットが感じられないが、フィボナッチ関数の実装などだと効果が見えやすい

### デメリット

デメリットというか、引数に関して必ず一定の値を返す関数でないと、メモ化は使えない

また、キャッシュしておくため、メモリの使用量などが大きい

