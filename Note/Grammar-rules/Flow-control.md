# Go流程控制

Go在流程控制方面的特点如下:
- 没有 `do` 和 `while` 循环, 只有一个更广义的 `for` 语句
- `switch` 语句灵活多变, 还可以用于类型判断
- `if` 语句和 `switch` 语句都可以包含一条初始化子语句
- `break` 语句和 `continue` 语句可以后跟一条标签(`label`)语句, 以标识需要终止或继续的代码块
- `defer` 语句可以使用我们更加方便地执行异常捕获和资源回收任务
- `select` 语句也可用于多分支选择, 但只与通道配合使用
- `go` 语句用于异步启用 `goroutine` 并执行指定函数

## 代码块和作用域

代码块就是一个由花括号包裹的表达式和语句的序列, 代码块中也可以不包含任何内容, 即: 空代码块`{}`

除了显式的代码块之外, 还有一些隐式的代码块:
- 所有Go代码形成了一个最大的代码块, 即: 全域代码块
- 每一个代码包中的代码共同组成了一个代码块, 即: 代码包代码块
- 每一个源码文件都是一个代码块, 即: 源码文件代码块
- 每一个 `if`、 `for`、 `switch` 和 `select` 语句都是一个代码块
- 每一个在 `switch` 或 `select` 语句中的 `case` 分支都是一个代码块

在Go中, 使用代码块表示词法上的作用域范围, 具体规则如下:
- 一个预定义标识符的作用域是全域代码块
- 表示一个常量、变量、类型或函数(不包括方法), 且声明在函数之外的标识符的作用域是当前的代码包代码块
- 被导入的代码包的名称的作用域是当前的源码文件代码块
- 表示方法接收者、方法参数或方法结果的标识符的作用域是当前的方法代码块
- 对于表示常量、变量、类型或函数的标识符, 如果被声明在函数内部, 那么作用域就是包含其声明的那个最内层的代码块

此外, 我们还可以重新声明已经在外层代码块中声明过的标识符, 当在内层代码块中使用这个标识符, 它表示的总是在该代码块中与它绑定在一起的那个程序实体, 可以说, 此时在外层代码块中声明的那个同名标识符被屏蔽了

## if 语句

`if` 语句会根据条件表达式来执行两个分支中的一个, 如果表达式的结果是 `true` 则执行 `if` 分支, 否则 `else` 分支会被执行
``` Go
var number int
// 没有else分支
if number < 100 {
  number++
}

// 有else分支
if number < 100 {
  number++
} else {
  number--
}

// 包含初始化子语句, 用于初始化局部变量
if diff := 100 - number; number < 100 {
  number++
} else {
  number--
}

// 支持串联
if diff := 100 - number; number < 100 {
  number++
} else if number < 200 {
  number--
} else {
  number -= 100
}
```

## switch 语句

`switch` 语句也提供了一种多分支执行的方法, 它会用一个表达式或类型说明符与每一个 `case` 进行比较, 并决定执行哪一个分支

### 表达式 switch 语句

在表达式 `switch` 语句中, `switch` 表达式和所有 `case` 携带的表达式(也称为 `case` 表达式)都会被求值, 并且执行顺序是自左向右, 自上而下, 只有第一个与 `switch` 表达式的求值结果相同的 `case` 表达式的分支会执行, 如果没有找到匹配的 `case` 表达式并且存在 `default case`, 那么 `default case` 的分支会执行
> `default case` 最多只能有一个, 另外 `switch` 表达式可以省略, 这时 `true` 会作为(并不存在的) `switch` 表达式的结果
``` Go
var content string = "Go"

switch content {
  default:
    fmt.Printf("default\n")
  case "Python":
    fmt.Printf("Python\n")
  case "Go": {
      fmt.Printf("Go")
      fmt.Printf("\n")
  }
}

// switch语句可以包含一条子语句来初始化局部变量
switch lang := strings.TrimSpace(content); lang {
  default:
    fmt.Printf("default\n")
  case "Python":
    fmt.Printf("Python\n")
  case "Go":
    fmt.Printf("Go\n")
}

// 使用fallthrough强制执行后面的case代码, fallthrough不会判断下一条case的结果(或default)是否为true
switch lang := strings.TrimSpace(content); lang {
  case "Python":
    fmt.Printf("Python\n")
    fallthrough
  case "Go":
    fmt.Printf("Go\n")
  case "Java", "C":
    fmt.Printf("Other\n")
  default:
    fmt.Printf("default\n")
}
```
> `break` 可以跳出当前 `switch` 语句

### 类型 switch 语句

类型 `switch` 语句将对类型进行判断, 而不是值
``` Go
var v interface{}
// do something
switch v.(type) {
  case string:
    fmt.Printf("string value = %s\n", v)
  case int, uint, int8, uint8, int16, uint16, int32, uint32, int64, uint64:
    fmt.Printf("integer value = %d\n", v)
  default:
    fmt.Printf("unsupported value, type = %T\n", v)
}
```
类型 `switch` 语句的 `switch` 表达式会包含一个特殊的类型断言, 例如 `v.(type)`, 它虽然特殊, 但是也要遵循类型断言的规则; 其次, 每个 `case` 表达式中包含的都是类型字面量而不是表达式; `fallthrough` 语句不允许出现在类型 `switch` 语句中

类型 `switch` 语句的 `switch` 表达式还有一种变形写法, 如下:
``` Go
switch i := v.(type) {
  case string:
    fmt.Printf("string value = %s\n", i)
  case int, uint, int8, uint8, int16, uint16, int32, uint32, int64, uint64:
    fmt.Printf("integer value = %d\n", i)
  default:
    fmt.Printf("unsupported value, type = %T\n", i)
}
```
这里的 `i := v.(type)` 使经类型转换后的值得以保存, `i` 的类型一定会是 `v` 的值的实际类型

## for 语句

`for` 语句用于根据给定的条件重复执行一个代码块, 这个条件或由 `for` 子句直接给出, 或从 `range` 子句中获得

### for 子句

一条 `for` 语句可以携带一条 `for` 子句, `for` 子句可以包含初始化子句、条件子句和后置子句
``` Go
package main

import "fmt"

func main() {

  // The most basic type, with a single condition.
  i := 1
  for i <= 3 {
    fmt.Println(i)
    i = i + 1
  }

  // A classic initial/condition/after `for` loop.
  for j := 7; j <= 9; j++ {
    fmt.Println(j)
  }

  // `for` without a condition will loop repeatedly
  // until you `break` out of the loop or `return` from
  // the enclosing function.
  for {
    fmt.Println("loop")
    break
  }

  // You can also `continue` to the next iteration of
  // the loop.
  for n := 0; n <= 5; n++ {
    if n%2 == 0 {
      continue
    }
    fmt.Println(n)
  }
}
```
在 `for` 子句的初始化子句和后置子句同时被省略, 或者其中的部分代码都省略的情况下, 分隔符 `;` 可以省略:
``` Go
var m = 1
for m < 50 { 省略初始化子句和后置子句
  m *= 3
}
```

### range 子句

一条 `for` 语句可以携带一条 `range` 子句, 这样就可以迭代出一个数组或切片值中的每个元素、一个字符串中的每个字符, 或者一个字典中的每个键值对, 以及持续地接收一个通道类型值中的元素, 随着迭代的进行, 每一次获取的迭代值(索引、元素、字符或键值对)都会赋值给相应的迭代变量:
``` Go
package main

import "fmt"

func main() {

  // Here we use `range` to sum the numbers in a slice.
  // Arrays work like this too.
  nums := []int{2, 3, 4}
  sum := 0
  for _, num := range nums {
    sum += num
  }
  fmt.Println("sum:", sum)

  // `range` on arrays and slices provides both the
  // index and value for each entry. Above we didn't
  // need the index, so we ignored it with the
  // blank identifier `_`. Sometimes we actually want
  // the indexes though.
  for i, num := range nums {
    if num == 3 {
      fmt.Println("index:", i)
    }
  }

  // `range` on map iterates over key/value pairs.
  kvs := map[string]string{"a": "apple", "b": "banana"}
  for k, v := range kvs {
    fmt.Printf("%s -> %s\n", k, v)
  }

  // `range` can also iterate over just the keys of a map.
  for k := range kvs {
    fmt.Println("key:", k)
  }

  // `range` on strings iterates over Unicode code
  // points. The first value is the starting byte index
  // of the `rune` and the second the `rune` itself.
  for i, c := range "go" {
    fmt.Println(i, c)
  }
}
```
> 在 `range` 关键字右边的是 `range` 表达式, `range` 表达式一般只会在迭代开始前被求值一次

使用 `range` 子句, 有以下三点需要注意:
- 若对数组、切片或字符串进行迭代, 且 `:=` 左边只有一个迭代变量时, 一定要小心, 这时只会得到其中元素的索引, 而不是元素本身; 这很可能不是你想要的
- 迭代没有任何元素的数组值、为 `nil` 的切片值, 为 `nil` 的字典或为 `""` 的字符串值, 并不会执行 `for` 语句中的代码, `for` 语句在一开始时就会直接结束执行, 因为这些值的长度都为 `0`
- 迭代为 `nil` 的通道值会让当前流程永远阻塞在 `for` 语句上!

## defer 语句

`defer` 语句用于延迟操作, 用于函数体内, 通常用于函数结束后的一系列操作, 例如清理操作

有几点需要注意:
- 如果 `defer` 函数中使用外部变量, 应通过参数传入
- 调用 `defer` 函数时若有参数传入, 那么将在 `defer` 执行时求值
- 多个 `defer` 函数调动时, 会与调用顺序相反, 可以想象为将调用压入同一栈, 调用时从栈顶取出

``` Go
func printNumbers() {
  for i := 0; i < 5; i++ {
    defer func(n int) {
      fmt.Printf("%d", n)
    }(i * 2)
  }
}
// 执行结果: 86420
```

## panic 和 recover

通常情况下, 函数向其调用方法报告的错误都是返回一个 `error` 类型的值, 但是遇到致命的轻局昂将会导致程序无法继续运行, Go推荐通过调用 `panic` 函数来报告错误

### panic

用于停止当前的控制流程并引发一个运行时恐慌, 可以接受一个任意类型的参数值, 不过这个参数值的类型通常是 `string` 或 `error`, 因为这样更容易描述运行时恐慌的信息:
``` Go
func main() {
  panic("a problem")
}

// 也可以通过Go的运行时系统来引发
fmt.Println([3]int{1, 2, 3}[3]) // 数组越界, 相当于调用panic并传入一个runtime.Error类型的参数值
```
> `runtime.Error` 是一个接口类型, 并且内嵌了Go内置的error接口类型

### recover

运行时恐慌一旦被引发, 就会向调用方传播直至程序崩溃, `recover` 用于“拦截”运行时恐慌的内建函数, 它可以使当前的程序从恐慌状态中恢复并重新获得流程控制权, `recover` 函数被调用后, 会返回一个 `interface{}`类型的结果, 如果当时的程序正处于运行时恐慌的状态, 那么这个结果就会是非 `nil` 的
> `recover` 函数应该与 `defer` 语句搭配起来使用:
``` Go
defer func() {
  if p := recover(); p != nil {
    fmt.Printf("Recovered panic: %s\n", p)
  }
}()
```
将此段代码放在函数体的开始处, 这样可以有效防止该函数及其下层调用中的代码引发运行时恐慌, 那么这个结果就会是非 `nil`, 一旦发现其结果非 `nil`, 就应该采取相应的措施

Go标准库中有一种常见的用法值得参考:
``` Go
// fmt中的Token函数部分声明
func (s *ss) Token(skipSpace bool, f func(rune) bool) (tok []byte, err error) {
  defer func() {
    if e := recover(); e != nil {
      if se, ok := e.(scanError); ok {
        err = se.err
      } else {
        panic(e)
      }
    }
  }()
  // 省略部分代码
}
```
在 `Token` 函数包含的延迟函数中, 当运行时恐慌携带值的类型是 `fmt.scanError` 时, 这个值就会被赋值给代表结果值的变量 `err`, 否则运行时恐慌就会被重新引发. 如果这个重新引发的运行时恐慌传递到了调用栈的最顶层, 那么标准输出上就会打印出类型这样的内容:
```
  panic: <运行时恐慌被首次引发时携带的值的字符串形式> [recovered]
    panic: <运行时恐慌被重新引发时携带的值的字符串形式>

  goroutine 1 [running]:
  main.func·001()
  <调用栈信息>

  goroutine 2 [runnable]:
  exit status 2
```
这里展现的惯用法与两个:
- 可以把运行时恐慌的携带值转换为 `error` 类型值, 并当做常规结果返回给调用方, 这样既阻止了运行时恐慌的扩散, 又传递了引起恐慌的原因
- 检查运行时恐慌携带值的类型, 并根据类型做不同的后续动作, 这样可以精确地控制程序的错误处理行为