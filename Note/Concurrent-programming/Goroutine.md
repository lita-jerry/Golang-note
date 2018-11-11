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
  go say("world")  //开一个新的Goroutines执行
  say("hello")  //当前Goroutines执行
  fmt.Scanln()  //防止未执行完毕就退出程序
  fmt.Println("quit")
}

//以上输出: hello与world的打印顺序可能因为运行环境不同而不同
// hello
// world
// hello
// world
// hello
// world
// hello
// world
// hello
// world

// quit
```
> runtime.Gosched()表示出让时间片, 让出当前 `goroutine` 执行权限, 调度器会安排其他等待的任务去运行, 并在下轮某个时间片再从该位置恢复执行
多个 `goroutine` 运行在同一个进程里面, 共享内存数据, 不过设计上我们要遵循 `不要通过共享来通信, 而是要通过通信来共享`
> 默认情况下, 在 `Go 1.5` 将标识并发系统个数的`runtime.GOMAXPROCS`的初始值由 `1` 改为了运行环境的CPU核心数, 如果在 `Go 1.5` 及其以前的环境使用线程, 需要显示的配置核心数量: `runtime.GOMAXPROCS(n)`
