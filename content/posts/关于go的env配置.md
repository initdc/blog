---
title: "关于go的env配置"
date: 2020-06-15T23:26:12+08:00
draft: false
---

## 缘起

都是因为go, 可能是go非常不错, 作为一个熟悉各种编程语言Hello World写法, 被npm深深摧残过之后的人, 终于还是回到了包管理还是稍微靠谱的go语言, 而且C-like.

 go可能天生高并发, 而且是可编译的静态语言, 至少性能会表现的不错.

也可能是因为v2ray装的太多, 还是不小心学会了Caddyserver的用法, 还妄想深入的学习一下Caddyserver 2的用法. 事实证明, 任何明显的大改结构的更新都是辣鸡, 我从来没见过这么辣鸡的CLI, 我怎么知道为什么Caddyserver 2的filemanager模块可以用命令行启动而配置文件一直写不对???

但go还是太好, 比起谁都不知道它到底还要装多少依赖的npm, 还要装几个版本的py, go的基本盘还是挺稳的.

---

## 我吃柠檬

macOS说好也不好, 桌面软件好, 环境不算差, 但不太OK(毕竟差一套工具链), 最吐血的是因为国内的网络呵呵呵呵. 现在网这么差, 我怎么抄代码???

brew会把软件安装到它的路径 ```/usr/local/Cellar``` 和 ```/usr/local/Caskroom``` , 有部分软件是没问题的, 但要是有修改配置文件和设置路径的需求可就是遭了难了. 谁让macOS 连个正常的 ```/etc``` 都没嘚, 每次装软件还得先更新brew本体, 您可真是太慢了. 

鉴于我总是搞不懂brew安装的go的版本和路径, (系统也会自带一个版本), 我用了官网的安装包, 路径还是相当正常的, ```/usr/local/go/bin/go``` .

然后面向IDE编程, Goland. 然后就迎来了本篇的问题, 也是因为这个问题的解决让我有了写技术博客的想法.

---

## 正文

go的env是做什么用的呢? 讲道理我也不知道, 先来看看默认的有什么吧, 鉴于我已经配置好我的 .zshrc, 就看看 root 用户默认的.
```
GO111MODULE=""
GOARCH="amd64"
GOBIN=""
GOCACHE="/var/root/Library/Caches/go-build"
GOENV="/var/root/Library/Application Support/go/env"
GOEXE=""
GOFLAGS=""
GOHOSTARCH="amd64"
GOHOSTOS="darwin"
GOINSECURE=""
GONOPROXY=""
GONOSUMDB=""
GOOS="darwin"
GOPATH="/var/root/go"
GOPRIVATE=""
GOPROXY="https://proxy.golang.org,direct"
GOROOT="/usr/local/go"
GOSUMDB="sum.golang.org"
GOTMPDIR=""
GOTOOLDIR="/usr/local/go/pkg/tool/darwin_amd64"
GCCGO="gccgo"
AR="ar"
CC="clang"
CXX="clang++"
CGO_ENABLED="1"
GOMOD=""
CGO_CFLAGS="-g -O2"
CGO_CPPFLAGS=""
CGO_CXXFLAGS="-g -O2"
CGO_FFLAGS="-g -O2"
CGO_LDFLAGS="-g -O2"
PKG_CONFIG="pkg-config"
GOGCCFLAGS="-fPIC -m64 -pthread -fno-caret-diagnostics -Qunused-arguments -fmessage-length=0 -fdebug-prefix-map=/tmp/go-build494680677=/tmp/go-build -gno-record-gcc-switches -fno-common"
```
Goland需要配置的参数

![a](/2020-06-16a.png)

看到就能大概了解了, GOROOT是go的程序文件夹, GOPATH可能就是放导入的各种包的文件路径了. 但只有如此吗?我们看看go的命令参数.

```
Go is a tool for managing Go source code.

Usage:

	go <command> [arguments]

The commands are:

	bug         start a bug report
	build       compile packages and dependencies
	clean       remove object files and cached files
	doc         show documentation for package or symbol
	env         print Go environment information
	fix         update packages to use new APIs
	fmt         gofmt (reformat) package sources
	generate    generate Go files by processing source
	get         add dependencies to current module and install them
	install     compile and install packages and dependencies
	list        list packages or modules
	mod         module maintenance
	run         compile and run Go program
	test        test packages
	tool        run specified go tool
	version     print Go version
	vet         report likely mistakes in packages

Use "go help <command>" for more information about a command.

Additional help topics:

	buildmode   build modes
	c           calling between Go and C
	cache       build and test caching
	environment environment variables
	filetype    file types
	go.mod      the go.mod file
	gopath      GOPATH environment variable
	gopath-get  legacy GOPATH go get
	goproxy     module proxy protocol
	importpath  import path syntax
	modules     modules, module versions, and more
	module-get  module-aware go get
	module-auth module authentication using go.sum
	module-private module configuration for non-public modules
	packages    package lists and patterns
	testflag    testing flags
	testfunc    testing functions

Use "go help <topic>" for more information about that topic.

```
```go build``` 用来编译源码

```go get``` 用来下载模块

```go install``` 编译并安装源码到 GOBIN

```go run``` 用来直接运行源码

GOBIN 是什么路径呢? 如果没有设置的话, 默认是 ```GOBIN=$GOPATH/bin``` 

那么GOPATH 又在哪呢? 如果没有设置的话, 默认是 ```GOPATH=$HOME/go```

GOENV 存放用```go env -w```设置的环境变量.

这个文件里适合设置什么呢? 可能常常变动的参数比较好, 比如go get源的镜像

例如 ( 使用shell操作 )

```
$ go env -w GO111MODULE=on
$ go env -w GOPROXY=https://goproxy.cn,direct
```

参数好多, 那么第一重要的变量是那个? 是GOROOT, 所有的GO环境参数都是为go服务的, 如果不能确定GOROOT, 设置其他变量时会出错.

而GOROOT的位置就是go本体的父文件夹, 所以有个确定的go本体位置很重要, 这就是不推荐用brew安装go的原因了.

---
## 中程

```go install``` 这么好用的工具就是为了让你每次运行程序的时候, 少打一个 ```.``` 和一个 ```/``` , 顺便少打十万个字符的路径而已. 但是默认这个 GOBIN 的参数, 是没有导入PATH的, **we need do a big job** , 把变量写入shell的配置文件, ```.bashrc``` 或 ```.zshrc```

鉴于我$HOME 目录文件夹太多, 我又喜欢清理, 所以把 go 目录换成 .go 目录. 同时, 为了避免什么时候用brew装一个go语言写成的程序, brew自动下载一个版本的go 作为依赖, 扰乱基于版本的go的环境参数, 我写入(写死)了 GOROOT参数.

具体如下, 供参考:

```
export GOROOT=/usr/local/go
export GOPATH=$HOME/.go
export GOBIN=$GOPATH/bin

PATH=$PATH:$GOPATH:$GOBIN
export PATH
```

面向IDE编程, 添加自动化编译配置, Goland需要哪些配置呢?

![c](/20200616c.png)

默认的 Go tool arguments 里有一个 ```-i``` 参数, 不知道为什么, 有这个, build时会提示权限不够. 我误打误撞删了反而没有问题, 唉, 让我咋说呢.

![b](/2020-06-16b.png)

写go代码时, 要导入很多包, 然后这就是关于项目GOPATH 和全局GOPATH的设置, 鉴于咱们网络基础设施也不好, 还赶上了移动宽带, 就不那么高要求了, 还是全局吧...

之前提到 GOBIN 在 GOOATH 里, 那么还有什么在这个 GOPATH 呢?

![d](/20200616d.png)

pkg 里面就是各种包了, go get 下载的包, import用到的基础包, 还会根据版本标识文件夹名,  src 目录它这个意思应该是 让你在这个里面里放源码, 甚至新建项目, 我之前干过, 我把 go目录删了... 官方推荐的, 我们都不干, **I run on my way**.

## 尾声

在解决该问题之前 甚至写本文的时候, 我还是没搞懂这三个文件夹的到底干啥的, 参考了非常好的一个博文, 来自 Medium, [Getting started with Go](https://medium.com/rungo/working-in-go-workspace-3b0576e0534a) , 里面说 src 是放 go get的文件, pkg 放根据指令集优化的二进制和调试符号(是个啥?)和源码. 中间还提了一嘴, 项目找包会先找 GOPATH, 找不到找GOROOT.

带着疑问, 我打开了GOROOT, 翻翻目录,

![e](/20200616e.png)

老外诚不欺我, 虽然你说的没错, 但我没理解, 这个锅, 得google机翻背.

我最近很苦恼的一件事就是Chrome mac版本没定制, 纯原生, 一堆傻叼操作, 而且 OmegaSwitchy识别新的未加载源很慢, win版本没问题, mac上 Firefox版的插件也没问题, 但Firefox又没好用的翻译插件. 现在DeepL还转收费制了, 真是还未普及, 就以收费, 不得不说, 炼丹成本好像是高...

## 终章

这个博客我还没想好部署到哪... 主题我也不会用, 我也想搞个牛逼的主题啊, 人菜事还多. 我还想开个评论系统, 目前看, 技术不够... 打扰了, 告辞 .

希望这个开篇技术博能开启我的编程人生, 另人生苦短, 我不想早早脱发, 但已经拦不住趋势了...