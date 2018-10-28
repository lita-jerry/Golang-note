# Go基本元素

## 标识符

同一代码块中不允许出现同名的程序实体

大小写用于区分访问权限

预定义标识符:

- 所有基本数据类型的名称
- 接口类型error
- 常量true、false和iota

所有内奸函数的名称: `append`、`cap`、`close`、`complex`、`copy`、`delete`、`imag`、`len`、`make`、`new`、`panic`、`print`、`println`、`real`和`recover`

> 空标识符`_`用在变量声明或代码包导入,若声明一个变量而不使用,则编译器会报错,使用空标识符`_`就可以绕过编译器检查,不会产生任何操作

## 关键字

关键字是被编程语言保留的字符串序列,不能把他们用作标识符,因此也叫保留字

| 类别 |  关键字  |
| ------------ | ------------ |
|  程序声明  |  `import`和`package`  |
|  程序实体声明和定义  |  `chan`、`const`、`func`、`interface`、`map`、`struct`、`type`和`var`  |
|  程序流程控制  |  `go`、`select`、`break`、`case`、`continue`、`default`、`defer`、`else`、`fallthrough`、`for`、`goto`、`if`、`range`、`return`和`switch`  |

Go的关键字共有`25`个,其中与并发编程有关的关键字有`go`、`chan`、`select`

特别说明一下关键字`type` ———— 类型声明, 用于声明一个自定义类型:

``` Go
type myString string
```

这里将`myString`的类型声明为`string`类型的一个别名类型,反过来说,`string`类型是`myString`类型的潜在类型

虽然类型及其潜在类型是不同的两个类型,但是他们的值可以相互转换,不产生新值: `string(myString("ABC"))`

## 字面量

简单来说,字面量的意思就是值的一种标记法

常用的字面量有以下三种:

- 用于表示基础数据类型值的各种字面量,例如表示浮点数类型值的`3E-2`
- 用于构造各种自定义的复合数据类型的类型字面量,例如用于表示一个名称为`People`的结构体类型:

``` Go
type People struct {
	Name	string
	Sex	int
}
```

- 用于表示符合数据类型的值的复合字面量

``` Go
People{Name: "ZhangSan", Sex: 1}
```

其中People表示这个值的类型,紧随其后的就是由键值对表示的复合元素列表

## 分隔符

## 操作符