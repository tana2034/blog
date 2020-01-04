---
title: VSCodeでのRuby開発
---

VSCodeでRubyの開発をするための環境を整えたいと思います

## docker環境で開発する

私の場合はローカル環境での開発は、Ruby用のコンテナを作ってそこで動かしているので、その中で作業できると便利と思いました

VSCodeは少し前に[リモート環境での開発環境](https://code.visualstudio.com/docs/remote/remote-overview)についてリリースがあり、これを使ってみたいと思います

参考リンク
[https://code.visualstudio.com/docs/remote/containers](https://code.visualstudio.com/docs/remote/containers)

### やったこと

ちなみに mac OSです

#### 事前準備

下記をinstallしておきます
- Docker
- VSCode
- Remote Development extenstion pack

#### 早速コンテナにattachする

- VSCodeのcommand pallet で`Remote-Containers: Attach to Running Container`を選択する
- 接続したいコンテナを選択する
- 終わり

かんたん！

####  各Extensionをコンテナ内で使えるようにする

- これはあとの作業になりますが、VSCodeのExtensionをコンテナ内で使うためには、各Extensionを有効にする必要があります

## VSCodeにExtensionを追加

### Solargraph

コードの補完機能とか、ジャンプとかドキュメンテーションの参照とかができるようになるRubyのLanguage Serverです

#### gem install solargraph
事前にsolargraphをgem installしておきます
私の場合はGemfileに追加してインストールします、そうするとdockerなのでイメージをbuildし直す必要があります

```
# Gemfile

group  :development  do
	gem  "solargraph"
end
```
```
$ bundle install
$ bundle exec solargraph -v
0.38 # 入った
```

#### VSCodeにExtension追加

- VSCodeのExtensionsから「Ruby Solargraph」を選択してインストール
- 設定ファイルを修正しておきます

```
# setting.json
"solargraph.autoformat": true, 
"solargraph.useBundler": true, # bundle経由で使用する場合はtrue
"solargraph.diagnostics": true # rubocopによる警告など出したい場合はtrue
```

#### .solargraph.yml

- [この辺](https://solargraph.org/guides/configuration)を参考に.solargraph.ymlをワークスペースのルートに追加

```
# .solargraph.yml
include:
- "**/*.rb"
exclude:
- spec/**/*
- test/**/*
- vendor/**/*
- ".bundle/**/*"
require: []
domains: []
reporters:
- rubocop
- require_not_found
max_files: 5000
```

全部終わったらVSCodeを再起動すると使用できると思います

## 感想

### VSCodeの良いところ

- Remote Developmentの機能を使えば、ローカル環境に色々インストールしなくてもよくなる
	- 特にエディタ周りはコンテナとエディタのLintツールとかのバージョンの差異とかを管理しなくて良くなる（コンテナに開発環境が整ってるのに、エディタ側で文法チェックしたくてローカル環境にも結局同じバージョンをインストールしなきゃいけないとかあって嫌だった）
- solargraphを使えばコードの補完とかジャンプとか普通にできる

### 悩ましいところ
- Remote Developmentの機能、VSCodeをコンテナにアタッチするのがちょっと時間がかかるかも
- あと、開発しているとコンテナ内にも色々欲しくなってくる
	- vimとかzshにしたいとか…
	- そうなると、ローカル環境のままで良かったのでは感
- Railsに特化した形の補完は[Rubymineの方が強そう](https://blog.jetbrains.com/ruby/2008/11/rubymine-rails-specific-completion/)

### その他

- debuggerまで手が回らなかったのであとで調べたいです
- この辺の設定をあとでhttps://github.com/castwide/solargraph/issues/87
