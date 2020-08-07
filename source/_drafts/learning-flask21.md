---
title: Flask学习
date: 2020-04-03 11:38:05
categories: [编程]
tags: [Python,Flask]
---

Flask-script是Flask团队官方出品的一个拓展库，用于添加Flask脚本。



路由管理：

存在循环引用问题。

1. 可以使用赖加载方式，以函数调用的方式进行加载
2. 使用蓝图
   1. 按照flask-blueprint
   2. 进行路由规划



### 数据库：

web开发中大多数情况都是关系型数据库

ORM

1. flask-sqlalchemy
2. 原生SQL代码利用率低，很难做到代码复用；复杂查询条件的SQL语句会非常长；修改SQL语句需要了解业务逻辑时直接写SQL容易忽视SQL语法问题

Flask数据查询：

1. 获取单个对象（使用first、get、get_or_404）

2. BaseQuery对象有这些筛选方法（filter、filter_by、offset、limit、order_by、get、first、pagination）

   1. offset和limit一起使用时，不管谁在前谁在后，默认都是先执行offset
   2. order_by调用必须在offset和limit之前
   3. 

3. 获取结果集

   1. all方法返回结果是一个列表，它是特例；在flask_sqlalchemy中，all方法只能放在最后

   2. filter方法返回结果是一个BaseQuery对象，输出值显示的是对应的SQL语句

   3. filter使用方法：

      1. 类名.属性名.魔术方法（临界值）
      2. 类名.属性名-操作运算符-临界值（运算符有contains、startswith、endswith、in_、like、\_\_gt__、\_\_ge\_\_、\_\_lt\_\_、\_\_le\_\_）

   4. 级联数据使用：

      1. 手动获取，通过数据库表键值进行关联查询
      2. 使用relationship进行级联查询，反向引用

   5. filter_by使用方法：

      1. 属性名-操作运算符-临界值（运算符有>、<、=）
      2. filter_by一般用于级联数据查询（用于模型之间数据存在关联关系的查询）

      

   

### Flask Route机制

Flask框架比Django后出来，砍掉了很多Django框架中冗余的东西，增加了很多新特性解决了Django框架的痛点。比如一个视图函数中，要控制用户在浏览器里输入的id是一个数字，Django在视图函数中通常使用正则表达式进行过滤，如

```python
urlpatterns = [
    path('', views.index),
    re_path('^users/(\d+)/$', views.users),
```

Flask则不需要这样，只需要在视图函数中使用converter类型的转换器来检查即可。

如

```python
@blue.route('/users/<int:id>/')
def users(id):
    print('id:', id, type(id))
    return 'users id valid'
```



为什么可以像上面这样呢，是因为Flask是基于Werkzurg的一个框架，Flask的Route机制正是在Werkzurg基础上进行了一层封装得到的，Werkzurg中这种机制叫做Converter ，可以通过特定的语法，将 URL中特定部分转化成对应的Python变量。

Flask中常用的converter类型有：

用法：

​		<converter:variable_name>

类型：

​		string：从url中接收斜线（"/"）之前的部分作为参数（默认使用string类型）

​		int：接收整型

​		float：接收浮点型

​		path：接收全部url作为参数，可以接收斜线（"/"）后面的部分

​		uuid：只接收uuid类型的字符串

​		any：限定url中接收参数的值

```python
@blue.route('/getuser/<int:id>/')
def getuser(id):
    print('id:', id, type(id))
    return 'getuser success'

@blue.route('/getinfo/<string:token>/')
def getinfo(token):
    print('token:', token, type(token))
    return 'getinfo success'

@blue.route('/getinfo1/<path:token>/')
def getinfo1(token):
    print('token:', token, type(token))
    return 'getinfo1 success'

@blue.route('/getuuid/<uuid:uuid>/')
def getuuid(uuid):
    print('uuid:', uuid, type(uuid))
    return 'getuuid success'

@blue.route('/getany/<any("1","11","111"):num>/')
def getany(num):
    print('num:', num, type(num))
    return 'getany success'
```



可以将上面这几个路由映射到同一个视图函数上面：

```python
@blue.route('/getuser/<int:var>/')
@blue.route('/getinfo/<string:var>/')
@blue.route('/getinfo1/<path:ar>/')
@blue.route('/getuuid/<uuid:var>/')
@blue.route('/getany/<any("1","11","111"):var>/')
def get(var):
    print("var:", var, type(var))
    return 'get success'
```

Flask视图函数中默认支持GET、HEAD、OPTIONS，其余请求类型不支持，需要开发人员手动注册。



### Flask四大对象

Request、Session、g、Config

Flask里面一次请求进来，会创建一个请求上下文对象，我们称之为request_context，它包含request、flash、session等这些初始化参数，然后请求上下文对象会被Flask创建的协程或线程（Flask会先检查是否可以使用协程，如果有协程，优先使用协程，没有则使用线程）先压入栈空间，请求处理完后，将请求上下文对象（这时请求上下文对象里的数据内容已经发送了变化）从栈空间里出栈。我们称这为一次请求的生命周期，g对象的作用范围就在这一次请求的生命周期内。



通俗点理解，g对象无法跨路由传递参数，就在于不相同的两个路由是两个独立的请求，A路由里的g对象数据，无法在B路由里进行使用。



如果想实现跨路由传递参数，就需要使用session，session可以做到跨路由传递参数是因为它将每一次请求的记录保存到cookie里面做了持久化处理。session实现机制简单来说，是这样一个过程：

1. request请求进来后，Flask会先去获取cookie的值
2. 对cookie的值进行base64解码，然后使用secret_key解密，赋值给session进行使用（加密解密模块是基于itsdangerous模块实现）
3. 请求处理完之后，对session数据加密，然后使用base64编码
4. 设置cookie



session处理流程中与request一样，同样存在入栈和出栈操作。



g对象在一次请求过程中，用于在不同视图函数间传递参数。



config对象和Flask的app.config模块功能一致，可以从环境变量里获取参数，它可以直接在模板中被引用，在视图函数中要使用，需要从current_app.config获取。



### Flask离线脚本

离线脚本是相对Flask应用请求来说的一个概念，比如你启动了一个Flask应用程序。有视图、有模型，模型连接着后台数据库flask_demo，在浏览器里输入一个路由地址，给数据库flask_demo里User用户表添加了一名用户。

这整个操作过程，都伴随在Flask的request_context（请求上下文）里完成。

如果你不启动这个Flask应用，如何给数据库flask_demo里User用户表插入一条记录呢？

这就是离线脚本可以干的事情。

这个时候没有Flask的在线应用环境可以使用，可以手工创建一个app_context（应用上下文），然后手动操作入栈和出栈，更新数据库表的记录。

```
# 获取到生成app
app = create_app().app
# 创建app_ctx
app_ctx = app.app_context()
# app_ctx 入栈
app_ctx.push()
# 对数据库进行操作
db.session.add(Users(name="大萨达所1"))
# 提交
db.session.commit()
app_ctx.pop()
```





### Flask Request对象

服务器在接收到客户端请求后，会自动创建request对象，request对象由Flask框架创建，可以给它添加属性，但不能修改或删除它的属性。

request对象具有如下属性：

url：完整url请求地址

base_url：去掉GET参数的url地址

host_url：只有主机和端口号的url地址

host：只有域名和端口号

path：路由中的路径

method：请求方法

remote_addr：客户端IP地址

args：GET请求参数（例如：接收/xxx/?id=3&username=jack中id和username这两个变量的值），但并不是GET请求专属，所有请求上传参数都可以从args属性中获取

form：POST请求参数（可以使用Postman进行模拟，除了接收POST请求上传的参数，form也可以接收PUT、PATCH、DELETE请求上传的参数；Django可以接收GET和POST请求上传的参数，但没法直接接收PUT、PATCH、DELETE请求上传的参数，只能间接从request.body中提取，这一点没有Flask方便）

（args和form都是ImmutableMultiDict类型，这个类型是Werkzug类对Python字典类型的封装）

files：文件上传

headers：请求头

cookies：请求中的cookie



### Flask Response对象

Response对象由开发人员构造，包含服务器返回给客户端的数据，Flask返回的Response通常可以是这4种类型，string，response对象，tuple，WSGI instance实例。

Response返回的4种形式：

1. 直接构建Response对象
2. 通过make_response方法返回数据，make_response包含返回的数据内容data以及返回状态码code
3. 直接返回字符串（也可以使用render_template将模板转换为html字符串）
4. 返回模板（本质和方法3一样）
5. 重定向

终止处理：

1. abort

   本质是一个Exception

2. errorhandler

   使用errorhandler进行异常捕获，比如捕获404、401，然后重定向到其它视图，让程序报错变得友好



### Session处理

Session的目的是实现跨请求共享数据，它出现的原因是：

1. web开发中HTTP请求都是"短连接"，尽管现在有keep-alive，但当前请求无法识别上一次请求
2. HTTP请求是无状态的；
3. HTTP请求从客户端发起Request到服务端返回Response就处理结束了；



常用Session的处理方法有：Cookie、Session、Token

Cookie客户端会话技术，数据存储在客户端，使用key-value结构存储；Flask框架背后的Cookie机制默认对中文字符进行了处理，可以保存用户设置的中文信息。



Flask框架的Session数据默认并不存储在服务器端，它是将Session数据进行序列化、Base64、zlib压缩、哈希等处理之后，保存到客户端的Cookie里面，Flask框架的Session技术允许你查看服务器端存储在客户端的Session数据，但不允许你修改。



Flask的session有效期默认是31天，Django里session有效期默认是15天。



Flask可以借助flask_session插件将Session数据存储在服务器端，将Session数据对应的key存储在cookie里面。

flask_session可以将Session数据存储在Redis、filesystem、memcached、sqlalchemy、mongodb等各类服务器终端中。如果是存储到Redis里面，通过RedisSessionInterface这个类的save_session方法，可以将数据进行pickler序列化，进行签名然后，存储在Redis里面。



### Flask Restful API

RESTful是一种软件架构风格，实现前后端分离的一种方式，前端通过异步请求的方式与后端交互，后端只负责数据展示。RESTful路径是名词复数，不能出现动词。每种URI代表一种资源，通过HTTP请求谓词来实现资源转换，如GET、POST、PUT、PATCH、DELETE。



RESTful核心在于将后端数据序列化。



默认返回字典，可以直接被序列化；如果返回内容中包含对象，需要先进行序列化处理。

常用序列化工具：

1. marshal函数
2. marshal_with装饰器
3. 序列化之前需要先定义序列化模板，模板使用字典格式，允许嵌套，value是fields.XXX类型，数据允许是任意格式
4. 如果数据与格式完全对应，数据就是预期结果；如果格式比数据字段多，依然可以正常返回，返回它的默认值；如果格式比数据中的字段少，依然可以正常返回，少的字段不会显示（以格式的模板为主）
5. 想要返回什么的格式以模板定义的内容为准
6. 格式中字段名需要与数据中字段名一致



### Flask模板

Flask模板使用Jinja2，与Django模板技术非常相像。使用方法都是先在父模板里定义block块，再在子模板里使用extends继承父模板，将要渲染的内容填充到子模板页面里面。

Flask在Django基础上增加了macro这个功能。



### Flask Bootstrap

html_attribs：给整个html添加属性



### Flask钩子对象

钩子函数是面向切面编程的一种方式，它可以动态介入请求流程。例如：before_request、after_request，和Django中中间件的作用非常类似。

以Django举例，一次web请求操作的流程是：

Client -> process_request（列表）

对process_request里每一项逐一进行处理

process_request -> url路由

url路由 -> process_view（列表） 

对process_view里每一项逐一进行处理

process_view -> views

views -> models

models -> views渲染模板

views -> response

response -> process_response（列表）

对process_response里每一项逐一进行处理



### 权限设计

- 两种设计方式

  - 单个字段实现所有权限

    - Linux权限设计
      - 使用二进制实现
        - 1 - 读权限
        - 2 - 写权限
        - 4 - 修改权限
      - 每一种权限使用一个不重复的二进制数字确定
        - 2的N次幂
        - 优点是各种权限互不干扰，一张表可以解决权限控制问题

    - 包含模式权限
      - 数值越大，权限越高，包含数值较小的权限

  - 多表实现权限设计
    - 用户表
    - 权限表
    - 用户权限表（用户表与权限表多对多关系）



### 特殊登录

- 扫码登录
  - 前置条件
    - 有一个已登录帐号
  - 使用已登录帐号中的token来访问要登录的接口
  - 扫码扫出来的就是登录接口
- 跨应用登录
  - 前置条件：有一个已登录帐号
- 第三方应用登录
- 常规登录
  - 使用用户名和密码登录，登录后服务器会生成一个token，后续可以直接通过token进行登录



### 并发

1. 并行改串行
2. 加锁
   1. 被关锁（对数据库加锁）
   2. 乐观锁（一种设计思想，假设读取时没有别人同时来读，一个线程读取时默认没有其它线程来读，操作完之后再读一遍，如果和预期一样，则操作成功；如果不一样，则说明有并发操作存在，回滚交易）
   3. 乐观锁的使用更方便，极限并发时两种方式可能仍然存在问题















