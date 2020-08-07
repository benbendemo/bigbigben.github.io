---
title: 离散型随机变量和连续型随机变量
draft: true
date: 2018-12-23 15:19:15
categories: 读书
tags: [离散型随机变量,连续型随机变量,CFA]
---

对于随机变量（Random variable）来说，它的每一个取值都对应一个概率，这些概率可以画出一个分布。

随机变量分为两类：

1、离散型随机变量

离散型随机变量取值可数

a、Discrete uniform distribution（离散均匀分布）

离散型随机变量取值可数并且每一个取值发生的概率是相等的。

比如：掷骰子，只会出现6个点数并且每个点数出现的概率都是1/6



b、Binomial distribution（二项式分布）

a1：Bernoulli random variable（贝努里随机变量）：只进行一次实验，这一次实验只存在两种结果，要么成功，要么不成功。这样的随机变量称为贝努里随机变量

P(Y=1)=p

P(Y=0)=1-p



a2：Binomial random variable（二项式随机变量）：进行N次实验，每次实验只有两种结果，要么成功，要么不成功。这样的随机变量称为二项式随机变量

N次实验X次成功的概率为：

nCx=P^x * (1-P)^(n-x)



a3：期望与方差

Bernoulli random variable期望等于P，方差等于P(1-P)

Binomial random variable期望等于nP，方差等于nP(1-P)



2、连续型随机变量

连续型随机变量取值不可数，可以有无限多个

连续型随机变量取值必须先规定好一个上限和下限，才可以计算一个取值区间的概率。

连续型随机变量的概率为图形的面积。使用Probablity density function（PDF）概率密度函数来进行计算。

连续型随机变量的概率是一个取值区间的概率，它是一个累积函数。

a、Continuous uniform distribution（连续均匀分布）

每一个取值出现的概率是相等的；随机变量的取值区间有一个上限和一个下限