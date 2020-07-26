---
title: "[ruby]Object#itself"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# [ruby]Object#itself

https://docs.ruby-lang.org/ja/latest/method/Object/i/itself.html

selfを返します…と書いてあるけれど、用途があまりわかっていない。

```ruby
1.itself       # => 1
'Hello'.itself # => 'Hello'
 
object = Object.new
object.object_id == object.itself.object_id # => true
```

[ついに来ました！恒等関数: Kernel#itself](https://www.techscore.com/blog/2014/09/29/ruby-2-2-0-preview1-%E3%81%A4%E3%81%84%E3%81%AB%E6%9D%A5%E3%81%BE%E3%81%97%E3%81%9F%EF%BC%81%E6%81%92%E7%AD%89%E9%96%A2%E6%95%B0-kernelitself-%E3%81%AA%E3%81%A9%E3%81%AA%E3%81%A9/)
に例があって、自分自身の値でグループ化したい時など綺麗にかける。

```ruby
values = [5, 7, 3, 7, 7, 5, 1, 1, 1, 0]
 
# Ruby 2.2.0 未満
values.group_by{|x| x} # => {5=>[5, 5], 7=>[7, 7, 7], 3=>[3], 1=>[1, 1, 1], 0=>[0]}
 
# Ruby 2.2.0 以降
values.group_by(&:itself) # => {5=>[5, 5], 7=>[7, 7, 7], 3=>[3], 1=>[1, 1, 1], 0=>[0]}
```

恒等関数とはなんぞやと思ったけれど、数学用語で`f(x) = x`となるfを恒等関数というみたい。