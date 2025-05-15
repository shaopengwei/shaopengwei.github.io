---
title: Python 的输入和输出
date: 2025-05-15 20:21
venue: Tianjin
collection: publications
category: Python
permalink: /publication/Python 的输入和输出-2025-05-15
excerpt: Python 的输入和输出
---
# Python 的输入和输出

### 1. 输出方式
```让 python 在控制台输出内容```
常见的输出方式：
    1）标准输出 print()
    最简单直观的输出方式
    可以输出多种类型
    输出完成后自动换行
    例如：
        print("hello world!")
        print(1)
        print(",")
        print("你好")
        print("name: ", "zhangsan")
    2）格式化占位符输出 %
    当需要对输出的内容格式化输出，可以使用操作符 %
    例如：
        print("%d" % 2024)
        print("%s" % "你好")
        print("%f" % 3.1415926)
        print("今年是 %d, 圆周率PI = %0.2f" % (2024, 3.1415926))
        print("限制字符输出长度：%3s" % "hello world")
    3）格式化输出 str.format()
    提供更复杂的格式化需求
    例如：
        print("第一个数字{0}, 第二个字符{1}, 第三个小数{2:.2f}".format(1, "。", 3.1415926))
        print("Name: {name}, Age: {age}".format(name = "zhangsan", age = "18"))
        print("{0} - {1} - {0}".format(10, 20))
    4）格式化字符串常量输出 f-string
    支持动态计算出输出内容中的变量
    例如：
        name = "zhangsan"
        print(f"你好，{name}")
总结：在选择输出方式时，尽量选择简洁方便、易懂，符合团队代码风格的输出方式为宜。

### 2. 输入方式
```通过控制台输入内容给 python```
python 的标准输入方式为 input()，输入的内容均以字符串形式传入给 python，然后再通过其他方式转换为需要的类型
例如：
    a = input("请输入一个数：")
    a,b = input("请输入两个数，用空格分开：").split()
    a,b = input("请输入两个数，用逗号分开：").split(",")
    
