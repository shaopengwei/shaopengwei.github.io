---
title: 列表类型及操作
date: 2025-05-15 20:23
venue: Tianjin
collection: publications
category: Python
permalink: /publication/列表类型及操作-2025-05-15
excerpt: 列表类型及操作
---
# 列表类型及操作

### 1. 列表类型
列表是由多个```任意类型```的元素组成的```有序```集合。

### 2. 列表创建
列表元素之间用逗号分隔，所有元素用中括号（[]）包括起来。
创建方式一：testList = [1,2,3,4,5]
创建方式二：testList = list(1,2,3,4,5)

列表元素中可以包含任意类型数据：
list1 = [1, 'a', 3.14, True, "hello", [1,2,3]]

### 3. 访问列表元素
正向坐标：
list1[0], list1[1], list1[2]..., list1[5]
反向坐标：
list1[-6], list1[-5], list1[-4]..., list1[-1]
访问列表中部分元素：
list1[1:4]     list1中的第2个元素到第4个元素，即等于2小于4的元素
list1[1:]      list1中第2个元素到最后一个元素
list1[:-1]     list1中第1个元素到倒数第二个元素

### 4. 更新列表元素
列表元素可以被替换修改
添加元素：
list1.append(5)      在列表最后追加元素5
list1.insert(2, 3)   在下标2的位置插入元素3，其他元素依次向后退
删除元素：
list1.remove(1)      删除list1中值为1的元素
list1.pop()          删除并返回list1中最后一个元素
list1.pop(2)         删除并返回list1中下标为2的元素
del list1[1]         删除列表中下标为1的元素
修改元素：
list1[1] = 100       直接赋值修改

### 5. 操作列表
列表长度：
len(list1)
扩展列表：
list1.extend(list2)       将列表list2追加到list1的后面
拼接列表：
list3 = list1 + list2     列表list1拼接list2返回新的列表
清空列表：
list1.clear()             删除列表中的所有元素
列表比较：
list1 == list2            两个列表元素完全相同时返回True
list1 != list3
列表元素排序：
list1.sort()               从小到大排序
list1.sort(reverse=True)   逆序排序
列表元素反转：
list1.reverse()            列表元素反转排列
统计列表中元素出现次数：
list1.count(1)             列表中元素1的个数
查找元素下标：
list1.index(1)             返回第一个元素1的下标

### 6. 遍历列表元素
使用for循环：
    for item in list1:
        print(item)
    for i in range(len(list1)):
        print(list1[i])

使用while循环：
    index = 0
    while index < len(list1):
        print(list1[index])
        index += 1

使用列表推导式：
    newList = [item*2 for item in list1]