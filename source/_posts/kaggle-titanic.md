---
title: Python数据分析之泰坦尼克沉船统计
date: 2018-03-15 11:16:52
categories: 编程
tags: [Python,Kaggle,泰坦尼克沉船]
---

![Ship](kaggle-titanic/anthony-metcalfe-559306-unsplash.jpg)

（Photo by [Anthony Metcalfe](https://unsplash.com/photos/vnz2XeO2Y-g?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/titanic-ship?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)）

前段时间看到[**jmportilla**](https://github.com/jmportilla/Udemy-notes)这篇[**Kaggle Titanic Challenge**](http://nbviewer.jupyter.org/github/jmportilla/Udemy-notes/blob/master/Intro%20to%20Data%20Projects%20-%20Titanic.ipynb)文章时，我把它实现了出来。

目前处于学习阶段，只能通过大量动手实践，才能提升水平，免不了模仿别人。看着人家做出来的东西感觉很轻松，自己亲自动手做一遍才知道其实不容易。原文中注释都使用英文来写，很多地方不大好理解，本文添加了很多翻译，可能更容易理解一些。亲自动手做一遍，能够体会到Python数据分析的乐趣，也是促使你继续学习下去的动力。

**数据准备**

需要到[Kaggle官网](https://www.kaggle.com/c/titanic/data)下载泰坦尼克沉船数据。（需翻墙）

**环境准备**

macOS 10.11.6 + Anaconda Navigator 1.2.1 + Python 2.7.12
主要用到的是Python数据分析那一套包，只要安装Anaconda，那些包就自动全部包括在内。其次是安装Seaborn进行图形输出处理。

**执行结果**

用Anaconda里面Jupyter Notebook一步步写完整个过程后，将得到的Titanic.ipynb文件使用Jupyter NBViewer进行转换，得到[泰坦尼克沉船数据](http://nbviewer.jupyter.org/github/benbendemo/learning-python/blob/master/titanic/Titanic.ipynb)这个链接，观看更加直观。