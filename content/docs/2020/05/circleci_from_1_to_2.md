---
title: "[circleci]1から2へ移行する"
date: 2020-05-17T21:35:16+09:00
---

# [circleci]1から2へ移行する

下記のドキュメントを参考に、1から2へ移行する

https://circleci.com/docs/ja/2.0/migrating-from-1-2/

## circle.ymlの移行

これまで使用していた`circle.yml`ファイルを[`.circleci/config.yml`](https://circleci.com/docs/2.0/configuration-reference/)に移行していくことになる。

[このステップ](https://circleci.com/docs/ja/2.0/migrating-from-1-2/#steps-to-configure-required-keys)に従って変更する

### 移行ステップ

1. `circle.yml`を新しい`.circleci`ディレクトリに移動する
2. `circle.yml` -> `config.yml` にリネーム
3. `version: 2` の記載をファイル先頭に追加する
4. versionの記載の下に下記の二行を追加する。もし`machine: `という構成があったら、それを以下と置き換える。
```yml
jobs:
    build:
```
5.dockerイメージの記述をする。以下のように言語・バージョンを指定していた場合、dockerイメージとそのバージョンに置き換える。

```yml
# 移行前
  ruby:
    version:
```

```yml
# 移行後
  docker:
    - image: circleci/ruby:2.3-jessie
```

6. checkoutはstepsの配下にネストする。コマンドは `- run:` の後ろにかく

```yml
# 移行前
  checkout:
   post:
     - mkdir -p /tmp/test-data
     - echo "foo" > /tmp/test-data/foo
```

```yml
# 移行後
      steps:
        - checkout
        - run: mkdir -p /tmp/test-data
        - run: echo "foo" > /tmp/test-data/foo
```
また、`checkout:` の記述がなくても、`steps:` は追加しなくてはならない

7. (optional) 必要があれば `add_ssh_keys`を追加して、SSHができるようにしておく
8. 出来上がったYAMLを[バリデータ](http://codebeautify.org/yaml-validator) にかけてチェックする。

### Workflowsを構成する

https://circleci.com/docs/ja/2.0/workflows/

## Tips

https://circleci.com/docs/2.0/migration/

## 参考

[言語ガイド:Go](https://circleci.com/docs/ja/2.0/language-go/)
[2.0 config.ymlの設定例](https://circleci.com/docs/ja/2.0/sample-config/)
[Tips for Migrating to 2.0](https://circleci.com/docs/2.0/migration/)
[Configuring CircleCI](https://circleci.com/docs/2.0/configuration-reference/)