---
title: "[rails]N+1クエリ問題と解決方法"
date: 2020-12-07T17:51:39+09:00
---

# [Rails]N+1クエリ問題と解決方法

N+1クエリ問題について、よくわかっていなかったので調査してみました。

## N+1クエリ問題とは

> N+1クエリ問題とは、Tree 状の情報を DB から読み出す際、全レコードの取得に一つ＋各レコード分だけ SQL を発行してしまう問題

http://akasata.com/articles/103

```Ruby
Users.where(active: true).each do |user|
  user.items.each do |item|
    # この時、select * from items where id = ... というクエリがレコード数だけ投げられる
    puts item.name
  end
end
```

## railsでの対処方法(includes, preload, eager_load)

この問題は、railsの公式ドキュメントでも触れられています。

[N+1クエリ問題を解決する](https://railsguides.jp/active_record_querying.html#%E9%96%A2%E9%80%A3%E4%BB%98%E3%81%91%E3%82%92%E4%B8%80%E6%8B%AC%E8%AA%AD%E3%81%BF%E8%BE%BC%E3%81%BF%E3%81%99%E3%82%8B
)

https://moneyforward.com/engineers_blog/2019/04/02/activerecord-includes-preload-eagerload/

### includes

公式ドキュメントでは、このN+1クエリ問題を解決するためにこのincludesを利用することを紹介しています。

`includesを指定すると、Active Recordは指定されたすべての関連付けが最小限のクエリ回数で読み込まれるようにしてくれます。`

```Ruby
clients = Client.includes(:address).limit(10)

clients.each do |client|
  puts client.address.postcode
end
```
このとき発行されるクエリは`SELECT * FROM clients LIMIT 10`と `SELECT addresses.* FROM addresses WHERE (addresses.client_id IN (1,2,3,4,5,6,7,8,9,10))`の2件なので、相当減ったと言えると思います。

しかし、`最小限のクエリ回数で読み込まれる`というのはどういうことなのでしょうか。

これは、内部で`preload` と`eager_load`を場面によって使い分けているため、このように書かれているようです。

そうなると、`preload`と`eager_load`とはなんなのか、という話になってきます。

### preload

preloadは、関連テーブルを別クエリで取得する

```Ruby
User.preload(:posts)
# SELECT "posts".* FROM "posts" WHERE "posts"."user_id" IN (1, 2, 3)
```

メモリ使用量の点でeager_loadより有利なため、基本的にincludesを利用した際はpreloadが採用されるようです。

https://techracho.bpsinc.jp/hachi8833/2020_03_11/89510

### eager_load

https://railsdoc.github.io/classes/ActiveRecord/QueryMethods.html#method-i-eager_load

eager_loadは、関連テーブルを`LEFT OUTER JOIN`で取得する

```Ruby
User.eager_load(:posts)
# SELECT "users"."id" AS t0_r0, "users"."name" AS t0_r1, ...
# FROM "users" LEFT OUTER JOIN "posts" ON "posts"."user_id" =
# "users"."id"
```


### joins

LEFT OUTER JOINが出たので、では、INNER JOINにしたい場合はどうするかというと、`joins`を利用します。

```
User.joins(:posts)
# SELECT "users".*
# FROM "users"
# INNER JOIN "posts" ON "posts"."user_id" = "users"."id"
```

## では、どれを使えばよいのか

includesは、自身でどんなクエリを投げるか選択できないので、基本的には使わない方がよいでしょう、という意見が多く、それに同意します。
では、preloadとeager_loadについてはどちらがよいか？となるのですが、

https://moneyforward.com/engineers_blog/2019/04/02/activerecord-includes-preload-eagerload/

こちらだと、`has_many`の場合は `preload`、`has_one`場合は`eager_load`がよさそう、と一つの目安を上げています。
has_manyの場合にeager_loadをすると、スロークエリになりがちというのが主な理由です。

ただ、上記のサイトでもケースバイケースと述べられているので、
結局は一度クエリを投げてみてEXPLAINで見てみるのがいいんじゃないかな、と個人的には思います。

