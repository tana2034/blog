---
title: "[rails]ActiveRecord::Calculations"
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# [rails]ActiveRecord::Calculations

pluckについて知りたいと思っていたので、pluckについて学ぶついでに、pluckが入っているCalculationsのmoduleについて学びました

[ActiveRecord::Calculations](https://api.rubyonrails.org/classes/ActiveRecord/Calculations.html)

## Calculationモジュールはcalculate系とpluck系に分けられる

- calculate系：最終的にcalculateを使う（average, count, maximum, minimum, sum)
- pluck系：最終的にpluckを使う（pick, ids)

### calculate系

#### [average](https://api.rubyonrails.org/classes/ActiveRecord/Calculations.html#method-i-average)

```Ruby
Person.average(:age) # => 35.8
```

#### [count](https://api.rubyonrails.org/classes/ActiveRecord/Calculations.html#method-i-count)

```Ruby
Person.count
# => 234
```

groupと一緒に使うと、groupごとに集計したHashを返す

```Ruby
Person.group(:city).count
# => { 'Rome' => 5, 'Paris' => 3 }
```

#### [maximum](https://api.rubyonrails.org/classes/ActiveRecord/Calculations.html#method-i-maximum)

```Ruby
Person.maximum(:age) # => 93
```
#### [minimum](https://api.rubyonrails.org/classes/ActiveRecord/Calculations.html#method-i-minimum)

```Ruby
Person.minimum(:age) # => 7
```

#### [sum](https://api.rubyonrails.org/classes/ActiveRecord/Calculations.html#method-i-sum)

```Ruby
Person.sum(:age) # => 4562
```

### pluck系

#### [pluck](https://api.rubyonrails.org/classes/ActiveRecord/Calculations.html#method-i-pluck)
https://railsguides.jp/active_record_querying.html#pluck
https://techracho.bpsinc.jp/hachi8833/2018_09_26/62333

```Ruby
Person.pluck(:name)
# instead of 
Person.all.map(&:name)
```

pluckはデータベースから受け取った結果を直接Rubyの配列に変換するので、ActiveRecordオブジェクトを用意する必要がない

```Ruby
Person.pluck(:name)
# SELECT people.name FROM people
# => ['David', 'Jeremy', 'Jose']
```

pluckは直接クエリをトリガーするので、pluckの後に条件をチェインできないが、pluckの前に追加することはできる

```Ruby
# これはダメ
Client.pluck(:name).limit(1)
# これはOK
Client.limit(1).pluck(:name)
```

#### [ids](https://api.rubyonrails.org/classes/ActiveRecord/Calculations.html#method-i-ids)

https://railsguides.jp/active_record_querying.html#ids

primary keyの配列を返す、内部ではpluckを読んでる

```Ruby
Person.ids # SELECT people.id FROM people
```

#### [pick](https://api.rubyonrails.org/classes/ActiveRecord/Calculations.html#method-i-pick)

```Ruby
Person.where(id: 1).pick(:name)
```