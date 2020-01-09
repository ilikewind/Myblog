---
layout: post
title: "Python高级特性之列表生成式、生成器、迭代、可迭代对象与迭代器"
subtitle: ""
catalog: true
author: "WangW"
header-style: text
tags: 
    - Python
---


## 引言
在Python有几种高级特性比较重要，比如切片，迭代，列表生成式子，可迭代对象，生成器以及迭代器。本文主要讲解后面四种高级特性。  <!--break-->

![](https://raw.githubusercontent.com/learnroad/image_host/master/2019/20200109162045.png)

## 可迭代对象与迭代器
可迭代对象:
:    可以直接作用于``for``循环的对象统称为可迭代对象（Iterable），简单来说就是一个对象拥有`__iter__`方法，他就是可迭代对象；

迭代器:
:    可以被``next()``函数调用并不断返回下一个值的对象称为迭代器（Iterator）， 在可迭代对象的基础上增加一个`__next__`方法；注：可以通过`iter(example)`函数获取example可迭代对象的迭代器。


```python
class MyList(object):
    """自定义的一个可迭代对象"""
    def __init__(self):
        self.items = []

    def add(self, val):
        self.items.append(val)

    def __iter__(self):
        myiterator = MyIterator(self)
        return myiterator


class MyIterator(object):
    """自定义的供上面可迭代对象使用的一个迭代器"""
    def __init__(self, mylist):
        self.mylist = mylist
        # current用来记录当前访问到的位置
        self.current = 0

    def __next__(self):
        if self.current < len(self.mylist.items):
            item = self.mylist.items[self.current]
            self.current += 1
            return item
        else:
            raise StopIteration

    def __iter__(self):
        return self


if __name__ == '__main__':
    mylist = MyList()
    mylist.add(1)
    mylist.add(2)
    mylist.add(3)
    mylist.add(4)
    mylist.add(5)
    for num in mylist:
        print(num)

```

## 生成器
生成器:
:    是一种特殊的迭代器，构造比迭代器要简单的多；`return` 换 成 `yield` (Generator), 除了用`next`唤醒以外，还可以用`send`唤醒，使用send函数可以在唤醒的同时向断点处传入一个附加的数据。

```
使用了yield关键字的函数不再是函数，而是生成器。（使用了yield的函数就是生成器）
yield关键字有两点作用：
保存当前运行状态（断点），然后暂停执行，即将生成器（函数）挂起
将yield关键字后面表达式的值作为返回值返回，此时可以理解为起到了return的作用
可以使用next()函数让生成器从断点处继续执行，即唤醒生成器（函数）
Python3中的生成器可以使用return返回最终运行的返回值，而Python2中的生成器不允许使用return返回一个返回值（即可以使用return从生成器中退出，但return后不能有任何表达式）。
```

**次要概念：**
迭代:
:    如果给定一个list或者tuple，我们可以通过``for``循环来遍历这个list或者tuple,这种遍历我们称为迭代（Iteration）.

列表生成式:
:    列表生成式即List Comprehensions, 是Python内置的非常简单却强大的可以用来创建list的生成式.




## 关系
首先，可以看的出来，迭代（Iteration）是一个概念，描述了一个循环遍历过程；不理解概念的可以先看后面例子帮助理解；

#### 列表生成式,可迭代对象,迭代器,生成器之间的关系

- **生成器既是可迭代对象，也是迭代器**：首先，生成器可以被``next()``调用，所以是迭代器；其次，生成器可以用于``for``循环，所以也是迭代器；
- **迭代器一定是可迭代对象，但是可迭代对象不一定是迭代器**：比如list, dict, str等集合数据类型是可迭代对象，但不是迭代器，但是他们可以通过``iter()``函数生成一个迭代器对象。
- **生成器包括生成式比如``(x*x for x in range(10))``,和带有``yield``的伸成函数**,仔细看，能够看的出来生成式和就是将列表生成式的中括号改成括号。

## 实例以及参考
- [Python之列表生成式、生成器、可迭代对象与迭代器](https://www.cnblogs.com/yyds/p/6281453.html)
- [廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/1016959663602400/1017269809315232)
- [Python可迭代对象，迭代器，生成器的区别](https://blog.csdn.net/jinixin/article/details/72232604)
