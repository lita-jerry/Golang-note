# Go高级类型

Go的高级数据类型相当于自定义数据类型的模板或制作工具

## 数组

数组(array)是由若干相同类型的元素组成的序列

``` Go
var ipv4 [4]uint8 = [4]uint8{192, 168, 0, 1}
```

如果在函数体内, 可省略 `var` 关键字, 但赋值号必须由 `=` 变为 `:=`

数组长度也是数组类型的一部分, 只要数组长度不同, 即使是两个数组类型的元素类型相同, 他们也是不同的类型

与string类型一样, 一旦在声明中确定了数组类型的长度, 就无法改变它了

数组类型的零值一定是一个不包含任何元素的空数组, 一个类型的零值即为该类型变量未被显示赋值时的默认值, 以ipv4为例, 其所属类型的零值就是`[4]uint8{}`

在上面示例中, 等号右边的字面量表示该类型的一个值, 可以忽略在方括号中表示数组长度的正整数值:
``` Go
[...]uint8{192, 168, 0, 1}
```
方括号中的特殊标记 `...` 表示需要由Go编译器计算该值的元素数量并以此获得其长度

`索引表达式` 和 `切片表达式` 都可用于数组操作, 前者获取数组值中的一个元素, 后者获取一个元素类型与之相同的切片值

此外 `len` 和 `cap` 也都可以应用于数组值, 并都可以得到其长度

当需要详细规划程序所用的内存时, 数组类型非常有用, 使用数组值可以完全避免内存的二次分配操作, 因为它的长度是不可变的

## 切片

切片(slice)可以看做是一种对数组的包装形式, 它包装的数组称为该切片的底层数组. 反过来讲, 切片是针对其底层数组中某个连续片段的描述

``` Go
var ips = []string{"192.168.0.1", "192.168.0.2", "192.168.0.3"}
```

与数组不同, 切片的类型字面量(如`[]string`)并不携带长度信息. 切片的长度是可变的, 并且不是类型的一部分; 只要元素类型相同, 两个切片的类型就是相同的. 此外, 一个切片类型的零值总是 `nil`, 此零值的长度和容量都为0

切片值相当于对某个底层数组的引用, 其内部结构包含了3个元素:
- 指向底层数组中某个元素的指针
- 切片的长度
- 切片的容量

> 这里所说的容量是指: 从指针指向的那个元素到底层数组的最后一个元素的元素个数

切片值的容量意味着, 在不更换底层数组的前提下, 它的长度的最大值. 可通过 `cap` 函数和切片表达式, 在此前提下最大化一个切片值的长度: `ips[:cap(ips)]`

> 简单来说, 切片的截取表达含义, 例如: `ips[0:2]`, 其含义为从切片下标 `0` 开始, 到下标 `2` 之前的这一段数据, 即: `ips[0]、ips[1]`

除 `len` 和 `cap`, 内建函数 `append` 也可应用于切片值: `ips = append(ips, "192.168.0.4")`
[append文档](https://golang.org/pkg/builtin/#append)

另一个值得提的内置函数是 `make`, 它用于初始化切片、字典或通道类型的值, 对于切片类型来说, 用 `make` 函数的好处就是可以用很短的代码初始化一个长度很大的值: `ips = make([]string, 100)`, 其中100个元素的值都是string的零值: 空字符串""

To learn more about slices, read the [Slices: usage and internals](https://blog.golang.org/go-slices-usage-and-internals) article.

## 字典

Go中字典类型是散列表(hash table)的一个实现, 其官方称为 `map`, 散列表是一个实现了关联数组的数据结构, 关联数组是用于表示键值对的无序集合的一种抽象数据类型. Go中称键值对为 `键-元素对`, 它把字典值中的每个键都看作与其对应的元素的索引, 这样的索引在同一个字典值中是唯一的

``` Go
var ipSwitches = map[string]bool{}
```

变量 `ipSwitches` 的键类型为 string, 元素类型为 bool, `map[string]bool{}` 表示了一个不包含任何元素的字典值

与切片类型一样, 字典类型也是一个引用类型, 字典类型的零值是 `nil`, 字典值的长度表示了其中的建-元素对的数量, 其零值的长度总是0

索引表达式可用于字典值中键-元素的添加和修改:
``` Go
ipSwitches["192.168.0.1"] = true  // 不存在该键, 添加元素值
ipSwitches["192.168.0.1"] = false // 已存在该键, 修改元素值
delete(ipSwitches, "192.168.0.1") // 删除元素, 无论是否存在键, 都会执行完毕
```

## 函数和方法

在Go中, 函数类型是一等类型, 意味着可以把函数当作一个值来传递和使用, 函数值既可以作为其他函数的参数, 也可以作为其结果, 还可以根据函数类型这一特性生成闭包

一个函数的声明通常包括关键字 `func`、函数名、分别由圆括号包裹的参数列表和结果列表, 以及由花括号包裹的函数体:
``` Go
func doSomething(parameter int)(int, error) {
  // do something
}
```

函数可以没有参数列表, 也可以没有结果列表, 但空参数列表必须保留括号, 而空结果列表则不用:
``` Go
func doSomething() {
  // do something
}
```

另外, 参数列表中的参数必须有名称, 而结果列表中结果的名称则可有可无, 不过结果列表中的结果要么全都省略名称, 要么就全都有名称, 带有结果名称的 `doSomething` 函数的声明:
``` Go
func doSomething(parameter int)(result int, err error) {
  // 如果函数的结果有名称, 以他们为名的变量就会被隐式声明, 在这里就可以直接使用
  if parameter == 0 {
    err = errors.New("parameter is 0")
    return
  }
  // 给代表结果的变量赋值, 就相当于设置函数的返回结果
  result = parameter * 10
  return
}
```
> Go编程有一个惯用法, 即把 `error` 类型的结果作为函数结果列表的最后一员

可以在使用的时候实现函数执行体, 此时需要将函数提升成为一个类型, 调用的时候将函数当成参数传入(也叫闭包)

``` Go
// 定义函数类型
type funcType func (parameter1 int, parameter2 int) (result int, err error)

func callFuncType (a int, b int, func_type funcType) (result int, err error) {
  if func_type == nil {
    err = errors.New("func_type is nil")
    return
  }

  return func_type(a, b)
}
```

上面函数的使用方法:
``` Go
var func_type funcType = func (parameter1 int, parameter2 int) (result int, err error) {
  // 这里可以定义函数执行体内的逻辑
  result = parameter1 + parameter2
  return
}

var result, err = callFuncType(1, 2, func_type)
```

作为一等类型的函数类型让程序的灵活性大大增加, 接口不再是定义行为的唯一途径

> 函数类型的零值是 `nil`, 检查函数值是否为 `nil` 是有必要的

方法是函数的一种, 它实际上就是某个数据类型关联在一起的函数:
``` Go
type myInt int

func (x myInt) add(y int) myInt {
  x = x + myInt(y)
  return x
}
```

从声明上看, 方法只是在关键字 `func` 和函数名称之间, 加了一个由圆括号包裹的接收者声明. 接收者声明由两部分组成:
- `x` 指定类型的值在当前方法中的标识符
- `myInt` 表明这个方法与哪个类型关联

> 参数是以数据值传入到方法中, 修改参数 `x` 并不会影响原值(这里指调用者)
``` Go
x := myInt(1)
y := x.add(2)
fmt.Println(x, y) // 输出: 1 3
```

值方法接收者类型是非指针的数据类型, 若将方法改为指针方法, 如下:
``` Go
func (x *myInt) add(y int) myInt {
  *x = *x + myInt(y)
  return *x
}
```
此时 `fmt.Println(x, y)` 的输出为 `3 3`

> 对于某个非指针的数据类型, 与它关联的方法的集合中只包含它的值方法. 而对于它的指针类型, 其方法集合中既包含值方法也包含指针方法. 不过, 在非指针数据类型的值上, 也是能够调用其指针方法的, 因为Go在内部做了自动转换. 例如, 若 `add` 方法是指针方法, 那么表达式 `x.add(2)` 会被自动转换为 `(&x).add(2)`

### 参数可变型函数

参数可变型函数可以使用任何数量的尾随参数, 例如我们使用过的 `fmt.Println()`
``` Go
package main

import "fmt"

// Here's a function that will take an arbitrary number
// of `int`s as arguments.
func sum(nums ...int) {
  fmt.Print(nums, " ")
  total := 0
  for _, num := range nums {
    total += num
  }
  fmt.Println(total)
}

func main() {

  // Variadic functions can be called in the usual way
  // with individual arguments.
  sum(1, 2)
  sum(1, 2, 3)

  // If you already have multiple args in a slice,
  // apply them to a variadic function using
  // `func(slice...)` like this.
  nums := []int{1, 2, 3, 4}
  sum(nums...)
}
```
> 如果切片中已经有多个 `args`, 请使用 `func(slice...)` 这样的方法将他们应用于参数可变型函数

## 接口

Go的接口类型用于定义一组行为, 其中每个行为都由一个方法声明表示. 接口类型中的方法声明只有方法签名而没有方法体, 而方法签名包括且仅包括方法的名称、参数列表和结果列表
``` Go
type Talk interface {
  Hello (userName string) string
  Talk (heard string) (saying string, end bool, err error)
}
```
type、接口类型名称、interface以及由花括号包裹的方法声明集合, 共同组成了一个接口类型声明
> 其中每个方法声明必须独占一行

只要一个数据类型的方法集合中包含 `Talk` 接口声明的所有方法, 那么它就一定是 `Talk` 接口的实现类型, 这种接口实现方式完全是 `非入侵式` 的
``` Go
type myTalk string

func (talk *myTalk) Hello (userName string) string {
  // 省略部分代码
}

func (talk myTalk) Talk (heard string) (saying string, end bool, err error) {
  // 省略部分代码
}
```
> 与 `myTalk` 关联的所有方法均为指针方法, 意味着 `myTalk` 类型并不是 `Talk` 接口的实现类型, `*myTalk` 才是

一个接口类型的变量可以被赋予任何实现类型的值, 例如: `var talk Talk = new(myTalk)`, 内建函数 `new` 的功能是创建一个指定类型的值, 并返回该值的指针. 若想确定变量 `talk` 中的值是否属于 `*myTalk` 类型, 则可以用类型断言来判断: `_, ok := talk.(*myTalk)`

Go的数据类型之间并不存在继承关系, 接口类型之间也是如此, 不过, 一个接口类型的声明中可以嵌入任意其他接口类型. 更通俗地讲, 一组行为中可以包含其他的行为组, 而且数量不限
``` Go
type ChatBot interface {
  Hello2 (userName) string
  Talk
}

// 使用ChatBot
var chat ChatBot = new(myTalk)
chat.Hello2("lisi")
chat.Hello("zhangsan")
```

## 结构体

结构体类型不仅可以关联方法, 而且可以有内置元素(又称字段), 结构体类型的声明一般以关键字 `type` 开始, 并依次包含类型名称、关键字 `struct` 以及由花括号包裹的字段声明列表
``` Go
type simpleCN struct {
  name string
  talk Talk
}
```
结构体类型中的每个字段声明都需独占一行, 一般情况下, 字段声明需由字段名称和字段类型的字面量组成

结构体类型的值一般由复合字面量来表达, 复合字面量可以由类型字面量和由花括号包裹的键值对列表组成, 在同一个结构体字面量中, 一个字段名称只能出现一次

可以忽略字段的名称, 但是有两个限制:
- 要么忽略所有字段名称, 要么都不忽略
- 多个字段值的顺序应该与结构体类型中字段声明的顺序一致, 并且不能够省略对任何一字段的赋值. 例如 `simpleCN{"simple.cn", nil}` 是合法的, 而 `simpleCN{nil, "simple.cn"}` 和 `simpleCN{"simple.cn"}` 就不合法. 在不忽略字段名称的写法中, 未被赋值的字段会自动被其类型的零值填充

> 与数组类型相同, 结构体类型属于值类型, 因此结构体类型的零值不是 `nil`, 例如 `simpleCN` 的零值就是 `simpleCN{}`

结构体的匿名字段:
``` Go
type User struct {
  name string
}

type Team struct {
  User // 匿名字段, 那么默认Team就包含了User的所有字段, 也包含User的所有method方法, 即: 字段/方法继承
  name string // 假设有字段名冲突, 访问Team.name也只是Team的name字段, 如需访问User的name需要Team. User.name, 即: 字段/方法覆盖
}
```
