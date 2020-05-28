---
title: Go语言入门
date: 2020-05-20 15:45:39
tags:
	- go
	- golang
categories: 编程
copyright: true
top: 1
typora-root-url: ../
typora-copy-images-to: ../images
---





![00_go](/images/00_go.png)

## GO语言

耳闻已久的Go语言，今天入门了解一下。

Go之所以火爆的原因之一是，拥有CoreOS，Kubernetes（Google），InfluxDB知名大客户，另外Docker也是Go语言所编写。

Go语言的功能特性（所有这些特性，以后慢慢学习了解）

- 并发支持

- 简单性和一致性
- 面向对象
- 指针
- 编译器
- 包管理

Go语言标准库地址

> https://golang.org/pkg 官网
>
> https://studygolang.com/pkgdoc 中文



项目源码地址

>  https://github.com/golang/go



## 快速上手

通过安装包

> 国内下载地址： https://studygolang.com/dl

配置

```bash
➜  ~ go version
go version go1.14.3 darwin/amd64
# 如果不存在该命令，请添加环境变量
# ~/.bash_profile
export PATH=$PATH:/usr/local/go/bin
```

在国内加速器是必要的

```bash
# 启用 Go Modules 功能
go env -w GO111MODULE=on
go env -w  GOPROXY=https://goproxy.io
go env -w  GOPROXY=https://goproxy.cn
go env -w GOPROXY=https://mirrors.aliyun.com/goproxy/

# Go version >= 1.13
go env -w GOPROXY=https://goproxy.io,direct
# 设置不走 proxy 的私有仓库，多个用逗号相隔
go env -w GOPRIVATE=*.example.com

# 检测
go env | grep GOPROXY
go env | grep GOBIN

```



开启Go的“Hello world”

创建文件 `hello.go` 文件

```go
package main

import "fmt"

func main() {
	fmt.Printf("hello, world\n")
}
```

运行

```bash
$ go run hello.go
hello, world
```

编译

```bash
 go build -o hello hello.go
 # 执行文件
 ./hello
 Hello, world.
```

Mac | Linux 上也可以编译出 exe 文件

```bash
GOOS=windows go build -o hello.exe hello.go
```

Windows编译

```bash
go build -o hello.exe hello.go
```

更多查看下面地址

> https://github.com/golang/go/wiki/WindowsCrossCompiling



## IDE

工具的选择上 vscode 似乎比较多，另外就是GoLand， 附上地址

> https://code.visualstudio.com
>
> https://www.jetbrains.com/go

使用 vscode， 添加go插件

![0_go](/images/0_go.png)





## 收集一些学习资料

> Go官网博客 https://blog.golang.org
>
> Go语言中文社区 https://studygolang.com
>
> 知识图谱 https://github.com/gocn/knowledge
>
> 中文在线 https://mikespook.com/learning-go