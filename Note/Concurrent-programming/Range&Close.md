# Range和Close

我们使用 `Channel` 的 `Buffered` 时, 需要确定读取的次数, 很不方便, 所以也可以通过 `range` 操作 `slice` 或者 `map` 一样操作缓存类型的 `channel`

``` Go
package main

import (
  "fmt"
)

func printEvenNumber(n int, c chan int) {
  number, i := 0, 0
  for ; i < n; number++ {
    if (number % 2 == 0) {
      c <- number
      i++
    }
  }
  close(c)
}

func main() {
  c := make(chan int, 10)
  go printEvenNumber(100, c)
  for i := range c {
    fmt.Println(i)
  }
}
```
`for i := range c` 能够不断地读取 `channel` 传递的数据, 直到该 `channel` 被显式的关闭. 在消费方可以通过语法 `v, ok := <- ch` 中的 `ok` 查询 `channel` 是否被关闭, 如果 `ok` 为 `false`, 则表示 `channel` 已经没有任何数据并已经被关闭
> 应确保在生产者中关闭 `channel` 而不是在消费方, 否则容易引起 `pannic`
> `channel` 不像文件操作需要显式关闭, 当你确认没有任何发送的数据了, 或者想显式结束 `range` 循环, 或者想显式的结束 `channel`, 则可以调用 `close` 关闭