# Buffered Channels

Go允许指定 `channel` 的缓冲大小, 就是指明 `channel` 可以存储多少元素: `ch := make(chan string, 3)`, 创建了存储3个元素的 `string` 型 `channel`, 在这个 `channel` 中, 前3个元素可以无阻塞的写入, 当写入第4个元素时, 代码将会阻塞, 直到其他 `goroutine` 从 `channel` 中读取一些元素, 腾出元素空间
``` Go
ch := make(chan type, value)
```

当 `value = 0` 时, `channel` 是无缓冲阻塞读写的，当 `value > 0` 时, `channel` 有缓冲、是非阻塞的, 直到写满 `value` 个元素才阻塞写入

``` Go
package main

import "fmt"

func main() {
  c := make(chan int, 2)//修改2为1就报错，修改2为3可以正常运行
  c <- 1
  c <- 2
  fmt.Println(<-c)
  fmt.Println(<-c)
}
//修改为1报如下的错误:
//fatal error: all goroutines are asleep - deadlock!
```