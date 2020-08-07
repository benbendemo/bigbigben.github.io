---
title: Python元类的一个疑惑
date: 2018-03-14 15:28:45
categories: 编程
tags: [Python,元类,metaclass]
---

![Magic place](python-metaclass/cosmic-timetraveler-19924-unsplash.jpg)

（Photo by [Cosmic Timetraveler](https://unsplash.com/photos/pYyOZ8q7AII?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/magic?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)）

Python高级语法里面的元类，之前一直没有看懂，今天重新看讲解元类的文章。这篇[深刻理解Python中的元类(metaclass)](http://python.jobbole.com/21351/)翻译Stack Overflow上面[大神e-satis对元类的讲解](https://stackoverflow.com/questions/100003/what-is-a-metaclass-in-python)，讲解得十分清楚，看完后我对元类的理解豁然开朗。

下面是结合代码（基于Python 2.7环境）进行实操。

```python
class SayMetaClass(type):
    '''
    元类作用主要是创建类时动态修改类的属性，这里我们在SayMetaClass里重写__new__函数，当创建这个类的
    实例时候，会将"say_XXX"这种形式的属性定义为方法
    '''
	def __new__(cls, name, bases, attrs):
		attrs['say_' + name] = lambda self,value,saying=name: (saying+','+value+'!')
		return type.__new__(cls, name, bases, attrs)

class Hello(object):
    # 指示使用SayMetaClass来定制类，有了这一句后元类魔法就会生效
    __metaclass__ = SayMetaClass
    pass
```

编译后，查看Hello这个类的\__dict__属性，可以看到有say_Hello方法，它是一个function，解释器幕后创建完Hello类之后，就生成了say_Hello方法。

```python
In [74]: Hello.__dict__
Out[74]: 
dict_proxy({'__dict__': <attribute '__dict__' of 'Hello' objects>,
            '__doc__': None,
            '__metaclass__': __main__.SayMetaClass,
            '__module__': '__main__',
            '__weakref__': <attribute '__weakref__' of 'Hello' objects>,
            'say_Hello': <function __main__.<lambda>>})
```

接下来，我们new一个实例对象hello，然后调用say_Hello方法（注意：一定要是say_Hello，使用say_hello或别的格式都会报错，因为和SayMetaClass里面\__new__函数定义的格式不匹配），传入参数'jackson'，成功打印预期结果。

```python
In [64]: hello = Hello()
In [65]: hello.say_Hello('jackson')
Out[65]: 'Hello,jackson!'
```

既然这样，我使用type元类来新建一个Bye类，创建实例bye，然后执行bye.say_Bye('andrew')方法应该也是可以成功的。查看Bye.\__dict__，结果发现Bye类没有这个say_Bye这个方法。

```python
In [76]: Bye = type('Bye',(object,),{'__metaclass__':SayMetaClass})

In [77]: print Bye
<class '__main__.Bye'>

In [78]: Bye.__dict__
Out[78]: 
dict_proxy({'__dict__': <attribute '__dict__' of 'Bye' objects>,
            '__doc__': None,
            '__metaclass__': __main__.SayMetaClass,
            '__module__': '__main__',
            '__weakref__': <attribute '__weakref__' of 'Bye' objects>})
```

我们new一个实例对象bye，调用say_Bye方法，提示没有这个属性。

```python
In [79]: bye = Bye()

In [80]: bye.say_Bye('andrew')
Traceback (most recent call last):

  File "<ipython-input-80-07a0df2d8098>", line 1, in <module>
    bye.say_Bye('andrew')

AttributeError: 'Bye' object has no attribute 'say_Bye'
```

我没想明白这里面的原因。谁知道答案？请指点一下。

**参考资料：**

- [深刻理解Python中的元类(metaclass)](http://python.jobbole.com/21351/)

- [What are metaclasses in Python?](https://stackoverflow.com/questions/100003/what-are-metaclasses-in-python)

- [使用元类](https://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/001386820064557c69858840b4c48d2b8411bc2ea9099ba000#0)
