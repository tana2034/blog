---
title: "[Go]GoでHello world"
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# [Go]GoでHello, world

## 環境

- macOS Catalina(10.15.2)
  
## インストール

[brew](https://formulae.brew.sh/formula/go)のstableが1.13.8（20/2/15現在）、これをいれる

```bash
brew install go
go version
# go version go1.13.8 darwin/amd64
```

## Hello, world

https://golang.org/doc/install#testing

これ通りにやってみる

```zsh
mkdir $HOME/go/src/hello
cd $_
touch hello.go
```

```go
// hello.go
package main

import "fmt"

func main() {
	fmt.Printf("hello, world\n")
}
```

```zsh
go build
ls
# hello    hello.go
./hello
# hello, world
```