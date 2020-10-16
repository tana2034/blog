---
title: "[rails]Enumについて学ぶ"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# [rails]Enumについて学ぶ

railsの仕様として用意されているenumについてメモ

https://railsguides.jp/active_record_querying.html#enums
https://edgeapi.rubyonrails.org/classes/ActiveRecord/Enum.html

整数のカラムを値の集合にマッピングできる。
下記の例で言うと、`availability`と言う整数のカラムに対して、`:available`, `:unavailable`と言う値をマッピングさせている

```Ruby
class Book < ApplicationRecord
  enum availability: [:available, :unavailable]
end
```

こうすると、検索の時以下のように書ける

```Ruby
# 下の両方の例で、利用可能な本を問い合わせている
Book.available
# または
Book.where(availability: :available)

book = Book.new(availability: :available)
book.available?   # => true
book.unavailable! # => true
book.available?   # => false
```

割り当てられる整数は自動で0から始まるけれど、一応任意で指定もできる。

```Ruby
class Conversation < ActiveRecord::Base
  enum status: { active: 0, archived: 1 }
end
```