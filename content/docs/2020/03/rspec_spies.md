---
title: "[Rspec]Spyについて"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# [Rspec]Spyについて
RspecのSpyという機能について調査しました

## Spyとは何か
[公式ドキュメント](https://relishapp.com/rspec/rspec-mocks/v/3-9/docs/basics/spies)

    You can use any test double (or partial double) as a spy, but the double must be setup to spy on the messages you care about. Spies automatically spy on all messages, or you can allow a message to spy on it.

説明文の`message`というのが意味が取りづらい…けど、[オブジェクト指向でいうところのmessage](https://ja.wikipedia.org/wiki/%E3%82%AA%E3%83%96%E3%82%B8%E3%82%A7%E3%82%AF%E3%83%88%E6%8C%87%E5%90%91%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0#%E3%83%A1%E3%83%83%E3%82%BB%E3%83%BC%E3%82%B8)なのかな。

`double`オブジェクトの呼び出されたメソッドを全て監視することもできるし、特定のメソッドを監視することができる機能がSpy機能として捉えてます。

### オブジェクトの挙動の監視
https://relishapp.com/rspec/rspec-mocks/v/3-9/docs/basics/spies#using-a-spy

spyのオブジェクトが実行したどんなメソッドも追えていて、have_receivedで実行したことを確認できる。

```Ruby
RSpec.describe "have_received" do
  it "passes when the message has been received" do
    spy_object = spy('spy_object')
    spy_object.do_something
    spy_object.do_something_else
    expect(spy_object).to have_received(:do_something)
    expect(spy_object).to have_received(:do_something_else)
  end
end
```

### 特定のメソッドの実行を追う
https://relishapp.com/rspec/rspec-mocks/v/3-9/docs/basics/spies#spy-on-a-method-on-a-partial-double

```Ruby
RSpec.describe User, type: :model do
  subject { User.new }
  it 'do something' do
    # 呼ばれることを確認したいメソッドを指定する
    allow(subject).to receive(:update_attribute)
    subject.update_attribute(:email, 'example+1@example.com')
    expect(subject).to have_received(:update_attribute)
  end
end
```

#### 呼ばれた回数をチェックする
```Ruby

RSpec.describe User, type: :model do
  subject { User.new }
  it 'do something' do
    allow(subject).to receive(:update_attribute)
    subject.update_attribute(:email, 'example+1@example.com')
    # once
    expect(subject).to have_received(:update_attribute).once
  end
end
```

## 参考リンク
[Rails tips: RSpecの「スパイ（spy）」の解説（翻訳）](https://techracho.bpsinc.jp/hachi8833/2018_03_12/53518)

