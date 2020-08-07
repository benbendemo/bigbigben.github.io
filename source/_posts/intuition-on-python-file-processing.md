---
title: 直觉与Python CSV文件处理
date: 2019-10-07 19:56:20
categories: [编程]
tags: [CSV,Python,生成器,迭代器]
---

![](intuition-on-python-file-processing/tianyi-ma-WiONHd_zYI4-unsplash.jpg)

(Photo by [Tianyi Ma](https://unsplash.com/@tma?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/computer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText))

客户需要用Python解决这样一个问题，读入一个3.4G大小的CSV文件，除首行记录外，将剩余行数记录中的K列，按照业务规则进行内容替换，处理完后生成一个新的CSV文件出来。

沟通了一番需求后，了解到CSV文件编码格式是"ISO 8859-1"；文件记录数大概有400多万行（拿到文件后我没敢用Excel打开看，怕卡死）；首行是Header Cloumn，记录每一行数据的Layout；除首行外每一行记录都使用Tab键区分。

下面是我的分析思路：

CSV文件很大，不可能一次性读入内容，只能分块来读；首行是Header Column，读出来后需要单独标记不做处理；剩下400多万行中有一些需要替换（替换可以简单理解为将第K列的值由"Tencent"修改为"Alibaba"），具体哪些行，事先是不知道的；那最好就读一行处理一行。

<!--more-->

最先进入脑海的是使用Pandas包来处理，但客户要求用内置的CSV模块来做。

如果使用文件对象的read方法，指定一个固定的BlockSize，每次读入一块固定大小的内容进行处理，这样看起来可行，但这个ChunkSize不知道该指定多少，每行记录的栏位数都相同，但具体每行记录，可能有大有小，指定ChunkSize，如果读进来的内容是10.5行或20.3行这样的大小，那非整数部分的0.5行或0.3行，会不会出现异常呢？无法评估这个风险，因此放弃了指定ChunkSize方案。

如果用文件对象的readline方法，读一行处理一行，这样应该可行，以前没做过类似的需求，readline方案要多长时间处理完3.4G大小的文件，这个时间预估不到。

最后想到使用Python生成器来做，它可以创建一个generator对象，因为generator本质是一个迭代器（实现了Next方法的可迭代对象就叫Iterator），它可以表示一个数据流序列，每次读入一行处理一行然后写入一行；非常符合需要。

使用一个Sample文件测试成功后，直接读入3.4G大小的CSV文件进行转换，先别看下面，你们猜一下要多长时间处理完？

第一次执行完，看到结果，说实话让我有点惊讶，一度有点怀疑是不是代码哪里出问题导致程序异常终止了。因为我的直觉是：处理这么大的文件，CPU至少需要计算半个小时吧。重新跑一遍后，发现还是这个时间，有点喜出望外，generator的处理效率真得非常强悍。



废话不多说了，大家看代码吧

```python
#! /usr/bin/env python
# -*- coding: utf-8 -*-

import sys
import csv
import datetime
from functools import wraps

filename = 'old.csv'
filename_new = 'new.csv'
ENCODIND_FMT = 'ISO-8859-1'

# Check Python verison
PY3 = (sys.version_info[0] >= 3)


def decorator(func):
    """
    Statistic execute time for each function
    """
    @wraps(func)
    def wrapper(*args, **kwargs):

        t1 = datetime.datetime.now()
        res = func(*args, **kwargs)
        t2 = datetime.datetime.now()
        print("Function %s takes %s Time" % (func.__name__, (t2-t1)))
        return res
    return wrapper


def read_csv_reader_yield(filename):
    if PY3:
        with open(filename, 'r', encoding=ENCODIND_FMT, newline='') as fp:
            csv_content = csv.reader(fp, delimiter='\t')
            yield next(csv_content)
            for i in csv_content:
                yield i
    else:
        with open(filename, 'rb') as fp:
            csv_content = csv.reader(fp, delimiter='\t')
            yield next(csv_content)
            for i in csv_content:
                yield i


def change_colx_2_coly(content_one):
    """
    Do nothing
    """
    return content_one


def write_to_csv_yield(header_column, csv_generator, filename_new):
    if PY3:
        with open(filename_new, 'w', encoding=ENCODIND_FMT, newline='') as fp:
            csv_content = csv.writer(fp, delimiter='\t')
            csv_content.writerow(header_column)
            for x in csv_generator:
                csv_content.writerow(change_colx_2_coly(x))
    else:
        with open(filename_new, 'wb') as fp:
            csv_content = csv.writer(fp, delimiter='\t')
            csv_content.writerow(header_column)
            for x in csv_generator:
                csv_content.writerow(change_colx_2_coly(x))


@decorator
def generate_new_csv_file(filename, filename_new):

    try:
        csv_generator = read_csv_reader_yield(filename)
    except Exception as e:
        print("csvfile generator error", type(e), e)
    else:
        header_column = next(csv_generator)
        print("csvfile generator get header success")

    try:
        write_to_csv_yield(header_column, csv_generator, filename_new)
    except Exception as e:
        print("write new csv file error", type(e), e)
    else:
        print("write new csv file success")

if __name__ == '__main__':
  
    generate_new_csv_file(filename, filename_new)
```



上面程序中change_colx_2_coly函数负责将K列中记录进行替换，这里为了简便，我将其具体功能替去掉了，不对文件记录做任何处理，只是简单地对读入记录做一个Return，我们的目的是测试generator处理文件的执行效率。

Python2执行结果：

```Python
csvfile generator get header success
write new csv file success
Function generate_new_csv_file takes 0:02:56.134797 Time
[Finished in 176.2s]
```

Python3执行结果：

```python
csvfile generator get header success
write new csv file success
Function generate_new_csv_file takes 0:03:04.136872 Time
[Finished in 184.6s]
```



看到了吧，3分钟多一点就处理完了；加上change_colx_2_coly函数功能的代码，处理完这个3.4G大小的文件，也是3分钟多一点，几乎和这个没差别。

我惊喜地跟同事们宣布这个结果时，他们都表示不可思议。

凭直觉，我以为至少要30分钟；万万没想到，不到3分钟左右就把3.4G大小的文本文件处理完了。

这说明一个事情，直觉是一个多么不靠谱的事情。

如果以后有人问我类似问题，我会根据文件大小跟他预估时间，当他按照我说的方法做完之后，发现我预估的时间"很准"，会跟身边其他人说"他的直觉真准"。

呵呵，其实哪里是直觉，只不过是经验换来的罢了。



附：

> generator本质是一个迭代器（Python中实现了Next方法的可迭代对象（Iterable）就叫Iterator），Iterator对象表示的是一个数据流，可以理解为它就是一个有序序列，通常你无法提前预知这个有序序列的长度，但可以通过next()函数源源不断地计算序列中每一条数据直到没有数据时返回StopIteration报错。相比于List、Dict、Set、Tuple、Str这些没有实现next()方法的可迭代对象（Iterable）来说，Iterator这种计算形式又被称为惰性计算，因为它不需要将要计算的内容全部读入内存，一次性拿到所有数据后再做计算。



**参考资料**

- [UnicodeDecodeError, invalid continuation byte](https://stackoverflow.com/questions/5552555/unicodedecodeerror-invalid-continuation-byte)
- [Python 3 CSV file giving UnicodeDecodeError: 'utf-8' codec can't decode byte error when I print](https://stackoverflow.com/questions/21504319/python-3-csv-file-giving-unicodedecodeerror-utf-8-codec-cant-decode-byte-err)
- [Python: Converting from ISO-8859-1/latin1 to UTF-8](https://stackoverflow.com/questions/6539881/python-converting-from-iso-8859-1-latin1-to-utf-8)
-  [Encoding characters with ISO 8859-1 in Python](https://stackoverflow.com/questions/32123872/encoding-characters-with-iso-8859-1-in-python)
-  [python读取CSV文件](https://www.cnblogs.com/liujinhong/p/5937527.html)