---
title: WSGI理解
date: 2018-02-14 18:00:47
categories: 编程
tags: [Python,WSGI]
---



今天学习WSGI资料，很难理解。



```python
def reflect_x(*args, **kwargs):
    
    """
    reflect_x:使用反射机制定义调用函数。
    根据输入字符串，在指定模块里查找对应函数，执行函数，返回结果
    
    Parameters
    ----------
    args:第一个必须是函数名，后面才是函数调用参数（必输）
    kwargs:函数调用参数（关键字参数，可选）
       
    Return
    ------
    DataFrame，返回对应的df
    
    Reference
    ---------
    https://stackoverflow.com/questions/2724260/
    why-does-pythons-import-require-fromlist
    """
    
    f_name = args[0]
    print "f_name:",type(f_name),f_name
    
    # fromlist参数作用是不仅仅只在当前包查找，还可以在子目录里查找。
    f_folder = __import__("get.get", fromlist = True)
    print "f_folder:",type(f_folder),f_folder
    
    f_func = getattr(f_folder, f_name, None)
    print "f_func:",type(f_func),f_func
    
    if f_func:
        # 实际执行时，只能从args第二个参数开始传入
        df = f_func(*args[1:], **kwargs)
        return df
    else:
        print "f_func not found"
```

参考资料：

[python wsgi 简介](http://python.jobbole.com/87361/)

[python的 WSGI 简介](http://blog.csdn.net/yangz_xx/article/details/37508909)

[PEP 3333 -- Python Web Server Gateway Interface v1.0.1](https://www.python.org/dev/peps/pep-3333/)

[Getting Started with WSGI](http://lucumr.pocoo.org/2007/5/21/getting-started-with-wsgi/)

[Python Web开发最难懂的WSGI协议，到底包含哪些内容？](http://python.jobbole.com/88653/?utm_source=blog.jobbole.com&utm_medium=relatedPosts)

[WSGI接口](https://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/001386832689740b04430a98f614b6da89da2157ea3efe2000)

[wsgiref 源码解析](http://python.jobbole.com/87390/)