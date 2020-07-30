---
title: "NumPy object copy and broadcasting rules"
date: 2020-07-30T20:32:05+08:00
categories: ["NumPy"]
tags: ["NumPy"]
draft: false
---

当操作数组的时候，有时候需要将数据拷贝到一个新的数组中，有时候不需要。其实无论是在编程语言中还是NumPy中，都主要涉及以下三种情况。

## **NumPy数组拷贝**

### **完全没有复制(No Copy at All)**

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

### **浅拷贝(Shallow Copy）**

NumPy中`viwew()`方法可以创建一个当前ndarray对象的副本，特点是它们内部对象具有相同的数据相同的地址但是整个ndarray对象的地址不同。也就是说通过`view()`方法创建的ndarray对象本身是不持有自己的数据的，其数据引用创建该对象的数组的数据。可以通过一个例子来说明

```python
import numpy as np

a = np.array([[1, 2, 3], [4, 5, 6]])
b = a.view()
print(a is b)    # False, 整个ndaray的地址不同
print(b.flags.owndata)    # False, 其本身不持有数据
print(a[0])    # [1 2 3]
b[0] = [2, 3, 4]
print(a[0])    # [2 3 4]
print(b[0])    # [2 3 4]
```

可以看到，两个ndarray对象地址不同，但是内部为同一个引用。通过`view()`方法创建出的对象不持有自己的数据。

### **深拷贝(Deep Copy)**

`copy()`方法可以创建一个具有完整副本且内部对象地址不相同的ndarray副本。其持有的数据均是自己的，这就意味着修改数据不会使原数组发生变化。示例如下

```python
import numpy as np

a = np.array([[1, 2, 3], [4, 5, 6]])
b = a.copy()
print(a is b)    # False, 整个ndaray的地址不同
print(b.flags.owndata)    # True, 其本身持有数据
print(a[0])    # [1 2 3]
b[0] = [2, 3, 4]
print(a[0])    # [1 2 3]
print(b[0])    # [2 3 4]
```

数组的切片实际上是对该数组数据的浅拷贝（即使是原数组的一部分），比如

```python
import numpy as np

a = np.r_[1:4, 0, 3]
b = a[1 : 3]
print(a)    # [1 2 3 0 3]
b[0] = 10
print(a)    # [ 1 10  3  0  3]
print(b)    # [10  3]
```

所以当使用切片时，如果无法保证对该切片不修改，那么就应该使用深拷贝，防止对原数组发生修改，上述代码可改为

```python
import numpy as np

a = np.r_[1:4, 0, 3]
b = a[1 : 3].copy()
print(a)    # [1 2 3 0 3]
b[0] = 10
print(a)    # [1 2 3 0 3]
print(b)    # [10  3]
```

## **NumPy广播规则(Broadcasting rules)**

NumPy广播允许通用函数以一种有意义的方式对不同尺寸的输入进行处理。

### **最简单的广播规则**

当一个数组与一个标量相运算时，标量将会被拉伸为一个与数组有着相同长度的数组，其元素均为标量的简单拷贝。这也就是向量的数乘运算可以通过符号*实现的原因，即向量数乘`2 * np.array([1, 2, 3])`实际上是`np.array([2, 2, 2]) * np.array([1, 2, 3])`。那么下面代码也是成立的

```python
np.multiply(np.array([1, 2, 3]), 2) == np.multiply(np.array([1, 2, 3]), np.array([2, 2, 2]))
```

当然NumPy足够智能，实际上遇到标量与数组相运算时，并不会真正地去拷贝。

### **一般广播规则**

在NumPy中，对于运算相兼容的尺寸包含两种，分别是

+ 第二个数组的维度与第一个数组的维度后兼容；
+ 第二个数组的维度为1，长度为1.

我们先看第一种情况，这里的后兼容并不是通常意义上的向后兼容，而是尺寸上的吻合。比如，下面的这几种尺寸就吻合

```python
(2, 2, 2) is compatible with (2,)
(2, 2, 2) is compatible with (2, 2)
```

也就是说被运算的数组必须是第一个数组尺寸的一部分，且必须从尾部开始。无论被运算数组包含几个轴，只要与第一个数组后兼容即可。比如下面的尺寸就是不兼容的

```python
(2, 2, 3) is not compatible with (2,)
```

但是如果将尺寸`(2,)`改成`(3,)`，那么就兼容了。

另一种情况，就是如果被运算数组只有一个轴，	且长度为一，那么和谁都兼容。即

```python
(2, 2, 2) is compatible with (2,)
(2, 2, 2) is compatible with (2,)
(2, 2, 3) is not compatible with (2,)
```

对于只含有一个长度为1的1个轴的数组，即`(1,)`，参与运算时会先将其拉伸为`(n,)`，这里的`n`与第一个数组的最后一个轴的长度一致。然后再按行与第一个数组的元素匹配计算。