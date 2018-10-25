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

编译并安装包及其依赖项

``` sh
go install [-i] [build flags] [packages]
```

该命令在内部执行分为两个步骤

- 编译生成可执行文件
- 将生成的文件从临时文件夹移动到`$GOPATH/pkg`或`$GOPATH/bin`中.

> `go install`命令参数与`go build`的参数基本相同.

## go get

下载并安装包和依赖项

``` sh
go get [-d] [-f] [-t] [-u] [-v] [-fix] [-insecure] [build flags] [packages]
```

`go get`执行两步操作,第一将资源文件下载下来,第二步执行`go install`

工具会根据域名的不同调用不同的下载工具:

| 资源域名  |  下载工具  |
| ------------ | ------------ |
|  GitHub  |  Git  |
|  BitBucket  |  Git, Mercurial  |
|  Google Code Project Hosting  |  Git, Mercurial, Subversion |
|  Launchpad  |  Bazaar  |

主要参数说明:

- `-d` 表示只下载,但不安装.

- `-u` 让命令利用网络来更新已有代码包及其依赖包,默认情况下,该命令只会从网络上下载本地不存在的代码包,而不会更新已有的代码包.

- `-f` 仅在使用`-u`标记时才有效,该标记会让命令程序忽略掉对已下载代码包的导入路径的检查,如果下载并安装的代码包所属的项目是你从别人那里Fork过来的,那么这样做就尤为重要了.

- `-fix` 包下载完成后,先执行修正动作,而后再进行编译和安装.

- `-t` 同时下载测试所需的包.

- `-v` 显示执行的命令.

- `-insecure` 表示可以使用非安全的scheme(如HTTP)下载包,如下载包所有的网络协议不支持HTTPS,则可以添加该标记,并在安全的网络环境使用.

> `go get`命令还支持`go build`的参数,详细请参考`go help build`.

## go doc

## go env

## go fix

## go run
