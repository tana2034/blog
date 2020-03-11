---
title: "[Rspec]Mocks Basics"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# [Rspec]Mocks Basics

RSpecのmockについて学びました

https://relishapp.com/rspec/rspec-mocks/v/3-9/docs/basics/

## Test Doubles

Doubleオブジェクトは、本当のオブジェクトの代替として使用できる。
ただし、このままだとメソッドを呼ぶことはできない。

```Ruby
RSpec.describe "A test double" do
  it "raises errors when messages not allowed or expected are received" do
    dbl = double("Some Collaborator")
    # これはエラーになる
    dbl.foo
  end
end
```

## Allowing messages

`double()`でモックオブジェクトを作っただけだと、メソッドを代替して呼べたりはしないので、メッセージングがしたい場合は、`allow().to receive`を使う必要がある。

```Ruby
RSpec.describe "allow" do
  it "returns nil from allowed messages" do
    dbl = double("Some Collaborator")
    # dblのオブジェクトがfooメソッドを呼べるようになる
    allow(dbl).to receive(:foo)
    expect(dbl.foo).to be_nil
  end
end
```

## Expecting messages

メッセージを受け取れる（≒メソッドを呼べる）ようになったdoubleオブジェクトに対して、実際に呼ばれたかをテストすることができる。

```Ruby

RSpec.describe "A fulfilled positive message expectation" do
  it "passes" do
    dbl = double("Some Collaborator")
    expect(dbl).to receive(:foo)
    dbl.foo
  end
end
```

## Spies

`double()`の代わりに、`spy()`を使うことで、全てのメソッドの使用を追うことができる。これは、`allow`が不要。

```Ruby
RSpec.describe "have_received" do
  it "passes when the message has been received" do
    invitation = spy('invitation')
    invitation.deliver
    expect(invitation).to have_received(:deliver)
  end
end
```
また、通常のオブジェクトも、`allow`を使うことでメソッドを使ったかを追跡できる。

```Ruby
class Invitation
  def self.deliver; end
end

RSpec.describe "have_received" do
  it "passes when the expectation is met" do
    allow(Invitation).to receive(:deliver)
    Invitation.deliver
    expect(Invitation).to have_received(:deliver)
  end
end
```