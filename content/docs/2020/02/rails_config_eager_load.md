---
title: "[rails]config.eager_load"
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# [rails]config.eager_load

railsを使っていてたまに聞く言葉としてeager_loadがあり、どういうものなのかよく認識していなかったので調査しました。
ActiveRecordでの関連テーブルの一括読み込みのeager loadingはまた別でまとめます。

## `config.eager_load`は何か

[Rails Applicationを設定する](https://railsguides.jp/configuring.html)で`config.eager_load`にbool値が設定できます

    config.eager_load: trueにすると、config.eager_load_namespacesに登録された事前一括読み込み(eager loading)用の名前空間をすべて読み込みます。ここにはアプリケーション、エンジン、Railsフレームワ ークを含むあらゆる登録済み名前空間が含まれます。

Railsのアプリケーションを作成した時、デフォルトでは、`development.rb`は`false`, `production.rb`は`true`になっています。本番は迅速なレスポンスを返し、開発環境ではメモリ消費を抑えることを意識しているものと思います。

### `config.eager_load = false`のとき、何が起こるのか

参考）[Railsの config.eager_load はModelやControllerを読み込むタイミングを制御できる](https://qiita.com/shakemurasan/items/305bd3d78d67b646bc06)

`config.eager_load = false`の場合、クラスが呼び出されるまでクラスが定義として存在しません

```Ruby
# 使用するまでUserクラスは存在しない
irb(main):001:0> Object.const_defined?('User')
# => false
irb(main):002:0> User.all
  User Load (6.8ms)  SELECT "users".* FROM "users" LIMIT $1  [["LIMIT", 11]]
# => #<ActiveRecord::Relation [#<User id: 1, name: "Rails Tutorial",... ]>
irb(main):003:0> Object.const_defined?('User')
# => true
```

試しに`config.eager_load = true`にして再度`rails console`を立ち上げてみます

```Ruby
irb(main):001:0> Object.const_defined?('User')
# => true
```
Userクラスを実際に使用する前に、定義を確認することができました

### コードリーディング

`railties/lib/rails/application/finisher.rb`

```Ruby
...
# initializerに名前とブロックを渡す
      initializer :eager_load! do
        if config.eager_load
          ActiveSupport.run_load_hooks(:before_eager_load, self)
          # Checks defined?(Zeitwerk) instead of zeitwerk_enabled? because we
          # want to eager load any dependency managed by Zeitwerk regardless of
          # the autoloading mode of the application.
          Zeitwerk::Loader.eager_load_all if defined?(Zeitwerk)
          config.eager_load_namespaces.each(&:eager_load!)
        end
      end
...
```

このZeitwerkを使ったeager_load_allは[Zeitwerkモード](https://railsguides.jp/autoloading_and_reloading_constants.html#zeitwerk%E3%83%A2%E3%83%BC%E3%83%89%E3%82%92%E6%9C%89%E5%8A%B9%E3%81%AB%E3%81%99%E3%82%8B)で使用される処理で、これはRails6.0以降で導入されたもの。

rails 4.2.7のとき
```Ruby
    initializer :eager_load! do
        if config.eager_load
        ActiveSupport.run_load_hooks(:before_eager_load, self)
        config.eager_load_namespaces.each(&:eager_load!)
        end
    end
```
namespacesごとのeager_loadを実行しているぽい、このeager_loadの実態は


