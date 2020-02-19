---
title: "[Go]Go言語を学ぶ（初級編）"
weight: 1
# bookFlatSection: false
# bookToc: 6
# bookHidden: false
---

# [Go]Go言語を学ぶ（初級編）

業務でGoを使うことになったので、Go言語を学んでみました

## どうやって学ぶかの展望

とにかくまだ一回も触ったことがないので、どうやって学ぶか考えてみました

- [プログラミング経験者がGo言語を本格的に勉強する前に読むための本](https://www.amazon.co.jp/%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0%E7%B5%8C%E9%A8%93%E8%80%85%E3%81%8CGo%E8%A8%80%E8%AA%9E%E3%82%92%E6%9C%AC%E6%A0%BC%E7%9A%84%E3%81%AB%E5%8B%89%E5%BC%B7%E3%81%99%E3%82%8B%E5%89%8D%E3%81%AB%E8%AA%AD%E3%82%80%E3%81%9F%E3%82%81%E3%81%AE%E6%9C%AC-%E5%A4%A9%E7%94%B0%E5%A3%AB%E9%83%8E-ebook/dp/B06XJ86BFZ)を読む
- [A Tour of Go](https://tour.golang.org/welcome/1)で実際に動かしてみる
- 自分の環境にGoをインストールし、簡易的なアプリケーションを作成する

## プログラミング経験者がGo言語を本格的に勉強する前に読むための本

基本的な文法を学びました

- 30分位でサクっと読める
- 他の言語とこんなところが同じ、と付記してくれるので他言語学習者に伝わりやすい
- でもこれだけでGoを使おうとするのは結構無謀な感じがあるので、本当に入り口という気がする

## A Tour of Go

A Tour of GoはオンラインのGoの実行環境([Go Playground](https://play.golang.org/))で実際にGoを動かしながら、Goの基本的な文法を学ぶことができます

以下はやった時の自分用メモ

#### Packages, variables, and functions
- Packages
- Imports
- Exported names
- Functions
```go
// 変数名の後ろに型を書く！
func add(x int, y int) int {
	return x + y
}
```
- Functions continue
- Multiple results
  
複数の戻り値を返すことができる（返す値の型の指定は以下の通り）

```go
func swap(x, y string) (string, string) {
	return y, x
}
```
- Named Return Value

戻り値に名前をつけられる

```go
func split(sum int) (x, y int) {
	x = sum * 4 / 9
	y = sum - x
	return
}
```

- Variables
- Variables with initializers
- Short variables declarations
`var`を使う代わりに`:=`で暗黙的な型宣言ができる
```go
var c, python, java = true, false, "no!"
```
```go
c, python, java := true, false, "no!"
```
- Basic types
- Zero values
- Type conversions
- Type interface
- Constants
- Numeric Constants

#### Flow Control statements: for, if, else, switch and defer

- For
- For continued
- For is Go's "while"
- Forever
- If with a short statement
- If and else
- [Exercise: Loops and Functions](https://go-tour-jp.appspot.com/flowcontrol/8)
  - あとで考える
- Switch
- Switch evaluation order
- Switch with no condition
- Defer
  
関数の終わりまで処理を遅延させる
```go
// helloが出力された後にworldが出力される
func main() {
	defer fmt.Println("world")

	fmt.Println("hello")
}
```
- Stacking defers

いくつもあったら…
```go
func main() {
    defer fmt.Println(1)
    defer fmt.Println(2)
    defer fmt.Println(3)
    defer fmt.Println(4)
    defer fmt.Println(5)
    return
}
// 5, 4, 3, 2, 1の順に出力される
```

#### More types: structs, slices, and maps.

- Pointers

ポインタは、値のメモリアドレスを指す

```go
    i := 42
	fmt.Println(i) // 42
	fmt.Println(&i) // 0x40e020
	fmt.Println(*&i) // 42
```

- Structs
- Struct FieldsFields

```go
type Vertex struct {
    X int
    Y int
    x int
}

v := Vertex{1, 2}
v.x = 4
```
- Pointers to structs
- Struct Literals
- Arrays

```go
var a [10]string
a[0] = "aaa" 
```
- Slices
arrayとの違いが当初わからなかったけど、サイズを指定しなかったらそれはSliceになるみたい。
```go
// これは配列
a := [3]int{1, 2, 3}
// これはスライス
b := []int{1, 2, 3}
// これもスライスになる
c := a[1:2]
```
- Slices are like references to arrays 
- Slice literals
- Slice defaults
- Slice length and capacity
- Nil slices
- Creating a slice with make
- Slices of slices
- Appending to a slice
- Range
スライスやマップを一つずつ処理する時に使用する
```go
var foo = []string{"a", "b", "c"}
for s, t := range foo {
    fmt.Printf("%d ", s)
    fmt.Printf("%s ", t)
}
// 0 a 1 b 2 c
```
- Range Continued
`_`でインデックスもしくは値の不要な値を捨てることができる（宣言しておいて使わないと`xxx declared and not used`とエラーになる）

- Exercise Slice
https://qiita.com/rock619/items/f412d1f870a022c142d0#exercise-slices
ためになる
- Maps
`make`関数は指定された型で初期化
```go
var d map[string]string
d = make(map[string]string)
d["a"] = "aaa"
d["b"] = "bbb"
fmt.Println(d["b"])
```
- Map literals
- Map literals continued
- Mutating Maps
```go
m := make(map[string]int)
m["Answer"] = 48
delete(m, "Answer")
v, ok := m["Answer"]
```
- Exercise: Maps
- Function values
関数も変数として扱える
- Function closures
関数はクロージャ
- Exercise: Fibonacci closure

#### Methods and interfaces

- Methods
- Methods are function
Goにはクラスがないけど、メソッドに型定義できる

```go
type Text struct {
	description string
}

// Textはレシーバになる
func (t Text) Good() bool {
	return t.description == "Good"
}

func main() {
	g := Text{"Good"}
    fmt.Println(g.Good())
    // true
	b := Text{"Bad"}
    fmt.Println(b.Good())
    // false
}
```

```go
// レシーバにならないパターン
// Textはレシーバになる
func Good(t Text)  bool {
	return t.description == "Good"
}

func main() {
	g := Text{"Good"}
    fmt.Println(Good(g))
    // true
	b := Text{"Bad"}
    fmt.Println(Good(b))
    // false
    fmt.Println(g.Good())
    // g.Good undefined (type Text has no field or method Good)
    // レシーバとして扱うとエラー
}

```
- Methods continue
structだけでなく、独自のtypeにも対応
- Pointer receiver
```go
// ポインタを渡すことで元の変数のコピーを操作する
func (v *Vertex) Scale(f float64) {
	v.X = v.X * f
	v.Y = v.Y * f
}
```
- Pointers and function
ポインタを関数に渡すような場合でも↑と同じようになる
- Methods and pointer indirection
- Methods and pointer indirection (2)
- Choosing a value or pointer receiver
ポインタレシーバを使う理由。
- Interfaces
`Interface(インタフェース)型は、メソッドのシグニチャの集まりで定義`
```go
type Abser interface {
    // メソッド
	Abs() float64
}
```
- Interfaces are implemented implicitly
```go
type I interface {
	M()
}

type T struct {
	S string
}

// これでTがIを実装していることになるらしい。。
func (t T) M() {
	fmt.Println(t.S)
}

func main() {
	var i I = T{"hello"}
	i.M()
}
```
- Interface values
- Interface values with nil underlying values
レシーバーがnilだと呼び出された値
- Nil interface values
実装がないインターフェースを呼び出すとランタイムエラーになる
```go
type I interface {
	M()
}

var i I
i.M()
```
- The empty interface
空のインターフェース、未知の型を表す時に使うらしい
```go
var i interface{}
i = 42
i = "a"
// なんでも良いみたい
```
- Type assertions
```go
var i interface{} = "hello"
// iの値が型Tであることを主張する
t := i.(T)
t, ok := i.(string)
// これはokはtrueになる、stringじゃない型を指定するとそのかたの値を持っていないのでfalse
```
- Type switch
型によって挙動を変えるためのswitch構文
```go
switch v := i.(type) {
case T:
    // here v has type T
case S:
    // here v has type S
default:
    // no match; here v has the same type as i
}
```
- Stringers
よく使われるインターフェース
```go
type Person struct {
	Name string
	Age  int
}

// PersonをレシーバにしたStringの関数を作成しておくと、Printlnする時にこれが出力される
func (p Person) String() string {
	return fmt.Sprintf("%v (%v years)", p.Name, p.Age)
}

func main() {
	a := Person{"Arthur Dent", 42}
	z := Person{"Zaphod Beeblebrox", 9001}
	fmt.Println(a, z)
}
```
- Exercise Stringers
もっと良い書き方ありそう
```go
func (ipaddr IPAddr) String() string {
	return fmt.Sprintf("%d.%d.%d.%d", ipaddr[0], ipaddr[1], ipaddr[2], ipaddr[3])
}
```
- Errors 
error型は組み込みのインターフェース
- Exercise: Errors
あとでやりたい
- Readers
この辺またあとでやる
- Exercise: Readers
- Images
- Exercise: Images

#### Concurrency

- Goroutines
でた！ゴルーチン
goroutine (ゴルーチン)は、Goのランタイムに管理される軽量なスレッド
```go
func up(start int) {
    var count int
	for i := 0; i < 3; i++ {
		time.Sleep(100 * time.Millisecond)
		count = start + i
		fmt.Println("i: ", i)
		fmt.Println("start: ", start)
		fmt.Println("count: ", count)
	}
}

func main() {
	go up(1)
	up(100)
}
// i:  0
// start:  1
// count:  1
// i:  0
// start:  100
// count:  100
// i:  1
// start:  100
// count:  101
// i:  1
// start:  1
// count:  2
// i:  2
// start:  1
// count:  3
// i:  2
// start:  100
// count:  102
```
ちなみにgoroutineだけにしておくと勝手に終わってしまう
- Channels
チャネル型は値の送受信が可能
```go
func plusone(val int, c chan int) {
    val = val+1
	c <- val
}

func main() {
	c := make(chan int)
	go plusone(1, c)
	go plusone(100, c)
	x, y := <-c, <-c
	fmt.Println(x, y)
}
```

`通常、片方が準備できるまで送受信はブロックされます。これにより、明確なロックや条件変数がなくても、goroutineの同期を可能にします。 `
これで同期するのか

- Buffered Channel
`チャネルは、 バッファ ( buffer )として使えます。 `
バッファとして使えるがそもそもわからないな…
[wikipedia バッファ](https://ja.wikipedia.org/wiki/%E3%83%90%E3%83%83%E3%83%95%E3%82%A1)
うーん、 `時間的差違を補正する遅延や一時記憶など整合を図り矛盾を解決する機能や手段`として使えるということかな。

```go
// 2を1に変えるとエラーになる
ch := make(chan int, 2)
ch <- 1
ch <- 2
fmt.Println(<-ch)
fmt.Println(<-ch)
```
- Range Close
チャネルはクローズできる
```go
close(c)
```
通常、チャネルはクローズしなくて良い
チャネルをクローズするのは受け手じゃなく送り手側
（受け手がcloseして送信するとpanicになる）
受け手は成功したかを受け取れるのでそこで判別する
```go
v, ok := <-ch
```
- Select
selectは複数の通信操作で待たせる
```go
func fibonacci(c, quit chan int) {
	x, y := 0, 1
	for {
		select {
		case c <- x:
			x, y = y, x+y
			fmt.Println("x:", x)
		case <-quit:
			fmt.Println("quit")
			return
		}
	}
}

func main() {
	c := make(chan int)
	quit := make(chan int)
	go func() {
		for i := 0; i < 10; i++ {
			fmt.Println(<-c)
		}
		quit <- 0
	}()
	fibonacci(c, quit)
}
// 0
// 1
// 1
// 2
// 3
// 5
// 8
// 13
// 21
// 34
// quit
```
goroutineとして呼び出された関数は、処理全体が終わるまで動く
チャネルの数を受け取ってfibonacciの中のselectは待機する、値を取得したら動作

- Default Selection
- Exercise: Equivalent Binary Trees
- Exercise: Equivalent Binary Trees
- sync.Mutex

通信が必要なく、一度に１つのgoroutineのみを動かしたい、コンフリクトを避けたい場合、sync.Mutexの`Lock`と`Unlock`を使う

```go

// Inc increments the counter for the given key.
func (c *SafeCounter) Inc(key string) {
	c.mux.Lock()
	// Lock so only one goroutine at a time can access the map c.v.
	c.v[key]++
	c.mux.Unlock()
}
```
- Exercise: Web Crawler

## 自分の環境にGoをインストールし、簡易的なアプリケーションを作成する
これは別途やります

## 言語習得における今回の知見

- A Tour of Goよかったです、読むだけじゃなくて実行してみるのが良い
- メモに残しながらやることで、色々疑問点が表出してきて、試せたのがよかった
- とはいえ長いしまだ理解できてないところもある（忘れてるところも）ので、都度復習したり追記したい

## 参考資料

- [Documentation](https://golang.org/doc/)
- [Youtube Writing, building, installing, and testing Go code](https://www.youtube.com/watch?v=XCsL89YtqCs)
- [pkgについての Documentation](https://golang.org/pkg/)
- [Go言語の仕様](https://golang.org/ref/spec)
- [動画　Goの並行処理](https://www.youtube.com/watch?v=f6kdp27TYZs)
- [Simple Programming Environment](https://vimeo.com/53221558)