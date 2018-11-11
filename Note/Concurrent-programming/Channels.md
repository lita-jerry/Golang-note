# Channels

Go提供了一个很好的通信机制 `channel`, 可以与Unix shell中的双向管道做类比: 可以通过它发送或接收值

值只能是特定的类型: `channel` 类型, 定义一个 `channel` 时, 也需要定义发送到 `channel` 的值的类型, 使用 `make` 创建channel:
``` Go
ci := make(chan int)
cs := make(chan string)
cf := make(chan interface{})
```

`channel` 通过操作符 `<-` 才发送和接收数据
``` Go
cs <- v  //将v发送到channel cs
v  <- cs //接收channel cs的数据
```

展示一个实际例子:
``` Go
package main

import "fmt"

func sum(a []int, c chan int) {
  total := 0
  for _, v := range a {
    total += v
  }
  c <- total  // send total to c
}

func main() {
  a := []int{7, 2, 8, -9, 4, 0}

  c := make(chan int)
  go sum(a[:len(a)/2], c)
  go sum(a[len(a)/2:], c)
  x, y := <-c, <-c  // receive from c

  fmt.Println(x, y, x + y)
}
```
> 默认情况下, `channel` 的的发送和接收数据都是阻塞的, 除非另一端已经准备好, 这样就使得 `goroutines` 同步变的更加的简单, 而不需要显式的 `lock`
