---
title: "NumPy object copy"
date: 2020-07-26T12:51:05+08:00
categories: ["NumPy"]
tags: ["NumPy"]
draft: false
---

当操作数组的时候，有时候需要将数据拷贝到一个新的数组中，有时候不需要。其实无论是在编程语言中还是NumPy中，都主要涉及以下三种情况。

## **完全没有复制(No Copy at All)**

简单赋值不会发生对象或者对象数据的拷贝。如下所示`a、b`均持有同一个ndarray对象实例

```python
import numpy as np

a = np.array([1, 2, 3])
b = a
print(id(a) == id(b))    # True
```

对象作为函数参数传递，传递的并非对象的值而是引用。因此函数调用实质上使用的是原对象，如下

```python
import numpy as np

def addr(obj):
    return id(obj)

a = np.array([1, 2, 3])
print(id(a) == addr(a))    # True
```

## **浅拷贝(Shallow Copy）**

NumPy中`viwew()`方法可以创建一个当前ndarray对象的副本，特点是它们内部对象具有相同的数据相同的地址但是整个ndarray对象的地址不同。可以通过一个例子来说明

```pytohn
import numpy as np

a = np.array([[1, 2, 3], [4, 5, 6]])
b = a.view()
print("id(a) : " + str(id(a)))
print("id(a[0]) : " + str(id(a[0])))
print("id(a[1]) : " + str(id(a[1])))
print('-' * 10)
print("id(b) : " + str(id(b)))
print("id(b[0]) : " + str(id(b[0])))
print("id(b[1]) : " + str(id(b[1])))
```

输出如下

```python
id(a) : 1921142584624
id(a[0]) : 1921145281632
id(a[1]) : 1921145281632
----------
id(b) : 1921142584864
id(b[0]) : 1921145281632
id(b[1]) : 1921145281632
```

可以看到，持有新旧ndarray对象的变量`a`、`b`的地址不同，但是内部地址依然相同。这也就意味着无论是变量`a`还是`b`，对ndarray的操作对于任何一方都是可见的。

## **深拷贝(Deep Copy)**

`copy()`方法可以创建一个具有完整副本且内部对象地址不相同的ndarray副本。示例如下