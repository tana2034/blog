---
title: "[RSpec]メモ"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# [RSpec]メモ

参考リンク）
https://magazine.rubyist.net/articles/0035/0035-RSpecInPractice.html

## RSpecとは

Rubyのテスティングフレームワーク。minitestと比べて何がいいのかや、使い方について悩むところがあったのでメモ。

### describeとcontext

[参考リンク](https://magazine.rubyist.net/articles/0035/0035-RSpecInPractice.html#describe-%E3%81%A8-context)

テストコードに構造を作るために, itの上位階層としてdescribeとcontextを使用する

```ruby
describe 'テストする対象（メソッド名など）' do
    context 'テストするときの状況(〜の場合）' do
        it '求める結果' do
            expect(subject).to be true
        end
    end
end
```

### subject

shouldレシーバが省略できるというメリットの他に、subjectを利用することで、何をテストしているか？が明確になる

### letで可変部分を共通化

### shared_contextの利用

複数のテストケースで共通のコンテキストを利用したい場合、`shared_context`を利用するとDRYにかける。
shared_contextを利用する箇所に`include_context`を含めるとOK

### コードの実行回数を数える

ちゃんとメソッドが利用されているか？それは期待する数か？（メモ化されているか？）
などを確認するために、メソッドの実行回数をテストできる

https://techracho.bpsinc.jp/hachi8833/2018_04_02/54350

```ruby
# class_instanceのmethod_nameが一回以上呼ばれていることをテスト
expect(class_instance).to have_received(:method_name).at_least(:once)
```

個人的な感覚だと先にclass_instanceをmock化するパターンが多いように思う

## 終わり

RSpecの書き方を忘れているので復習中