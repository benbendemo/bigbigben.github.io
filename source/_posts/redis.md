---
title: Redis初体验
date: 2018-03-27 10:49:08
categories: 编程
tags: [Redis,缓存设计策略,DataFrame,Tushare]
---

前两天整理[使用Python爬虫下载电子书](/2018/03/21/python-crawler/)一文时，提过要给爬虫程序增加缓存处理。后面花了2天时间把Redis用法摸熟，然后在Mac上配置好了Redis环境。搭好环境后，发现那个爬虫程序其实非常简单，使出Redis有点大材小用，于是把Redis用在缓存股票数据上面，结果效果非常好。

关于Redis的缓存处理，我参考了陈皓老师[缓存更新的套路](https://coolshell.cn/articles/17416.html)文中提到的“Cache Aside Pattern”模式，来进行缓存设计，下面直接引用他博客里面的内容，非常受启发。

> - **失效**：应用程序先从cache取数据，没有得到，则从数据库中取数据，成功后，放到缓存中。
>
>
> - **命中**：应用程序从cache中取数据，取到后返回。
>
>
> - **更新**：先把数据存到数据库中，成功后，再让缓存失效。



![Fetch data from Redis](redis/Cache-Aside-Design-Pattern.png)

![Update data in db](redis/Updating-Data-within-Cache-Aside-Pattern.png)

<!--more-->

参考上面的两张图，在写数据库操作后面，增加写入Redis处理，代码块就不贴了；读取数据操作变成“先读Redis再读数据库”这种模式。

```python
def get_df_from_redis_then_db(table_name):  
    
    """
    get_df_from_redis_then_db:
    获取指定df数据。先读缓存，如果找到，直接返回缓存；如果缓存没有，
    从数据库读取数据，读取成功后，将数据库数据写入缓存。

    Parameters
    ----------
    table_name:表名
       
    Return
    ------
    dataframe:返回表名对应的df
    """
    
    try:      
        df_from_redis = get_df_from_redis(table_name)
    except:
        try:
            df_from_db = fetch_table_from_db(table_name)
        except Exception as e:
            print_msg(PGMname, 
                      fetch_table_from_db.func_name,
                      'error',
                      e)    
        else:
            print_msg(PGMname, 
                      fetch_table_from_db.func_name,
                      'success')
            try:
                set_df_to_redis(df_from_db, table_name)
            except Exception as e:
                print_msg(PGMname, 
                          set_df_to_redis.func_name,
                          'error',
                          e)    
            else:
                print_msg(PGMname, 
                          set_df_to_redis.func_name,
                          'success')
            return df_from_db
    else:
        print_msg(PGMname, 
                  get_df_from_redis_then_db.func_name,
                  'success')
        return df_from_redis
```

程序跑完后，查看写进Redis里面的记录，总共有91条。

```bash
JacksonShawnMBP:bin jacksonshawn$ ./redis-cli
127.0.0.1:6379> keys *
 1) "fund_holdings2016q1"
 2) "debtpaying_data2016q3"
 3) "report_data2017q3"
 4) "k_data"
 5) "growth_data2015"
 6) "profit_data2017q3"
 7) "fund_holdings2015q4"
 8) "operation_data2015q1"
 9) "debtpaying_data2015"
10) "operation_data2016q1"
11) "xsg_data"
12) "profit_data2016q4"
13) "report_data2015q4"
14) "growth_data2016q1"
15) "debtpaying_data2017"
16) "growth_data2017q4"
17) "today_all"
18) "debtpaying_data2017q2"
19) "growth_data2015q2"
20) "operation_data2016q2"
21) "report_data2016q3"
22) "st_classified"
23) "profit_data2017q2"
24) "profit_data2017q4"
25) "debtpaying_data2016"
26) "growth_data2017q3"
27) "profit_data2017q1"
28) "report_data2017"
29) "report_data2015q2"
30) "growth_data2017"
31) "report_data2017q4"
32) "debtpaying_data2017q3"
33) "debtpaying_data2015q4"
34) "operation_data2015q3"
35) "fund_holdings2016q4"
36) "debtpaying_data2017q4"
37) "report_data2015q1"
38) "fund_holdings2015q1"
39) "profit_data2015q3"
40) "new_stocks"
41) "profit_data2016"
42) "profit_data2017"
43) "day_all"
44) "report_data2015"
45) "growth_data2015q4"
46) "debtpaying_data2015q3"
47) "fund_holdings2017q2"
48) "operation_data2016q4"
49) "report_data2017q2"
50) "fund_holdings2015q2"
51) "growth_data2015q1"
52) "fund_holdings2016q2"
53) "growth_data2017q2"
54) "report_data2016q2"
55) "fund_holdings2017q1"
56) "fund_holdings2017q4"
57) "growth_data2016q3"
58) "fund_holdings2015q3"
59) "report_data2016"
60) "report_data2017q1"
61) "profit_data2016q1"
62) "profit_data2015q4"
63) "debtpaying_data2017q1"
64) "debtpaying_data2016q4"
65) "industry_classified"
66) "report_data2016q1"
67) "debtpaying_data2016q2"
68) "profit_data2015q1"
69) "profit_data2016q2"
70) "report_data2016q4"
71) "fund_holdings2017q3"
72) "debtpaying_data2015q1"
73) "debtpaying_data2015q2"
74) "operation_data2016q3"
75) "operation_data2015q2"
76) "Stockxsg"
77) "operation_data2016"
78) "fund_holdings2016q3"
79) "growth_data2016"
80) "debtpaying_data2016q1"
81) "stock_basics"
82) "profit_data2015"
83) "profit_data2015q2"
84) "growth_data2016q4"
85) "operation_data2015q4"
86) "growth_data2016q2"
87) "operation_data2015"
88) "growth_data2017q1"
89) "report_data2015q3"
90) "profit_data2016q3"
91) "growth_data2015q3"
```

查看Redis消耗的内存空间，目前总共102.76M。相比MBP 8G的内存，这点消耗还算好，如果哪天到了500M，可能就要采用LRU策略来对这些key-value数据进行处理了。

```shell
# Memory
used_memory:107750736
used_memory_human:102.76M
```

另外，使用Redis存储和读取DataFrame，在网上搜到如下方式，亲测有效。我采用的就是这种方式，至于有没有其它更好的方法，目前暂时没有时间去研究。

> **set:**
>
> redisConn.set(“key”, df.to_msgpack(compress=“zlib”))
>
> **get:**
>
> pd.read_msgpack(redisConn.get(“key”))
>

**参考资料：**
- [Mac系统安装Redis](https://blog.csdn.net/mrsyf/article/details/79624054)
- [Redis 配置](http://www.redis.net.cn/tutorial/3504.html)
- [mac下设置redis开机启动方法步骤](https://blog.csdn.net/zzhongcy/article/details/70884701)
- [andymccurdy/redis-py](https://github.com/andymccurdy/redis-py)
- [Welcome to redis-py’s documentation!](https://redis-py.readthedocs.io/en/latest/)

