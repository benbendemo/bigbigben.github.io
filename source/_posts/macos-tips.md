---
title: macOS常用小技巧
date: 2020-09-06 23:36:01
categories: 编程
tags: [macOS]
---

这是一个长期更新帖，用来记录本人使用macOS遇到过的问题以及应对问题的方法，欢迎查看以及勘误。

# 软件安装

## macOS安装Homebrew报错

问题：在macOS里输入命令直接安装Homebrew提示报错。

```
shawn@xiaochangdeMacBook-Pro ~ % /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
curl: (7) Failed to connect to raw.githubusercontent.com port 443: Connection refused
```

原因：这是GitHub的`raw.githubusercontent.com`域名解析被污染所致

解决办法：修改hosts文件，使用IP地址读取文件内容

1. 查询真实IP地址

在https://www.ipaddress.com/查询raw.githubusercontent.com的真实IP。

![](/Users/shawn/Nodejs/Hexo/ipaddress_search.jpg)

2. 修改hosts

sudo vim /etc/hosts
添加如下内容：

199.232.68.133 raw.githubusercontent.com

3. 在终端再次安装Homebrew可以成功



## macOS安装IPython设置软连接

macOS使用pip3安装IPython成功后。
如果出现	`ipython command not found`，执行下面这条软连接命令即可。

ln -s /Library/Frameworks/Python.framework/Versions/3.6/bin/ipython /usr/local/bin/



# 网络使用

## 查看IP地址

macOS打开终端，使用如下命令可以查看本机IP地址。

```shell
ifconfig | grep "inet " | grep -v 127.0.0.1
```