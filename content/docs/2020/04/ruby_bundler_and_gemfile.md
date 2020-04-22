---
title: "[Ruby]Bundlerの使い方"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# [Ruby]Bundlerとは

## Bundlerとはなんなのか

Gemの依存関係を解決するためのgem。
プロジェクトの依存関係を宣言した`Gemfile`を元に、それらを取得する。

### どうやって取得するの？
`rubygems.org`（またはsourceに指定されている箇所）に接続して、`Gemfile`で指定した条件をすべて満たす必要なGemのリストを見つけてインストールします。
既に同じものがあった場合はそれを利用し、インストールしたすべてのバージョンを`Gemfile.lock`に書き込む。

### Gemfile.lockと環境の共有
Gemfile.lockが作成されることによって、他ユーザの環境や、開発環境に同じGemを入れることの保証ができる。
例えば、Gemfileにバージョン指定を`>= 0.4`として記載していた時、
`1.5.2`がインストールされたとする.
これがGemfile.lockに記載され、それを元にインストールされるので、他の環境で勝手に`2.0.0`が入ったりしないということになる。

### `bundle install` `bundle update`

#### `bundle install`
https://bundler.io/v2.0/man/bundle-install.1.html
bundle installは、
- `Gemfile.lock`がなければ、依存関係を解決してGemをinstallする。
- `Gemfile.lock`があって、`Gemfile`に更新がなければ、`Gemfile.lock`にしたがってGemをinstallする



#### `bundle update`
bundle updateは、`Gemfile.lock`を無視して対象のGemの依存関係を解決してGemをインストールする

##### `bundle update --conservative`の方がいい場合があるのはどうして？
`bundle update`は複数のGemが依存しているGemの更新も行ってしまうので、そのようなことを意図していない時は`--conservative`オプションをつけた方が良い。（[この辺](https://bundler.io/v2.0/man/bundle-update.1.html#OVERLAPPING-DEPENDENCIES)が参考になる)

リンク先の例に基づくと、gemの`thin`と`rack-perftools-profiler`はどちらも`rack`に依存している
```ruby
source "https://rubygems.org"

gem "thin"
gem "rack-perftools-profiler"
```
このGemfileがある状態で`bundle install`すると、`thin`は`rack >= 1.0`で、`rack-perftools-profiler`は`rack ~> 1.0`が条件なので、それを満たす`rack`がインストールされる

```bash
Fetching source index for https://rubygems.org/
Installing daemons (1.1.0)
Installing eventmachine (0.12.10) with native extensions
Installing open4 (1.0.1)
Installing perftools.rb (0.4.7) with native extensions
Installing rack (1.2.1)
Installing rack-perftools_profiler (0.0.2)
Installing thin (1.2.7) with native extensions
Using bundler (1.0.0.rc.3)
```
その後、`thin`のバージョンを上げたくなって、`bundle update thin`を実行すると、`thin`が依存しているGemである`daemons` `eventmachine` `rack`もアップデートされる。ただし、`rack-perftools_profiler`が依存している他の`open4`や`perftools.rb`はアップデートされない。なので、`rack-perftools_profiler`が依存しているGemのバージョンを意図せず上げてしまった可能性がある。

これを防ぐためには、
- `bundle update`を使わず、Gemfileに直接アップデートしたいバージョンを記載し、`bundle install`を実行すると、CONSERV ATIV　UPDATING機能により、`daemons`や`eventmachine`は更新されるが、`rack`は更新されなくなる。
- `bundle update --conservative`を利用しても、やはり共有されている依存関係は変更されないようになる。

##### 色々試す

- rails 4.1.0.rc2 gem -> actionpack 4.1.0.rc2 gem -> rack ~> 1.5.2 (which means >= 1.5.2 and < 1.6.0). 
- rack-cache gem -> rack >= 0.4. 

```ruby
# Gemfile
...
gem 'rails', '4.1.0.rc2'
gem 'rack-cache', '1.1'
```

この時、両方のGemの条件を満たす、rack 1.5.5がインストールされる

```ruby
     rack (1.5.5)
```

### 同じように条件を満たすrack 1.5.4をGemfileで指定した場合、通る？

```ruby
# Gemfile
...
gem 'rails', '4.1.0.rc2'
gem 'rack-cache', '1.1'
gem 'rack', '1.5.4'
```

- bundle installすると下記メッセージがでる

```bash
Fetching gem metadata from https://rubygems.org/.............
Fetching gem metadata from https://rubygems.org/.
You have requested:
  rack = 1.5.4

The bundle currently has rack locked at 1.5.5.
Try running `bundle update rack`

If you are updating multiple gems in your Gemfile at once,
try passing them all to `bundle update`
```

既に、Gemfile.lockでバージョン1.5.5でロックされているから、`bundle update`を使わないといけない
-> bundle update rackで実行、更新された

### railsv6.0.2.2にすると、依存しているrackのバージョンが上がる…どうする？

- rails 6.0.2.2 -> actionpack 6.0.2.2 -> rack  "~> 2.0", ">= 2.0.8"
- とりあえず上げてみる

```ruby
#Gemfile
...
gem 'rails', ' 6.0.2.2'
gem 'rack-cache', '1.1'
gem 'rack', '1.5.4'
```

やはりrackが固定されているせいで引っかかる！
```bash
Bundler could not find compatible versions for gem "rack":
  In snapshot (Gemfile.lock):
    rack (= 1.5.4)

  In Gemfile:
    rack (= 1.5.4)

    rails (= 6.0.2.2) was resolved to 6.0.2.2, which depends on
      actionpack (= 6.0.2.2) was resolved to 6.0.2.2, which depends on
        rack (~> 2.0, >= 2.0.8)

Running `bundle update` will rebuild your snapshot from scratch, using only
the gems in your Gemfile, which may resolve the conflict.
```
どうすれば、6.0.2.2に上げられるか？
まず、Gemfileのrackのバージョンが縛りになっていることは間違いないので、これを2.0.8に上げてみる

```Ruby
gem 'rails', ' 6.0.2.2'
gem 'rack-cache', '1.1'
# rackを2.0.8にしてみる
gem 'rack', '2.0.8'
```

`bundle update rack`実行で成功

`rack-cache`について、なんの調整もしていないけど、`bundle update`時の条件を満たせばそれでいい

### gemspecはなんなの？

gemspecはGemを作るときに依存関係を記述するファイル。