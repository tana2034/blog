---
title: "VSCodeを利用してRubyのDebugを行う"
date: 2020-03-18T09:22:58+09:00
draft: true
---

# VSCodeを利用してRubyのDebugを行う

- Remote Development機能でデバッグ試してみる

## うまく行かなかった

- docker-compose.ymlの`command`を`bundle exec rdebug-ide --debug --host 0.0.0.0 --port 1234 -- rails s -p 3000 -b 0.0.0.0`では、Railsサーバは立ち上がっていないようだった

## 参考文献

- [debugging-Ruby-on-Rails](https://github.com/Microsoft/vscode-recipes/tree/master/debugging-Ruby-on-Rails)　最も参考になるけどうまくいかなっかった
- [ruby debugging in vscode](https://dev.to/dnamsons/ruby-debugging-in-vscode-3bkj) 参考になる
- [Debugging a Rails server in Visual Studio Code](https://github.com/microsoft/vscode-recipes/tree/master/debugging-Ruby-on-Rails)
- [ruby-debug-ide](https://github.com/ruby-debug/ruby-debug-ide)
- [Debugging in a container](https://code.visualstudio.com/docs/remote/containers#_debugging-in-a-container)