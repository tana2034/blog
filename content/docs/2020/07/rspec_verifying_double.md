---
title: "[Rspec]Verifying doubles"
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
# draft:true
---

# [Rspec]Verifying doubles について学ぶ

Relishのドキュメントを読んで、Verifying doublesについて学びました。

https://relishapp.com/rspec/rspec-mocks/v/3-9/docs/verifying-doubles

## Verifying doublesとはなんなのか

> When using verifying doubles, RSpec will check that the methods
being stubbed are actually present on the underlying object if it is available. 

元となるオブジェクトがあるかを検証しているので、検証doublesらしい。
通常のdoublesは代替元となるオブジェクトの存在有無を確かめずにテストを通すが、verifying doublesの場合、指定したオブジェクトやクラスが存在しない場合はfailする。

## Verifying doublesの種類について

### instance double

https://relishapp.com/rspec/rspec-mocks/v/3-9/docs/verifying-doubles/using-an-instance-double

`instance_double()`で

```ruby
require 'unit_helper'

require 'user'

RSpec.describe User, '#suspend!' do
  it 'notifies the console' do
    # 引数にオブジェクトを渡しても良い
    notifier = instance_double("ConsoleNotifier")

    expect(notifier).to receive(:notify).with("suspended as")

    user = User.new(notifier)
    user.suspend!
  end
end
```

### class double

instance doubleと異なり、クラスメソッドの実装を保証する。

```ruby
require 'user'
require 'console_notifier'

RSpec.describe User, '#suspend!' do
  it 'notifies the console' do
    notifier = class_double("ConsoleNotifier").
      as_stubbed_const(:transfer_nested_constants => true)

    # notifyはCosoleNotifierのクラスメソッド
    expect(notifier).to receive(:notify).with("suspended as")
    expect(ConsoleNotifier::MAX_WIDTH).to eq(80)

    user = User.new
    user.suspend!
  end
end
```

### object double

> object_double can be used to create a double from an existing "template" object, from
which it verifies that any stubbed methods on the double also exist on the template. 

生成されたオブジェクトを引数として渡す

### Dynamic classes

### Partial doubles