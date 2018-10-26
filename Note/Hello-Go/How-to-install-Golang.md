# 如何安装Golang环境

## 下载安装包

[官网下载](https://golang.google.cn/dl/)

## 傻瓜式安装

macOS为例, 安装至 /usr/local/go

## 配置环境变量

> 可以写个配置脚本

``` sh
vim ~/.bash_profile
```

> 写入环境变量

```
export GOPATH=/Users/YouName/GolangWorkSpace/go
export GOBIN=/Users/YouName/GolangWorkSpace/go/bin
export PATH=$PATH:$GOBIN
```

> 执行脚本

``` sh
source ~/.bash_profile
```

> 查看当前环境变量 

``` sh
go env
```

> 指令出现当前Golang的环境变量, 主要查看GOROOT、GOPATH、GOBIN

```
GOROOT="/usr/local/go"
GOPATH="/Users/YouName/GolangWorkSpace/go"
GOBIN="/Users/YouName/GolangWorkSpace/go/bin"
```

## 编写第一个Go程序

> 创建工程

``` sh
cd $GOPATH
mkdir src bin pkg
cd src/
mkdir hello
cd hello/
vim main.go
```

> 在main.go中编写helloworld代码

``` 
package main

import (
    "fmt"
)

func main() {
    fmt.Println("hello world!")
}
``` 

> 编译安装

``` sh
go install
```

> 生成的可执行文件hello, 存放在$GOPATH/bin下, 可运行查看结果

``` sh
$GOPATH/bin/hello
```

## 关于IDE和插件

支持jetbrains全家桶(按年付费, 刚买的...肉疼): [GoLand](https://www.jetbrains.com/go/)

LiteIDE是一款专门为Go语言开发的跨平台轻量级集成开发环境(IDE), 由visualfc编写: [GitHub](https://github.com/astaxie/build-web-application-with-golang/blob/master/zh/01.4.md)