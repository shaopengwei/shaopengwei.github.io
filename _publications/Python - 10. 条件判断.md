---
title: 条件判断
date: 2025-05-15 20:23
venue: Tianjin
collection: publications
category: Python
permalink: /publication/条件判断-2025-05-15
excerpt: 条件判断
---
# 条件判断

### 1. if-else
if 判断条件:
    执行代码
    执行代码
elif 判断条件:
    执行代码
    执行代码
else:
    执行代码
    执行代码

### 2. switch-case
Python并没有提供switch语句，可以通过字典实现switch语句的功能。
x = 1
y = 2
operator = "/"
result = {         # 定义字典
    "+" : x + y,
    "-" : x - y,
    "*" : x * y,
    "/" : x / y 
}
print (result.get(operator))