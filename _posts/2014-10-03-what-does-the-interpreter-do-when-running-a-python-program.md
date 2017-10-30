---
layout: post
title: "一个python程序启动时interpreter做了哪些工作"
category: python
---
一个python程序启动时interpreter做了哪些工作？

Python是一种解析性语言
----------------------
编译器先把程序编译成字节码(byte code), 再进行解析。字节码是一种中间代码的存在， 为什么说一个语言是跨平台的？解析性对跨平台有极大地帮助。
词法、语法、句法分析


可以参考：[Introduction to the Python Interpreter]

写一篇文章实在是比写代码难多了，一直不以为然，写起来才知道对我来说有多么的难。想写一篇关于Python的简单文章，这是几天之前的构想了，到现在还是无从下手，不知道该写些什么。写一篇Blog, 觉得越简单的文章，写起来越难。要求读起来通俗易懂， 就要看你对你写的东西理解有多深。


模块的搜索路径
---------------

加载一个模块时, 模块的搜索路径顺序？当一个模块被import时， python是如何搜索并加载模块的呢? 解析器首先搜索python内建模块(built-in modules), 如果在内建模块中未发现， 则搜索sys.path中所列的文件夹。 

1. 当前文件夹 
2. PYTHONPATH环境变量 
3. python安装默认的第三方包文件夹 

        sys.prefix/Lib/site-packages 
        用户文件夹 
        ~/.local/lib/pythonX.Y/site-packages

##参考： 

[Introduction to the Python Interpreter](http://akaptur.github.io/blog/2013/11/15/introduction-to-the-python-interpreter/)

[Python Module Search Path](https://docs.python.org/2/tutorial/modules.html#the-module-search-path)

[Python Standard Modules](https://docs.python.org/2/tutorial/modules.html#standard-modules)

