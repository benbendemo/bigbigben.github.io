---
title: bdfy api
date: 2019-04-14 16:51:23
categories: 编程
tags: [Python,百度翻译,HTTPResponse]
---

百度翻译API给的demo程序里有一个坑，如果不是非常熟悉这一块内容，很可能遇上和我同样的问题。
人家工程师友情提示了"response是HTTPResponse对象"。

我在尝试将百度翻译API返回的response.read()解析为JSON类型时，试了很多种方法都不成功。


(http://api.fanyi.baidu.com/api/trans/product/apidoc)

