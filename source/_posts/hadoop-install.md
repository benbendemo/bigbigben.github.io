---
title: Ubuntu16.04安装Hadoop3.1.0
date: 2018-04-24 17:00:04
categories: 编程
tags: [Hadoop]
---

Ubuntu系统稳定性有点差，今天清理空间后，重新启动无法进入图形界面。考虑到稳定性还有我的MBP磁盘空间不够大，决定在本机搭建Hadoop3.1.0环境。

由于网上，同类型的安装教程很多，我就不去东施效颦，我只把我安装过程中参考的帖子和遇到的异常问题记录下来，以下是全部内容。

Hadoop官方在3.0.0及以后的版本里将一些默认的端口号进行了修改。Issue见[这里](https://issues.apache.org/jira/browse/HDFS-9427)。

> Namenode ports: 50470 --> 9871, 50070 --> 9870, 8020 --> 9820
> Secondary NN ports: 50091 --> 9869, 50090 --> 9868
> Datanode ports: 50020 --> 9867, 50010 --> 9866, 50475 --> 9865, 50075 --> 9864
>

**参考资料：**

- [Mac OS X EI Captain 安装 Hadoop 3.0.0-alpha2](https://www.jianshu.com/p/0e7f16469d87)

- [mac下hadoop环境的搭建以及碰到的坑点](https://blog.csdn.net/chenxun_2010/article/details/78238251)

- [Mac OS X Yosemite安装Hadoop 2.6记录](https://blog.csdn.net/yechaodechuntian/article/details/44312343)


------

这两天因工作需要，需要搭建一套Hadoop集群环境。

参照各种帖子，折腾2天，算把Hadoop环境配置起来。中间踩了无数坑，单机模式和伪分布模式（不开启Yarn服务）下WordCount测试都能够成功；集群环境暂时还没配好，随后开始布置。

在这个学习过程中，看到几篇写得不错的技术帖，统一收集整理在此。

PS：Ubuntu16.04下面发现阿里云的源有好几个网址存在问题，换用[中科大的源](https://mirrors.ustc.edu.cn/repogen/)，明显感觉稳定很多。

**参考资料：**

- [Hadoop部署 Ubuntu14.04](http://www.cnblogs.com/heavyhe/p/4546880.html)

- [Hadoop安装教程单机/伪分布式配置Hadoop2.6.0/Ubuntu14.04](http://www.powerxing.com/install-hadoop/)

- [Hadoop: Setting up a Single Node Cluster](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html)

- [史上最详细的Hadoop环境搭建](http://gitbook.cn/books/5954c9600326c7705af8a92a/index.html)

- [零基础学习 Hadoop 该如何下手？](https://www.zhihu.com/question/19795366)