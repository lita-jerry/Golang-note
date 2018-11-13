# Select

如果存在多个 `channel`, Go提供了一个关键字 `select` 用于监听 `channel` 上的数据流动

`select` 默认是阻塞的, 只有当监听的 `channel` 中有发送或接收的时候才会运行

> 当多个 `channel` 都准备好的时候, `select` 是随机选择一个执行的

``` Go
package main

import (
  "fmt"
)

func printEvenNumber(c chan int, quit chan int) {
  for i := 0;; i++ {
    if (i % 2 == 0) {
      select {
      case c <- i:
      case <- quit:
        return
      default:
        fmt.Println("当c阻塞的时候执行这里")
      }
    }
  }
}

func main() {
  c := make(chan int, 10)
  quit := make(chan int, 1)

  go func() {
    for i := 0; i < 5; i++ {
      fmt.Println(<- c)
    }
    quit <- 0
  }()

  printEvenNumber(c, quit)
}

//程序输出:
// 当c阻塞的时候执行这里
// 当c阻塞的时候执行这里
// 当c阻塞的时候执行这里
// 当c阻塞的时候执行这里
// 当c阻塞的时候执行这里
// 当c阻塞的时候执行这里
// 当c阻塞的时候执行这里
// 当c阻塞的时候执行这里
// 当c阻塞的时候执行这里
// 当c阻塞的时候执行这里
// 当c阻塞的时候执行这里
// 当c阻塞的时候执行这里
// 0
// 2
// 4
// 6
// 8
```

`select` 中还有 `default` 语法, 当所有的 `channel` 都没有准备好时, 默认执行 `default` 中的语句, 此时不在阻塞等待
