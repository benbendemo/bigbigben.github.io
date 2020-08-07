---
title: Python线程的join方法
date: 2019-07-13 18:46:51
categories: [编程]
tags: [Python,Threading,线程join方法]
---

一直没有搞明白Python线程里面的join方法。使用和不使用有啥区别，这篇文章请详见具体分析结果。

```python
import threading


def Thread_1(num):
    print('Thread_1 start')
    for i in range(num):
        print('喵')
    print('Thread_1 end')


def Thread_2(num):
    print('Thread_2 start')
    for i in range(num):
        print('呱')
    print('Thread_2 end')


def Thread_3(num):
    print('Thread_3 start')
    for i in range(num):
        time.sleep(3)
        print('汪')
    print('Thread_3 end')


def main1():

    # 对子线程来说，使用join方法后，主线程会等待子线程全部执行完后，再执行主线程内容，然后退出

    thread_list = []

    thread_1 = threading.Thread(target=Thread_1, args=(3,))
    thread_2 = threading.Thread(target=Thread_2, args=(3,))
    thread_list.append(thread_1)
    thread_list.append(thread_2)

    for i in thread_list:
        i.start()
    for i in thread_list:
        i.join()

    print('全搞定了.......')


def main2():

    # 对子线程来说，不使用join方法，主线程会和子线程抢占执行顺序，会等子线程全部执行完后再退出，
    # 但主线程不保证在子线程全部执行完之后再执行

    thread_list = []

    thread_1 = threading.Thread(target=Thread_1, args=(3,))
    thread_2 = threading.Thread(target=Thread_2, args=(3,))
    thread_list.append(thread_1)
    thread_list.append(thread_2)

    for i in thread_list:
        i.start()

    print('全搞定了.......')


def main3():

    # 对守护线程来说，使用join方法，主线程会等到守护线程全部执行完后，再执行主线程的内容，然后再退出
    thread_list = []

    thread_1 = threading.Thread(target=Thread_1, args=(3,))
    thread_2 = threading.Thread(target=Thread_2, args=(3,))
    thread_1.daemon = True
    thread_2.daemon = True

    thread_list.append(thread_1)
    thread_list.append(thread_2)

    for i in thread_list:
        i.start()
    for i in thread_list:
        i.join()

    print('全搞定了.......')


def main4():

    # 对守护线程来说，不使用join方法，主线程只保证主线程的内容被执行完然后退出
    # 主线程内容执行完后，守护线程内容还可以继续执行，但无法保证守护线程全部被执行完

    thread_list = []

    thread_1 = threading.Thread(target=Thread_1, args=(3,))
    thread_2 = threading.Thread(target=Thread_2, args=(3,))
    thread_1.daemon = True
    thread_2.daemon = True

    thread_list.append(thread_1)
    thread_list.append(thread_2)

    for i in thread_list:
        i.start()

    print('全搞定了.......')


def main5():

    # join参数设置主线程等待时间，子线程执行超过等待时间，主线程执行完自己内容后等子线程执行完再退出

    thread_1 = threading.Thread(target=Thread_3, args=(3,))
    thread_1.start()
    thread_1.join(10)

    print('全搞定了.......')


def main6():

    # join参数设置主线程等待时间，守护线程执行超过主线程设置的等待时间，主线程不等守护线程执行完直接退出

    thread_1 = threading.Thread(target=Thread_3, args=(3,))
    thread_1.daemon = True
    thread_1.start()
    thread_1.join(10)

    print('全搞定了.......')


def main7():

    # join参数不设置主线程等待时间，主线程等子线程全部执行完后，再执行自己的内容，然后退出

    thread_1 = threading.Thread(target=Thread_3, args=(3,))
    thread_1.start()
    thread_1.join()

    print('全搞定了.......')

if __name__ == '__main__':

    # main1函数，子线程使用join方法
    # main1()

    # main2函数，子线程不使用join方法
    # main2()

    # main3函数，守护线程使用join方法
    # main3()

    # main4函数，守护线程不使用join方法
    # main4()

    # main5函数，子线程join参数设置时间
    # main5()

    # main6函数，守护线程join参数设置时间
    main6()

    # main7函数，子线程join参数不设置时间
    # main7()
```

结论：

1.Python子线程被创建出来后，主线程一定会等子线程全部执行完后再退出;子线程使用join和不使用join的区别是使用join可以保证子线程一定在主线程执行前被执行完；
不使用join，则主线程会和子线程抢占cpu时间，会出现主线程在子线程前面先执行。具体见main1和main2函数的对比。

2.Python里面守护线程的地位比子线程低。如果创建守护线程，守护线程使用join方法，则主线程执行结束之前，守护线程全部会被执行完；守护线程如果不使用join方法，主线程
执行完后会直接退出，不保证守护线程被执行完。

3.对于第2点里守护线程不使用join方法的情况，主线程执行完后，守护线程可能会被执行完，也可能不会被执行完，结果无法保障。

4.join方法的作用是阻塞，阻塞主线程等待子线程执行结束。join方法有一个timeout参数，被设置的时间到期后，如果子线程还没结束，主线程会先执行自己的内容，然后再接着
去执行子线程的内容；一定会等子线程全部执行完再退出。见main5函数。

5.对于非守护线程(也就是子线程)来说，join方法的timeout参数无法控制时间到了之后让子线程不中断执行。比如main5函数，target函数会循环3次，每次sleep3秒，一共是9秒；
join参数设置为2时，主线程只等待子线程2秒钟，但target函数执行一次就需要sleep 3秒钟，这时主线程会先执行，执行完后再将target函数执行3次。
join参数设置为10时，主线程等待子线程10秒钟，子线程足够将target函数执行3次，子线程执行完后，主线程继续执行完，然后再退出

6.非守护线程(子线程)join参数不设置timeout参数，结果和普通的调用子线程调用一样。对比main5和main7执行结果。

7.对于守护线程，join方法的timeout参数会控制子线程的执行时间，超过了等待时间后，主线程执行完自己内容直接退出，不保证守护线程的内容被执行。见main6函数，timeout参数
设置为7和10时候，请对比执行结果；timeout参数设置为9时，请留意线程执行异常报错信息。



**参考资料**

- [Python线程中join()函数的理解](https://blog.csdn.net/Ha_hha/article/details/79393041)](https://blog.csdn.net/Jamesjjjjj/article/details/82802987)
- [A gentle introduction to multithreading](https://www.internalpointers.com/post/gentle-introduction-multithreading)