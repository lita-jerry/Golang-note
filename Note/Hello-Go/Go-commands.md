# Go命令

## 用法

``` sh
go <command> [arguments]
```

可通过 `go` 命令查看

![](images/command-go.png?raw=true)

使用 `go help <command>` 查看该命令的更多信息.

接下来我们将介绍一些常用命令及参数的使用方法.

## go build

编译包和依赖项

``` sh
go build [-o output] [-i] [build flags] [packages]
```

- 可单独对一个go文件执行build命令,也可对一个目录执行build命令,如果对一个目录执行,则会对整个目录的go文件进行编译,同目录下的源码文件的包声明要一致.

- 如果是普通包(非main),执行`go build`命令将不会产生文件,如需要生成文件,则需要使用`go install`,生成的对应文件在`$GOPATH/pkg`

- 如果是main包,执行`go build`后将在当前目录产生执行文件,如果需要在`$GOPATH/bin`下生成执行文件,则需要执行`go install`或`go build -o /path/xx.a`(Windows环境下为xx.exe)

- `go build`会忽略目录下以'_'或者'.'开头以及以'_test.go'结尾的go文件.

- 可以针对不同系统编译不同的文件,`go build`将会选择性的编译以系统名为后缀名的文件,例如xx_linux.go xx_darwin.go xx_windows.go xx_freebsd.go 文件,如果在Linux环境下编译,则只编译xx.linux.go,其他文件将忽略.

主要参数说明:

- `-a` 完全编译,更新全部至最新包,如果不但要编译依赖的包,还要安装他们的执行文件,可以加入标记`-i`.

- `-n` 打印编译命令但不执行.

- `-p n` 以n核CPU并行编译,默认为本机CPU核数.

- `-race` 开启编译的时自动检测数据竞争的情况,目前只支持64位的机器(linux/amd64, freebsd/amd64, darwin/amd64 windows/amd64).

- `-msan` 启用[memory sanitizer](https://github.com/google/sanitizers/wiki/MemorySanitizer)的互操作,只支持linux/amd64, linux/arm64 并且只有 Clang/LLVM 作为主C编译器的平台.

- `-v` 在编译的时候打印包的名字.

- `-work` 打印临时编译目录的名称,并且在编译结束退出后不删除该目录.

- `-x` 打印编译命令,可以看到具体执行了哪些操作.

- `-o` 指定输出路径、文件名.

- `-i` 编译并且安装包,可以理解为`go build` + `go install`.

## go install

## go get

## go doc

## go env

## go fix

## go run

## go