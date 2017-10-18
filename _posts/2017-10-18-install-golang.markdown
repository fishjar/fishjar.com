---
layout: post
title:  "有关Go语言的安装"
tags:
  - Go
  - golang
---

Linux安装Go一般有三种方式：

1. 下载源码自己编译安装，适合喜欢折腾、定制
2. 下载编译好的二进制包，解压安装，需要手工配置环境变量
3. 使用包管理工具（apt-get/yum）安装，操作简单，但是版本可能比较落后

**本文仅针对Ubuntu16.04的第二种方式安装**

## 下载二进制包

下载网页： <https://golang.org/dl/>

文件地址： <https://storage.googleapis.com/golang/go1.9.1.linux-amd64.tar.gz>

通过浏览器下载到本地文件夹，或者通过命令下载：

``` bash
curl -O https://storage.googleapis.com/golang/go1.9.1.linux-amd64.tar.gz
```
或者

``` bash
wget https://storage.googleapis.com/golang/go1.9.1.linux-amd64.tar.gz
```

## 解压文件

参考页面：<https://golang.org/doc/install>

命令解压文件并复制到安装目录：

``` bash
tar -C /usr/local -xzf go1.9.1.linux-amd64.tar.gz
```

其中`/usr/local`是安装目录，也可以自己设定其他的。

完成后，`/usr/local`路径后会多出一个`go`目录。


## 配置环境变量

按我到理解，配环境变量的目的是，告诉系统什么目录有可执行文件，这执行命令就会依次到相关目录搜索。

在终端执行`go`命令时，系统就可以找到`Go`的安装路径，找到Go的执行文件。

打开`/etc/profile`文件（全局配置），或者`$HOME/.profile`文件（本用户配置），最后一行添加：

``` bash
export PATH=$PATH:/usr/local/go/bin
```

上面写的`/usr/local/go/bin`，即前面的安装目录下面可执行文件的位置。

至此，应该可以在终端输入`go`命令了。


## 配置工作目录

目的应该是告诉go哪些目录是一个标准的go项目包，所以这个配置是一个路径列表

参考页面： <https://github.com/golang/go/wiki/Setting-GOPATH>

按照官方的介绍，如果工作目录是`$HOME/go`的话，是不需要配置的，如果其他目录则需要。

打开`$HOME/.profile`文件（本用户配置），最后一行添加：

``` bash
export GOPATH=$HOME/go:$HOME/work
```
上面示例配置了两个路径，`$HOME/go`和`$HOME/work`

保存，然后让配置生效

``` bash
source ~/.profile
```

至此，安装完成。

## 其他事项

一个标准的go项目结构如下：

``` bash
├── bin
├── pkg
└── src
    ├── hello
    │   └── hello.go
```
其中: 

- src 目录包含Go的源文件，它们被组织成包（每个目录都对应一个包），
- pkg 目录包含包对象，
- bin 目录包含可执行命令。

所以为了方便系统执行文件，也可以把工作目录下的`bin`目录添加到环境变量。

``` bash
export PATH=$PATH:$HOME/go/bin
```

















