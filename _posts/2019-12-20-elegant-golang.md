---
layout: single
title: "Writing elegant Golang"
author: "Danni"
toc: true
toc_sticky: true
lang: en
tags:
  - Go
---

Full disclosure, I did't start using Golang actively till recent months, even though I have always claimed to know it and put it in the language section on my resume(naively & shamelessly). But there is definietly a huge difference between knowing some common syntaxes and understanding the language in engineering level completely.

Last week, while I was having a conversation with one of my friends who started using Golang because of his PhD thesis, I realized we both shared same learning experience(Though definitely not the most efficient learning curve):

1. started a huge list of online tutorials;
2. proceeded to spend money on books;
3. gave up the first two & just started development;
4. found bugs couldn't understand, solved with online forumn, and went back to learning materials;

One thing we both 100% agreed on: **practice, practice, practice**. More precisely, practicing by building something yourself. I have always believed the best way to learn programming language is a quick project that adopts most common features and has a progressive learning curve.

### personal notes on writing concise & elegant Golang

1. `gofmt`, `goimports`, `golangci-lint`, etc.
2. Standard Go Project Layout:
    - do NOT contain `/src`: especially for those Java developers who is used to its design pattern;
    - `/internal` modules cannot be used by external parties;
3. do NOT use `init` for initialization, like in `rpc`, `DB`, or `Redis`, because init will be anonymously executed, initializing the resource connection. Every time we declare an `init()` function, Go will load and tun it **prior** to anything else in that package;

At the `init` stage. Best make some simple conditional/judging statements, like using `flag: True/False` to determine the status of the parameters;

```golang
// main.go
package main

import (
    "fmt"
)

var name string

func init() {
    name = "anonymous"
}

func main() {
    fmt.Printf("My name is %s", name)
}
```
Instead, it is better to use **Client + NewClient** for initializing connection.



4. Testing. Use frameworks like (GoMock)[https://blog.codecentric.de/en/2017/08/gomock-tutorial/], `httpMonky`, `monkey` and (GoMock)[https://github.com/golang/mock] for testing;

5. Optimization:
    - Instead of `fmt.Sprintf`, use `strconv`;
    - Use `sync.Pool`to re-use previously allocated objects and re-duce the work of the garbage collector;
    - avoid using structures containing pointers as key for large maps
        - For example, if you have a structure: `map[string]int`, the garbage collection has to check every string since it contains pointers;

### Reference

https://stephen.sh/posts/quick-go-performance-improvements