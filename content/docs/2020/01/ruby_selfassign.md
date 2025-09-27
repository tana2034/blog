---
title: "[Ruby]自己代入"
# bookFlatSection: false
# bookToc: 6
# bookHidden: false
---

# [Ruby]自己代入

## 自己代入でよくみるのは…

```Ruby
x += 1
y ||= 1
```

こんな例をよくみると思う

## いろんな自己代入

https://docs.ruby-lang.org/ja/latest/doc/spec=2foperator.html#selfassign

|自己代入の例|展開|
|:--|:--|
|x += y|x = x + y|
|x -= y|x = x - y|
|x *= y|x = x * y|
|x /= y|x = x / y|
|x %= y|x = x % y|
|x **= y|x = x ** y|
|x &&= y|x = x && y|
|x \|\|= y|x = x \|\| y|
|x &= y|x = x & y|
|x \|= y|x = x \| y|
|x ^= y|x = x ^ y|
|x <<= y|x = x << y|
|x >>= y|x = x >> y|

### x += y

最もよく使われそう

```Ruby
x = 3
x += 1
# => 4
```

こういうパターンもあるかも

```Ruby
x = [1, 2]
x += [3, 4]
# => [1, 2, 3, 4]
# 配列のマージもできる
```

### x ||= y

xがnil,falseだった場合にyをxに代入する、初期化でよく使われる

```Ruby
# resultsがnil、falseの場合、空の配列を代入する
results ||= []
```

### x **= y

`**`は指数計算に使われる

```Ruby
x = 3
x **= 2
# => 9
# 3を２乗した値である9が入る
```

### x &&= y

`&&`は`||`同様論理演算子なので、なんとなくわかる、ようで、結果がどうなるかピンとこない

```Ruby
x, y = nil, 3
x &&= y
# xはnilになる
y &&= x
# yはnilになる
a, b = 2, 5
a &&= b
# aは5になる
```

`x &&= y` は `x && y`がtrueならyの値が入って、falseならnilが入るようだ

### x &= y

ん…？`&`ってなんだっけ…

[Arrayの積演算](https://docs.ruby-lang.org/ja/latest/class/Array.html#I_--26)

配列に使えば良さそう

```Ruby
x = [1, 3, 4]
y = [2, 3, 4, 7]
x &= y
# => [3, 4]
```

### x |= y

`&`が分かっていればわかりやすそう

```Ruby
x = [1, 3, 4]
y = [2, 3, 4, 7]
x |= y
# => [1, 3, 4, 2, 7]
```

### その他

ビット演算とかシフトとかはそもそもがよく分かっていないので別でやります