---
title: Docker初体验
date: 2019-10-06 08:34:47
categories: [编程]
tags: [Docker]
---

工作需要，国庆节这几天宅在家里学习Docker的用法。不用不知道，一用吓一跳，Docker的简便和轻巧已经成为业界的标配，用它来搭建开发环境和部署生产已经成为很多项目的首选。

IT产业的变革和更新速度非常快，一般过个一两年，市场就会涌现出一门新的语言、开发平台或技术框架，用来解决之前解决不了的问题或提升之前解决问题的效率。

比如机器学习，解决之前解决不了的非确定性求值问题，多解释一句，之前的软件都是给定参数求出确定的值，通常这个确定值是唯一值；机器学习的Schema是给定参数，计算机给你求出一组近似值，标出这组取值中每一个取值接近正确值的概率，换言之，它求解的是一个非确定值；

而今天要说的Docker则代表后者，将搭建开发环境（严格来说，我觉得是测试环境，开发主要还是在程序员自己的宿主机里完成）和生产部署从软件开发流程中抽离出来，将其模块化，极大提高了生产效率。

淡扯完了，下面说一下我自己的Docker学习心得吧。

刚开始，我觉得还是先看中文博客了解一下Docker是什么，比如阮一峰的[Docker 入门教程](http://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)，刚开始就去吭Docker的英文Documentation，我担心对很多人来说，不一定能看得懂，更别谈理解了。

简单来说，可以将Docker理解为一个轻便型的虚拟机，以前你想在MacOS或Windows里装一个Ubuntu或Centos系统，你需要先安装虚拟机，再使用它们的ISO镜像文件去虚拟一个操作系统出来；现在不用这么麻烦，Docker安装好之后，从Docker官网提供的[Docker Hub](https://www.docker.com/products/docker-hub)里Pull一个你想要的Image（镜像文件，比如Ubuntu18.04）下来，使用Docker命令Run一下，就可以得到一个Ubuntu18.04操作系统，Docker里称这个系统为Container（容器），Container就是Image的实例，你可以从一个Image出发，生成多个Container，比如生成第一个Container，你在里面安装了Git，然后将这个Container的名称命名为"ubuntu-git"；生成第二个Container在里面安装了Redis，将其命名为"ubuntu-redis"；生成第三个Container在里面安装了Mysql，将其命名为"ubuntu-mysql"，它们分别是三个独立的个体，互不干扰、互不影响；只要不将这些Container删除，这些Container会一直存在，以后每次只需要Start这些Container就可以得到Ubuntu18.04+Git、Ubuntu18.04+Redis、Ubuntu18.04+Mysql开发环境；

如果将这些Container删除，下次则需要重新Run这些Image，然后你得重新在Container里安装你需要的应用；如果想将Ubuntu18.04+Git环境保留下来，使用Commit命令，将Container制作成一个新的Image（不同于最开始的Image），后面基于这个Image的到的Container就都会自带Git应用；

如果，你想将自己制作的Image分享给其他同事使用；需要将这个Image Push到Docker Hub里面，让其他同事从里面Pull这个镜像，这样其他人就得到了这个镜像。

如果以前没接触过，第一次看，可能很难看懂这些。



Docker常用命令：



**参考资料**

- [Docker 入门教程](http://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)
- [被忽略的后端开发神器 — Docker](https://juejin.im/post/5c2382b55188253847208748)