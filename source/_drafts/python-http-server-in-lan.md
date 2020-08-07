---
title: Python http.server局域网应用
date: 2020-02-28 17:52:31
categories: [编程]
tags: [Python,http.server]
---

众所周知，Python语言内置了一个简易的HTTPServer，在局域网环境，非常适合用于文件共享。

使用方法如下：

进入你要共享的目录，比如当前Download​文件夹，在Python2环境下输入：

```
python -m SimpleHTTPServer 8001
```

会将当前目录下的所有文件生成链接地址，开启一个共享服务器。

不指定端口号，默认使用8000。

这条命令默认bind在0.0.0.0这个IP地址上，在本机浏览器里输入127.0.0.1:8001或使用你本机IP地址加端口（例如：192.168.0.10:8001）就可以访问该目录下所有文件内容；局域网内其它机器知道你本机IP地址，也可以在它的机器上访问该目录所有内容。

如果要绑定在指定端口，只允许本机访问，python2下需要修改程序代码来实现这个功能。请参考

[非常简单的PYTHON HTTP服务](https://coolshell.cn/articles/1480.html)进行设置。



在Python3环境下，需要这样使用：

```
python3 -m http.server
```

默认也是Bind 0.0.0.0这个IP地址，默认端口也是8000。如果需要指定IP地址和端口，按照下面方法进行使用。

```
python3 -m http.server --bind 127.0.0.1 6789
```



我本机是macOS，另外家里还有一台老实的Windows XP，注意两台机器互相传输文件时，需要在防火墙中将Python程序拦截关闭；另外就是必须要Bind到0.0.0.0这个IP地址，否则无法互相访问。



今天在思考如何在macOS上进行流量检查时，发现*nix系统上有一个精简的命令可以对指定端口进行监控。比如，在macOS上将当前文件夹下内容以6789端口开放共享，直接在终端使用下面命令即可监控流量。

```
nc -l localhost 6789
```



为了进一步流量监控，使用了Proxyman这款工具，但是发现它和Charles有同样一个问题就是，无法监控本机的流量。比如我在本机开启了http://localhost:8080服务，这些软件默认不对127.0.0.1IP地址进行抓包。见[Proxyman#Issue226](https://github.com/ProxymanApp/Proxyman/issues/226)

查看Proxyman文档，发现修改host文件即可绕过这个限制。

macOS系统里Host文件是`/etc/hosts`，提权后添加如下这一行。即可通过http://proxyman.tk:8080在本机访问这个共享页面，macOS修改Host文件是即刻生效的。

```
127.0.0.1       proxyman.tk
```



如果想在局域网Windows XP这台机器上也使用这个伪IP地址访问，需要修改Windows系统里的Host文件。Host文件路径是：`c:/windows/system32/drivers/etc/hosts`，在里面加上如上这一行配置命令。

打开一个CMD窗口，输入下面两条命令使Host文件修改生效：

```
ipconfig /displaydns
ipconfig /flushdns
```

然后在XP机器上，使用http://proxyman.tk:1234也可以访问XP本机器上的共享页面。

