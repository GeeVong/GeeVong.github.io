---
layout: post
title:  "go module"
date:   2020-2-9 
header-style: text
categories: think
tags: idea
---


go module 问题：本文记录go module 使用过程中遇到的问题
资料参考
https://eddycjy.com/posts/go/go-moduels/2020-02-28-go-modules/


####本文要点：

```
1.从0开始搭建go开发环境
2.go项目依赖包管理实践操作--使用module
3.项目托管git实践操作
```


##### 1.从0开始搭建go开发环境

1.需要一个go sdk 包,并且解压 [go1.14.2.darwin-amd64.tar.gz](https://golang.org/dl/)

2. 编辑 .envrc 配置gopath: export GOPATH=$(pwd)/.go



##### 2.go项目依赖包管理实践操作--使用module

这个是时候已经可以进行开发了，引入第三方库。

```go
package main

import (
	"fmt"
	"github.com/7huang/go-mudule/testmod"
)

func main() {
	fmt.Println(testmod.SayHello("99"))
}
```


1. go mod init  gomodule  生成go.mod 文件
2. go download
3. go vender 处理报红问题



Go modules 命令：

| 命令            | 作用                             |
| --------------- | -------------------------------- |
| go mod init     | 生成 go.mod 文件                 |
| go mod download | 下载 go.mod 文件中指明的所有依赖 |
| go mod tidy     | 整理现有的依赖                   |
| go mod graph    | 查看现有的依赖结构               |
| go mod edit     | 编辑 go.mod 文件                 |
| go mod vendor   | 导出项目所有的依赖到 vendor 目录 |
| go mod verify   | 校验一个模块是否被篡改过         |
| go mod why      | 查看为什么需要依赖某模块         |



#### 遇到问题

```
1.倒入外部依赖包之后，ide还是存在标红问题
```

![image-20200702212223938](/Users/hq/Library/Application Support/typora-user-images/image-20200702212223938.png)

实际上该外部依赖包已经倒入。因为程序已经可以编译通过。

解决方式： go mod vender。（有点疑问）