---
title: 函数
date: 2025-05-15 20:24
venue: Tianjin
collection: publications
category: Python
permalink: /publication/函数-2025-05-15
excerpt: 函数
---
# 函数

### 1. 函数
函数是将一组可重复使用的代码片段组织在一起方便在不同地方调用的功能，提高代码复用性、可读性和组织性。

### 2. 定义函数
语法：
def 函数名(参数1, 参数2 = 默认值, 参数3, ...) -> 返回值类型:
    函数体
    return 返回值

例如：
def say():
    print("hello")

def say(name, age, work='teacher'):
    print("My name is %s, %d years old. I'm %s" % (name, age, work))

不定参数传递
def say(name, age, work='teacher', *args):
    return max(args)

### 3. 函数的调用
返回值 = 函数名(参数1, 参数2, ...)

例如：
say()
say("张三", 20, farmer)