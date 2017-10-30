---
layout: post
title: "Python模块示例——time"
category: python
---

获取当前时间
------------
time模块提供了时间相关的一些函数，最直接的可能就是要得到一个当前的时间，代码：

    >>> import time
    >>> time.ctime()
    'Thu Oct 09 21:07:12 2014'

获取指定时间
------------
`ctime([sec])`也可以接受一个时间秒数作为参数，返回时间秒数所表示的时间。 如果不提供参数则返回当前的时间表示（如上）。

    >>> time.ctime(1000)
    'Thu Jan 01 08:16:40 1970'

我所说的时间秒数是什么呢？
`time.time()`方法得到一个当前的时间， 这个时间是用秒来表示的，从epoch这个时间点开始，也就是从公元1970年1月1日0时0分0秒到现在的时间秒数。Python中的时间是以这个时间为基础的。所以`time.ctime()`是根据`time.time()`计算得到的。


格式化的时间
------------
所谓格式化的时间，就是将一个计算机认可的时间转变为人工可读的时间。不同的国家有不同的时间显示方式。
如果我们要以“xxxx年xx月xx日 星期x”的格式显示时间，要怎么做呢？如果要显示“2014/10/09 10:10:10”，怎么编码。
在time模块里，有`strftime(format[, time])`这个函数。format是我们想要的格式，time是一个时间结构，time可选。

返回当前时间的格式化时间：

    >>> print time.strftime('%Y年%M月%d日 星期%w')
    2014年31月09日 星期4

对于具体的格式字符对应的意义，可以参考python文档。[格式指令链接](https://docs.python.org/2/library/time.html#time.strftime)

返回指定的时间的格式化时间。因为strftime接受第二个参数为一个strut_time，我们首先要有一个strut_time，可以通过strptime方法或者struct_time类得到，然后作为第二个参数传给函数strftime。


同时python也提供了其他的时间函数和一个类struct_time, time模块中所有的函数如下：

>time() -- return current time in seconds since the Epoch as a float
>
>clock() -- return CPU time since process start as a float
>
>sleep() -- delay for a number of seconds given as a float
>
>gmtime() -- convert seconds since Epoch to UTC tuple
>
>localtime() -- convert seconds since Epoch to local time tuple
>
>asctime() -- convert time tuple to string
>
>ctime() -- convert time in seconds to string
>
>mktime() -- convert local time tuple to seconds since Epoch
>
>strftime() -- convert time tuple to string according to format specification
>
>strptime() -- parse string to time tuple according to format specification
>
>tzset() -- change the local timezone

小结
-----
python中对本地化的时间没有支持，不像java中指定了locale本地属性为ZH_CN后会自动显示中文。如果需要更多的对时间的操作，python中也提供了`date`, `time`, `datetime`类位于`datetime`包中。

参考
----
Python文档： [Time access and conversions](https://docs.python.org/2/library/time.html)
