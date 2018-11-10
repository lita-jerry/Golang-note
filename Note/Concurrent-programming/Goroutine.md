# Goroutine

`Goroutine` 其实就是协成, 但是比线程更小, 十几个 `goroutine ` 在底层体现就是五六个线程, `Go` 语言内部实现了这些 `Goroutine` 之间的内存共享

执行 `goroutine` 只需要极少的栈内存(大概4~5KB), 当然会根据相应的数据伸缩, 也正是因此, 可同时运行成千上万个并发任务, `goroutine ` 比 `thread` 更易用、更高效、更轻便

`goroutine` 是通过 `Go` 的 `runtime` 管理的一个线程管理器, `goroutine` 通过 `go` 关键字实现, 其实就是一个普通的函数:
``` Go
go hello(name)
```

来看一个具体的例子:
``` Go
package main

import (
  "fmt"
  "runtime"
)

func say(s string) {
  for i := 0; i < 5; i++ {
    runtime.Gosched()
    fmt.Println(s)
  }
}

func main() {
  go say("world") //开一个新的Goroutines执行
  say("hello")    //当前Goroutines执行
  fmt.Scanln()
  fmt.Println("quit")
}

```