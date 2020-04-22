---
title: "[Rubymine][VSCode]デバッガを使う"
date: 2020-03-20T09:43:18+09:00
---

# [Rubymine][VSCode]デバッガを使う

RubymineとVSCodeを比較中のため、どちらにもデバッガを導入しています

- 環境
    - MacOS Catalina 10.15.3

## Rubymine

https://pleiades.io/help/ruby/debugging-code.html
https://pleiades.io/help/ruby/starting-the-debugger-session.html
https://pleiades.io/help/ruby/running-applications.html

### 必要なgemのインストール

私の場合はGemfileには追加できないので直接追加します

```zsh
cd /path/to/project
gem install debase
gem install ruby-debug-ide
```

### 実行/デバッグ構成を作成
https://pleiades.io/help/ruby/run-debug-configuration.html

- 私の場合は環境変数を設定したいので、別でConfigurationを自分で作成
- RAILS_ENVの変更なので選択肢が`development` `production` `test`しか選べないのつらいと思ったけど普通に環境変数として追加すればOKだった

### 実行

https://pleiades.io/help/ruby/starting-the-debugger-session.html
ブレークポイントをセットして指定のConfigurationでDebug開始すると止まる！

## VSCode

### 必要なGemのインストール

これはRubymineと同じ

### Configurationを作成
https://code.visualstudio.com/docs/editor/debugging#_launch-configurations
この辺を参考にlaunch.jsonを作成する

- launch.json
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Debug fril_api",
            "type": "Ruby",
            "request": "launch",
            "cwd": "${workspaceRoot}",
            "program": "${workspaceRoot}/bin/rails",
            "showDebuggerOutput": true,
            // useBundlerオプションはGemfileにdebase, ruby-debug-ideを追加している人だけで、gem installで入れたような場合には不要だった
            // "useBundler": true,
            "pathToRDebugIDE": "/Users/USERNAME/.rbenv/versions/2.3.1/bin/rdebug-ide",
            "args": ["server"]
        }
    ]
}
```

### 実行

うごいた

## 感想など

- 導入
    - 導入自体はRubymineのほうが設定値も少なくて楽と思う
    - とはいえVSCodeもネット上に文献が多いのでそんなに苦労しなかった
- 使い心地
    - 個人的には、Rubymineのほうが変数の一覧はみやすいかなと思った
    - ただ、RubymineでDebuggerを立ち上げるとMacbookが唸りだすので、VSCodeのほうが軽いかなという気はした
