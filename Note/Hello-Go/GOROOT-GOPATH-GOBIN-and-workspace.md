# GOROOT GOPATH GOBIN与workspace

## GOROOT
Golang环境的安装目录,Golang安装完成后就已经确定.

## GOPATH
指向workspace的路径,需要将GOPATH路径添加到环境变量: $GOPATH
> $GOPATH 是Go命令所依赖的重要环境变量.
GOPATH是workspace的根目录,包含以下文件夹
- src 源文件的位置: .go, .c, .g, .s
- pkg 已编译包(.a)的位置
- bin Go构建可执行文件的位置
go get 工具将包下载到GOPATH的第一目录
> 从Go 1.8开始,如果GOPATH未设置环境变量，则默认为$HOME/go(Unix/Linux)和%USERPROFILE%/go(Windows).
> 有些工具假设GOPATH只有一个目录.

## GOBIN
该文件夹用于放置go install 和go get编译main包后生成的二进制文件

## workspace
项目的工作空间,即存放项目工程的位置,GOPATH是指向workspace根目录的路径.

## 面试可能会提问:
Q: 设置GOPATH的意义?
A: 为了集中组织代码和项目工程,以及代码间相互引用.

Q: 工作区是指? GOPATH是指?
A: 工作区是指存放工程文件的位置, GOPATH是指向工作区的根目录的路径.

Q: 编译后的.a文件是什么?
A: .a文件是在Linux下编译的归档文件, 也就是archive文件, 是程序编译后生成的静态库文件.
