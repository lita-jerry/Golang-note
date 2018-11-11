# Runtime Goroutine

在执行一些高并发的计算任务时, 为了尽量利用高性能服务器多核心的特性, 将计算任务并行化, 从而达到降低总计算时间的目的

## Gosched

出让时间片函数, 在设计并发任务时, 用户可以在每个 `goroutine` 中控制何时主动让出时间片给其他 `goroutine`, 这可以使用 `Gosched()` 函数实现:
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

//以上输出:
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
理论上讲应该如上所示的输出, 但是也会根据运行环境的不同, 输出 `hello` 与 `world` 的顺序可能也会不同

## NumCPU

有时为了将多个并发执行的 `goroutine` 分配给不同的CPU核心去完成, 用户就需要知道CPU核心的具体数目. 为此, `runtime` 包提供了 `NumCPU()` 函数可以完成这个任务

为了观察系统任务调度情况, 还可以使用 `NumGoroutine()` 函数返回正在执行和排队的任务总数

## Goexit

终止当前 `goroutine`, 如果要强行终止一个 `goroutine` 的执行, 可以调用 `Goexit()` 函数来完成, `Goexit()` 将终止整个堆栈链, 并在内层退出, 但是 `defer` 语句仍然或被执行
