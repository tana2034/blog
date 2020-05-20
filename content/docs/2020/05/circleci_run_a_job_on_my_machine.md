---
title: "[circleci]ローカルマシンでcircleciのジョブを動かす"
date: 2020-05-18T08:39:22+09:00
---

# [circleci]ローカルマシンでcircleciのジョブを動かす

[こちら](https://circleci.com/docs/2.0/local-cli/)を参考にCircleCIのジョブをローカルで動かす

[日本語](https://circleci.com/docs/ja/2.0/local-cli/)

## インストール

macOSの場合は下記コマンド

```bash
curl -fLSs https://raw.githubusercontent.com/CircleCI-Public/circleci-cli/master/install.sh | bash
# Starting installation.
# Installing CircleCI CLI v0.1.7411
# Installing to /usr/local/bin
# /usr/local/bin/circleci

circleci version 
# 0.1.7411+93b9d40 (release)
```

### update

```bash
circleci update
circleci switch
```

### 設定
[事前にAPI TOKENを作って](https://circleci.com/account/api)から、下記コマンドで自分のアカウントを設定する

```bash
circleci setup
```

### Configファイルのバリデーション

```bash
circleci config validate
```

## 自分のマシンでコンテナを動かす

https://circleci.com/docs/2.0/local-cli/#run-a-job-in-a-container-on-your-machine
やっとここまできた

```
circleci local execute
```

これでうまくいけば、万事OK…なのだけど、うまくいかない

### 実行中にエラー

```sh
...
====>> Checkout code
#!/bin/bash -eo pipefail
mkdir -p /home/circleci/project && cd /tmp/_circleci_local_build_repo && git ls-files | tar -T - -c | tar -x -C /home/circleci/project && cp -a /tmp/_circleci_local_build_repo/.git /home/circleci/project
tar: circle.yml: Cannot stat: No such file or directory
tar: Exiting with failure status due to previous errors
Error:
Exited with code exit status 2

Step failed
Error: runner failed (exited with 101)
Task failed
Error: task failed
```
設定ファイルをコミットしたら解消した。その後はコミットしなくても反映されたので、謎だけど…

