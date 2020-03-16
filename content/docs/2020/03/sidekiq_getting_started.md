---
title: "[Sidekiq]はじめてのSidekiq"
date: 2020-03-16T15:32:11+09:00
draft: true
---

# [Sidekiq]はじめてのSideki
[これ](https://github.com/mperham/sidekiq/wiki/Getting-Started)を参考に学ぶ

## Sidekiqとは

RubyのBackground処理を行うためのフレームワーク。

## はじめてみる

### Workerを作る

```Ruby
class HardWorker
  include Sidekiq::Worker

  def perform(name, count)
    # do something
  end
end
```

### Workerを実行する

呼び出すときは、`perform`じゃなく、`perform_async`とか`perform_in`とかを使う
rails cとかirbで下記のように呼び出すと実行できる

```Ruby
HardWorker.perform_async('bob', 5)
```

### Basics
https://github.com/mperham/sidekiq/wiki/The-Basics

#### Client

Workerの処理を実行する書き方として、２つは同じ
```Ruby
MyWorker.perform_async(1, 2, 3)
Sidekiq::Client.push('class' => MyWorker, 'args' => [1, 2, 3])  # Lower-level generic API
```

#### Redis
https://github.com/mperham/sidekiq/wiki/Using-Redis

Redisにジョブをためておいて、実行させることができる

##### 初期設定

```Ruby
# config/initializers/sidekiq.rb
Sidekiq.configure_server do |config|
  config.redis = { url: 'redis://redis.example.com:7372/0' }
end

Sidekiq.configure_client do |config|
  config.redis = { url: 'redis://redis.example.com:7372/0' }
end
```

#### Server
省略

### Error Handling

https://github.com/mperham/sidekiq/wiki/Error-Handling
あとでよむ

### Advanced Options
https://github.com/mperham/sidekiq/wiki/Advanced-Options
あとでよむ

#### Workers options
`sidekiq_options`によって、Workerの挙動をかえられる

- queue: キュー名を設定
- retry: `true` `false`または最大リトライ数を設定できる
- backtrace: 
- pool: Redisのコネクションプール

```Ruby
class HardWorker
  include Sidekiq::Worker
  sidekiq_options queue: :crawler, retry: false, backtrace: true
  
  def perform(name, count)
  end
end
```
