---
title: 字典类型及操作
date: 2025-05-15 20:23
venue: Tianjin
collection: publications
category: Python
permalink: /publication/字典类型及操作-2025-05-15
excerpt: 字典类型及操作
---
# 字典类型及操作

### 1. 字典类型
字典（Dictionary）是一种无序的键值对数据结构。
字典中的键（key）必须是唯一的、不可变的数据类型（例如字符串、数字、元组等），而值（value）可以是任意数据类型（包括列表、字典、对象等）。

### 2. 创建字典
可以使用花括号 {} 来创建字典，并通过 键: 值 的形式指定元素。
my_dict = {'name': 'Alice', 'age': 25, 'city': 'New York'}

### 3. 字典元素操作
访问元素：
my_dict['name']
修改元素：
my_dict['age'] = 20
添加元素：
my_dict['job'] = 'teacher'
删除元素：
del my_dict['city']
检查是否存在某个键：
'name' in my_dict
获取字典的所有键：
my_dict.keys()
获取字典的所有值：
my_dict.values()
获取字典的所有键值对：
my_dict.items()

### 4. 字典元素的遍历
使用 items() 方法：
    for key,value in my_dict.items():
        print(value)

使用 keys() 方法：
    for key in my_dict.keys():
        print(my_dict[key])