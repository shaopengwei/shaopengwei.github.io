---
title: 类和对象
date: 2025-05-15 20:24
venue: Tianjin
collection: publications
category: Python
permalink: /publication/类和对象-2025-05-15
excerpt: 类和对象
---
# 异常处理

### 1. 异常
异常是指程序执行过程中发生的错误和异常情况。当出现异常时，程序停止执行并向外抛出异常对象。

### 2. 异常处理
如果对抛出的异常对象不进行处理，程序会停止执行。
使用 try 语句来拦截异常并处理

语法：
try:
    需要执行的语句
except 检测异常类型1 as e:
    对出现的异常 e 进行判断和处理
except 检测异常类型2 as e:
    对出现的异常 e 进行判断和处理
except 检测异常类型3 as e:
    对出现的异常 e 进行判断和处理
except (异常类型1, 异常类型2, 异常类型3) as e:        # 使用元组列出需要接收的异常类型
    对出现的异常 e 进行判断和处理
except Exception as e:                            # 万能异常
    对出现的异常 e 进行判断和处理
else:
    没有捕获异常会执行的语句
finally:
    不论是否有异常都会执行的语句

### 3. 抛出自定义异常信息
语法：
raise 自定义异常类型(异常参数)

### 4. 常见 Python 异常类型
NameError()
SyntaxError()
KeyError()
IOError()
以及可以自定义异常类型