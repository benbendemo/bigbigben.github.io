---
title: Go语言学习第一篇
date: 2019-11-17 16:42:25
categories: [编程]
tags: [Go]
---

想学习Go语言的想法由来已久，今天迈出实质性的一步。开始看*Miek Gieben*写的《学习Go语言》这本书。

本篇记录第一天学习过程中看到的有趣内容。

1、Go语言变量类型放在了变量名的后面，这一点确实很特别，以后再查明为什么要这样设计。

> Go 同其他语言不同的地方在于变量的类型在变量名的后面。不是:int a，而是 a int。 当定义了一个变量，它默认赋值为其类型的 null 值。这意味着，在 var a int后，a 的 值为 0。而 var s string，意味着 s 被赋值为零长度字符串，也就是 ""。 



2、iota字符

第一次在一门语言中看到iota这个词，并且它是一个内置的变量。作用是用来生成枚举值。

比如

const ( 

​	a= iota 

​	b= iota 

​	c= iota

)

使用fmt.Printf()语句打印时，a、b、c三个变量的值依次为0、1、2。

在英语词典里表示“微小、极小” 含义，也表示希腊字母“I”，该单词来自新约中的一句短语“*until heaven and earth pass away, not an iota, not a dot, will pass from the Law*.“。



3、Rune

Rune在英文词典里含义是“神秘的符号”，在Go语言中表示int32的别名，它的作用和用法后续补充。



4、Go 原生支持复数 

第一门看到原生支持复数的语言。

