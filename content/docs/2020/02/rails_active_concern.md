---
title: "[rails]ActiveSupport::Concern"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# [rails]ActiveSupport::Concern

## Moduleの問題点

[module](/docs/2020/02/ruby_module/)をmix-inするときに、`include`, `extend`が使えることを学んだけれど、
別に全部が全部クラスメソッドにしたいわけじゃない、もしくはmodule自体をどっちで読み込めば良いのか悩むことがあると思う

そういう時は下記のように書く

```Ruby
# Module自体はincludeで読み込む
module M
  # ここでクラスメソッドに指定したいものをextendsで読み込む
  # includedはmoduleがincludeされた時に呼び出されるRubyのメソッド
  def self.included(base)
    base.extend ClassMethods
    base.class_eval do
      scope :disabled, -> { where(disabled: true) }
    end
  end

  module ClassMethods
    ...
  end
end
```
[Module#included](https://docs.ruby-lang.org/ja/latest/method/Module/i/included.html)'

## ActiveSupport::Concernを使って簡略化

上記のような例を、ActiveSupport::Concernを使うことで簡略化できます。
簡略化ポイントとしては２点で、①extendする部分をわざわざ書かなくて良い、②読み込み先のクラスが依存関係を書かなくて良い、です。

### extendをわざわざ書かなくて良い

上記の例は、以下のように書けるようです

```Ruby
require 'active_support/concern'

module M
  # Concernをextendする
  extend ActiveSupport::Concern

  # includedのブロックとして渡すと、includeされた時に定義される
  included do
    scope :disabled, -> { where(disabled: true) }
  end

  # Concernのclass_methodsにブロックを渡して評価する、すなわちクラスメソッドして扱えるようになる
  class_methods do
    ...
  end
end
```

includedやclass_methodにブロックとしてメソッドや変数を渡すことによって、自分でクラスメソッドのモジュールを作成して読み込むみたいな面倒なことをしなくて良い

### 読み込み先のクラスが依存関係を書かなくて良い

module Fooに依存しているmodule Barをclass Hostにmix-inしたい時、Hostは二つのmoduleをどちらもincludeしないといけない、面倒ですね

```Ruby
module Foo
  def self.included(base)
    base.class_eval do
      def self.method_injected_by_foo
        ...
      end
    end
  end
end

module Bar
  def self.included(base)
    base.method_injected_by_foo
  end
end

class Host
  include Foo # We need to include this dependency for Bar
  include Bar # Bar is the module that Host really needs
end
```

moduleにConcernを入れておくと、classがわざわざ依存関係を知らなくて済む、便利ですね

```Ruby
require 'active_support/concern'

module Foo
  extend ActiveSupport::Concern
  included do
    def self.method_injected_by_foo
      ...
    end
  end
end

module Bar
  extend ActiveSupport::Concern
  include Foo

  included do
    self.method_injected_by_foo
  end
end

class Host
  include Bar # It works, now Bar takes care of its dependencies
end
```

## 参考

- [ActiveSupport::Concern](https://api.rubyonrails.org/classes/ActiveSupport/Concern.html)
- [github](https://github.com/rails/rails/blob/master/activesupport/lib/active_support/concern.rb)