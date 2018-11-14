# 超时

有时 `goroutine` 会进入阻塞状态, 为了避免整个程序进入阻塞状态, 可以使用 `select` 来设置超时:

``` Go
package main

import (
  "fmt"
  "time"
)

func main() {
  c := make(chan int)
  o := make(chan bool)
  go func() {
    for {
      select {
        case v := <- c:
          fmt.Println(v)
        case <- time.After(5 * time.Second):
          fmt.Println("timeout")
          o <- true
          break
      }
    }
  }()
  <- o
}
```