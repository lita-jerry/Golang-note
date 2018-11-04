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
