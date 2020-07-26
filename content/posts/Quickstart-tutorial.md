---
title: "Creation and basic operation of NumPy vector (group)"
date: 2020-07-23T15:43:05+08:00
categories: ["NumPy"]
tags: ["NumPy"]
draft: false
---

NumPy的主要操作对象是同类型的多维数组，若干个数组具有相同的长度及类型，各数组元素可以通过非负整数索引。在NumPy当中，数组的维度被称为轴(axes)。

## **如何理解轴(axes)**

举个例子，三维空间中一点的坐标$[1, 2, 1]$有一个轴，该轴有三个元素，即长度为3。对于二维数组$[[ 1., 0., 0.], [ 0., 1., 2.]]$，其包含两个轴，第一个轴长度为2，第二个轴的长度为3。从而，对于轴这一概念，可以得到如下结论

1. NumPy中的轴就是编程语言中数组的维度，即一个轴就是数组的一个维度，也就是说若数组为一维数组，那么就有一个轴，若为$n$维数组，那么就有$n$个轴，但是各个轴的长度未必一致;
2. NumPy中轴的长度就是当前轴的元素个数，即使是轴，也算一个元素;
3. NumPy中，由于轴等同于数组的维度，那么第一个轴的长度就是数组第一维的长度，第二个轴的长度就是数组第二维（第一维的元素就是第二维）的长度，以此类推。

由于NumPy主要涉及矩阵运算，那么**轴等于维度**这一结论如何对标线性代数中的维度呢？

线性代数中，维度就是向量的分量个数，对于三维空间中一点的坐标$[1, 2, 1]$，其为三维向量，但是在NumPy或编程语言中，它却是一维。如何理解呢？

NumPy及编程语言中，最后一个轴或者说维度，事实上代表的就是一个向量，但是仅仅能说明这是一个向量，对于该向量的维度，是通过轴的长度来指明的。倒数第二个轴或者维度，一定是向量的集合，即向量组，向量组中向量的个数是通过该轴的长度来指明的，这也说明了向量组可以通过二维数组来描述。若存在三维数组，即三个轴，那么第一维或者说第一个轴一定是向量组的集合，其中各个向量组可以通过下标来索引。例如

+ 对于向量$[1, 2, 1]^T$，编程语言中通过**一维**数组(包含**一个轴**)$[1, 2, 1]$来说明这是一个向量，通过$[1, 2, 1].length$，来说明这是一个三维向量;
+ 对于向量组$[ 1., 0., 0.]^T, [ 0., 1., 2.]^T$，编程语言通过**二维**数组(包含**两个轴**)来说明这是一个向量组，其第一个维度（第一个轴）的长度为2，代表这个向量组包含两个向量，这也指明了第二个轴是向量，那么第二个轴的长度自然就是向量的维数。

综上，从数组维度的角度看，NumPy中的轴（其本质就是数组的维度）其实就是向量（或向量组）的一种组织方式，若一数组有一个轴，那么说明该数组代表的是一个向量，如果数组为二维，那么说明其为向量组，若为三维，那么就是向量组构成的集合，该集合可以通过索引来查改，等等

## **NumPy中如何创建向量组**

NumPy中的数组类型被称为`ndarray`，意为$n$维数组，不同于Python中只具备基本操作的列表类型，NumPy中在该类型的对象上施加了诸多的方法，可以完成众多对向量(组)的操作。

### **将已知数组转化为向量组**

如果已经知道要创建的向量组，那么直接使用`np.array()`方法进行创建，其参数为若干维数组。例如参数为一维数组

```python
import numpy as np

a = np.array([2,3,4])
print(a)    # [2 3 4]
print(a.ndim)    # 1
print(a.shape)    # (3,)
```

参数为2维数组，如下

```python
import numpy as np

a = np.array([[1, 2, 3], [2,3,4]])
attrs = ['a.ndim', 'a.shape', 'a.size', 'a.dtype', 'a.itemsize', 'a.data']
for attr in attrs:
    print(attr + " : " + str(eval(attr)))
```

上述代码演示了类`ndarray`实例的诸多属性，对其解释如下

1. `ndarray.ndim`，数组的维度或轴，对应于向量组的向量个数;
2. `ndarray.shape`，由数组的维度及各维度长度构成的二元组，对应于向量组的个数以及各向量的维数;
3. `ndarray.size`，$n$维数组中元素的个数，为`ndarray.shape`中二元组元素的乘积;
4. `ndarray.dtype`，数组中基本元素的类型;
5. `ndarray.itemsize`，数组中每个基本元素的字节数;
6. `ndarray.data`，包含数组实际元素的缓冲区，基本上不用它.

上述代码输出如下

```python
a.ndim : 2
a.shape : (2, 3)
a.size : 6
a.dtype : int32
a.itemsize : 4
a.data : <memory at 0x00000214316FB480>
```

### **生成某个范围的向量组**

那么如何创建指定范围内连续元素的向量呢？使用`arange()`方法即可

```python
import numpy as np

a = np.arange(15)
print(a)    # [ 0  1  2  3  4  5  6  7  8  9 10 11 12 13 14]
```

事实上，`arange()`方法的原型为

```python
def arange([start,] stop[, step,], dtype=None)
```

其中第一个参数`start`默认为0，参数`step`为步长默认为1，参数`dtype`为该方法返回数组的元素类型默认为`None`.

若省略start参数，但是不省略步长，那么必须指明步长的属性名；如果不省略参数start，若需要指明步长，那么可以省略step属性名；dtype的类型为`np`的一个枚举量或者字符串，同样若前三个参数均存在，那么可以省略属性名。对于这三种情况分别演示如下

```python
import numpy as np

a = np.arange(5, step = 1.5)
print(a)    # [0.  1.5 3.  4.5]
b = np.arange(0, 5, 1.2)
print(b)    # [0.  1.2 2.4 3.6 4.8]
c = np.arange(0, 5, 1, 'float64')
print(c)    # [0. 1. 2. 3. 4.]
```

上面通过`arange()`方法创建的都是向量组中只有一个向量的$n$维向量，那么如何创建向量组呢？这就需要调整一维向量的尺寸。通过链式调用$reshape()$方法即可实现，如下

```python
import numpy as np

a = np.arange(15).reshape(3, 5)
print(a)
```

输出为

```python
[[ 0  1  2  3  4]
 [ 5  6  7  8  9]
 [10 11 12 13 14]]
```

即创建了一个包含三个向量的向量组，其中每个向量的维数为5。该方法是一个类方法，这意味着是可以直接作用在ndarray上的，如

```python
np.array([1, 2, 3, 4, 5, 6]).reshape(2, 3)
```

对于`reshape()`方法，其接受的参数之积一定要等于原`shape()`方法二元组元素之积。

### **如何创建指定范围内固定数量元素的向量组**

`arange()`方法可以用步长来创建向量（组），但问题在于数目不好计算（对于浮点数步长来说）。有一种办法是在给定范围内创建某干个元素，元素间距相同，这可以通过`linspace()`方法来实现，如下

```python
import numpy as np

a = np.linspace(0, 9, 10)
print(a)    # [0. 1. 2. 3. 4. 5. 6. 7. 8. 9.]
```

该函数原型为

```python
def linspace(start, stop, num=50, endpoint=True, retstep=False, dtype=None,
             axis=0):
```

我们只需要关注参数`num`，该参数说明若省略元素个数，那么默认生成50个元素。对于该函数同样可以链式调用，例如

```python
import numpy as np

a = np.linspace(0, 9, 10).reshape((2, 5))
print(a)
```

输出为

```python
[[0. 1. 2. 3. 4.]
 [5. 6. 7. 8. 9.]]
```

### **创建元素均为0或者1的向量组**

可以通过`zeros()`和`ones()`方法创建元素均为0或者1的任意尺寸合法的向量（组），其参数为一个元组，分别为向量的个数以及向量的维数，例如

```python
import numpy as np

a = np.zeros((3, 5))
print(a)
b = np.ones((3, 5))
print(b)
c = np.ones((3,))
print(c)
```

输出如下

```python
[[0. 0. 0. 0. 0.]
 [0. 0. 0. 0. 0.]
 [0. 0. 0. 0. 0.]]
[[1. 1. 1. 1. 1.]
 [1. 1. 1. 1. 1.]
 [1. 1. 1. 1. 1.]]
[1. 1. 1.]
```

### **创建合法尺寸的随机向量组**

首先需要获取一个numpy的随机数实例，代码为

```python
rdm = np.random.default_rng(1)
```

其参数为一个种子（seed）。然后就可以使用该实例创建随机向量组，如下

```python
a = rdm.random((3, 3), dtype = np.float64)
```

随机数的范围在$[0.0, 1.0)$，所生成的随机数类型为`float64`，不可以设置`dtype`为整型，另一个可选值就是`float32`。

## **向量组的基本操作**

### **向量组的加法、数乘、乘法及幂**

向量组的基本操作包括加法（+）、数乘（*）、以及乘法（@），示例如下

```python
import numpy as np

a = np.array([1, 2, 3, 4])
b = np.array([2, 3, 4, 5])
print(a + b)    # [3 5 7 9]
print(2 * b)    # [ 4  6  8 10]
print(a @ b.T) # 40
```

特别地，对于算符`*`，若一个操作数为标量，那么对应的就是向量（组）的数乘，否则在两向量（组）尺寸相同的情况下会进行逐行逐列元素的相乘。

对于向量（组）乘法，在尺寸对应的情况下，即可以使用算符`@`，也可以使用方法`dot()`，即上面的向量乘法也可以这么写

```python
print(a.dot(b.T)) # 40
```

向量组乘法的尺寸一定要相互对应，如

```python
import numpy as np

a = np.array([[1, 2], [2, 3], [3, 4]])
b = np.array([[2, 3, 4], [3, 4, 5]])
print(a @ b)
print(a.T @ b.T)
```

输出为

```python
[[ 8 11 14]
 [13 18 23]
 [18 25 32]]
[[20 26]
 [29 38]]
```

上述演示的向量组的基本操作，其本质上都新创建了一个向量（组），然后将运算的结果填充进去。另一种方法是，直接利用现有的尺寸满足运算结果的向量，这样就不用新创建向量组了，那么加法及数乘算符自然变成了`+=`以及`*=`，乘法则不一定，因为尺寸不一定吻合。

还有就是向量组的幂运算`**`，其左侧操作数一定为向量，右侧为一个标量，顺序不能搞反了，示例如下

```python
import numpy as np

a = np.array([1, 3, 5])
print((a * a) * a == a ** 3)
b = np.array([[1, 3, 5], [2, 4, 6]])
print((b * b) * b == b ** 3)
```

结果为

```python
[ True  True  True]
[[ True  True  True]
 [ True  True  True]]
```

显然，通过对向量组施加$n - 1$次向量组逐元素相乘，等同于向量组的$n$次幂运算。

如果参与运算的两个同尺寸的向量具有不同的元素类型，那么最终的结果一定会转型到最精确的一种类型，即向上转型（upcasting），如下

```python
import numpy as np
import math

a = np.ones(3, dtype = np.int32)
b = np.linspace(0, math.pi, 3)
print((a + b).dtype.name)    # float64
```

### **向量组的最值、和**

可以通过方法`sum()`,`max()`,`min()`来分别计算向量组中的和、最大值、最小值。如下

```python
import numpy as np

a = np.arange(0, 10).reshape(2, 5)
print(a.sum())    # 45
print(a.max())    # 9
print(a.min())    # 0
```

上述三个方法都可以通过axis参数来指明其轴。以求和为例，对于含有$n$个轴的数组$（n \ge 2）$，当取值为$n - 1$时，按行计算数组的和，为$n - 2$时，按列计算数组的和。无论数组包含几个轴，这种方法同样适用，其计算是以二维数组为单位的，所以才存在行和列。以下述四维数组为例

```python
import numpy as np

a = np.array([[[[1, 2, 3], [4, 5, 6]], [[2, 3, 4], [5, 6, 7]]], [[[1, 3, 5], [4, 6, 8]], [[2, 4, 6], [5, 7, 9]]]])
print(a)
print('-' * 10)
print(a.sum(axis = 3))
print(a.sum(axis = 2))
```

其输出为

```python
[[[[1 2 3]
   [4 5 6]]

  [[2 3 4]
   [5 6 7]]]


 [[[1 3 5]
   [4 6 8]]

  [[2 4 6]
   [5 7 9]]]]
----------
[[[ 6 15]
  [ 9 18]]

 [[ 9 18]
  [12 21]]]
[[[ 5  7  9]
  [ 7  9 11]]

 [[ 5  9 13]
  [ 7 11 15]]]
```

可以看到，以二维数组为单位进行求和，然后将结果包装在一个一维数组里最终替换该二维数组，对$n$维数组中若干个二维数组进行相同的操作之后，将其一维数组包装的所有结果再次包装在$n$维数组当中，此时，最终的结果将变成了$n - 1$维。

四位数组可能略有抽象，但是二维数组就很明显了，如下

```python
import numpy as np

a = np.arange(12).reshape((2, 6))
print(a)
print('-' * 10)
print(a.sum(axis = 1))
print(a.sum(axis = 0))
```

结果为

```python
[[ 0  1  2  3  4  5]
 [ 6  7  8  9 10 11]]
----------
[15 51]
[ 6  8 10 12 14 16]
```

可以看到按行按列计算之后，结果由二维变成了一维。

ndarray实例的cumsum()方法用来逐行计算$n$维数组的累计和，如果是2维，那么先累计第一行，第一行累计完后，再在此基础之上累计下一行，即从第一维数组的第一个元素开始，以此类推。二维数组和四维数组累计和如下

```python
import numpy as np

a = np.arange(12).reshape((2, 6))
print(a)
print(a.cumsum())

print('-' * 10)

b = np.array([[[[1, 2, 3], [4, 5, 6]], [[2, 3, 4], [5, 6, 7]]], [[[1, 3, 5], [4, 6, 8]], [[2, 4, 6], [5, 7, 9]]]])
print(b)
print(b.cumsum())
```

输出为

```python
[[ 0  1  2  3  4  5]
 [ 6  7  8  9 10 11]]
[ 0  1  3  6 10 15 21 28 36 45 55 66]
----------
[[[[1 2 3]
   [4 5 6]]

  [[2 3 4]
   [5 6 7]]]


 [[[1 3 5]
   [4 6 8]]

  [[2 4 6]
   [5 7 9]]]]
[  1   3   6  10  15  21  23  26  30  35  41  48  49  52  57  61  67  75
  77  81  87  92  99 108]
```

该函数同样接受一个轴参数axis，其用法与上述示例的`sum()`函数一致。示例如下

```python
import numpy as np

a = np.arange(12).reshape((2, 6))
print(a)
print(a.cumsum(axis = 1))

print('-' * 10)

b = np.array([[[[1, 2, 3], [4, 5, 6]], [[2, 3, 4], [5, 6, 7]]], [[[1, 3, 5], [4, 6, 8]], [[2, 4, 6], [5, 7, 9]]]])
#print(b)
print(b.cumsum(axis = 3))
```

```python
[[ 0  1  2  3  4  5]
 [ 6  7  8  9 10 11]]
[[ 0  1  3  6 10 15]
 [ 6 13 21 30 40 51]]
----------
[[[[ 1  3  6]
   [ 4  9 15]]

  [[ 2  5  9]
   [ 5 11 18]]]


 [[[ 1  4  9]
   [ 4 10 18]]

  [[ 2  6 12]
   [ 5 12 21]]]]
```

## **通用函数**

考虑到数组乃至高维数组并非为向量组（的集合），有可能仅仅是数据的简单组织。NumPy也提供了对这类数据的通用数学处理函数，例如math包中的sin、exp等功能的函数，NumPy中的这类函数被称为通用函数(Universal Functions)。通用函数的特点是，它会分别施加在数组中的每个元素当中，然后返回一个相同维度的数组。通用函数直接通过numpy别名或者numpy以成员运算符的形式进行调用。

比如，对随机生成的float64类型的$2 \times 6$矩阵，首先将其映射到$[0, 10)$区间内，然后对其向上取整

```python
a = np.ceil(10 * ram.random((2, 6), np.float64), dtype = np.float64)
```

还可以对上述矩阵进行以元素为单位（标量）的加法操作，如下

```python
import numpy as np

ram = np.random.default_rng(1)
a = np.ceil(10 * ram.random((2, 6), np.float64), dtype = np.float64)
b = np.arange(12).reshape((2, 6))
print(a)
print(b)
print(np.add(a, b, dtype = 'float64'))
```

结果为

```python
[[ 6. 10.  2. 10.  4.  5.]
 [ 9.  5.  6.  1.  8.  6.]]
[[ 0  1  2  3  4  5]
 [ 6  7  8  9 10 11]]
[[ 6. 11.  4. 13.  8. 10.]
 [15. 12. 14. 10. 18. 17.]]
```

通用函数有很多，类似的包括`all, any, apply_along_axis, argmax, argmin, argsort, average, bincount, ceil, clip, conj, corrcoef, cov, cross, cumprod, cumsum, diff, dot, floor, inner, invert, lexsort, max, maximum, mean, median, min, minimum, nonzero, outer, prod, re, round, sort, std, sum, trace, transpose, var, vdot, vectorize, where`等。相关函数的介绍可以通过下述链接访问

```css
https://numpy.org/doc/stable/reference/generated/numpy.{{ufunc}}.html
```

将上述连接的模板变量ufunc替换为实际的函数名即可。例如要访问`add()`函数的文档，那么通过下面的链接即可

```css
https://numpy.org/doc/stable/reference/generated/numpy.add.html
```

## **索引、切片及迭代**

同Python中的所有序列一样，ndarray同样可以进行索引、切片和迭代操作。NumPy文档上说的是一维数组可以被索引、切片和迭代，但是这有一个隐含的意思，即一维是相对的。对于$n$维数组来说，其一维元素就是$n - 1$维，对于该$n - 1$维元素而言，$n - 2$维又是其第一维。了解了这个前提，接下来就会很方便。

### **一维数组的索引、切片及迭代**

当然是通过索引来访问。但是一次性只能访问一个元素，如何访问多个连续元素呢？和Python一样

```python
import numpy as np

ram = np.random.default_rng(1)
a = np.ceil(10 * ram.random((9,), np.float64), dtype = np.float64)
print(a)    # [ 6. 10.  2. 10.  4.  5.  9.  5.  6.]
print(a[0])    # 6.0
print(a[1 : 5])    # [10.  2. 10.  4.]
```

如果想访问索引不连续的元素呢？用dnarray对象将索引包含然后作为索引访问即可，如下

```python
import numpy as np

ram = np.random.default_rng(1)
a = np.ceil(10 * ram.random((9,), np.float64), dtype = np.float64)
print(a)    # [ 6. 10.  2. 10.  4.  5.  9.  5.  6.]
print(a[0])    # 6.0
print(a[1 : 5])    # [10.  2. 10.  4.]
print(a[np.array([1, 3, 5])])    # [ 10.  10.  5.]
```

上述访问多个元素的操作本质就是创建了切片，然而切片的还有一种操作就是从起始位置开始每隔若干个元素进行一次元素修改，即如果切片操作为`a[1 : 6 : 3] = 100`的话，那么数组的第一个元素将会被修改为100，然后从下一个元素开始数第三个元素再次修改，以此类推，示例如下

```python
import numpy as np

ram = np.random.default_rng(1)
a = np.ceil(10 * ram.random((9,), np.float64), dtype = np.float64)
print(a)    # [ 6. 10.  2. 10.  4.  5.  9.  5.  6.]
a[1 : 6 : 3] = 100
print(a)    # [  6. 100.   2.  10. 100.   5.   9.   5.   6.]
```

`a[ : : -1]`表示反转一维数组a。

对于一维数组的迭代，依然通过`for in`循环来遍历其元素（标量）

```python
for ele in a:
    print(ele)
```

### **二维数组的索引、切片及迭代**

二维数组的访问，需要通过两个下标来定位一个标量，分别为行和列，均从0开始计数。事实上，行标指明了二维数组中的某个向量，而列标指明了该向量中的某个分量，从而便能准确的定位一个标量。

如果访问或切片二维数组的某个子式呢？如下

```python
import numpy as np

ram = np.random.default_rng(1)
a = np.ceil(10 * ram.random((6, 6), np.float64), dtype = np.float64)
print(a[1 : 3, 3 : 5])
```

从而上述代码实现访问子式或者创建切片，其特点就是子式是原数组中完整的部分。当然索引可以为负，只是最小的负数一定要在`:`之前，否则什么也取不到，因为最小的负数所代表的行的真实行标大于较大的负数所代表的真实行标,如下

```python
print(a[-3 : -1, 2 : 4])
```

在二维数组中若`[]`中只存在一个数字，那么其为行标，列默认为全部列。即下式成立

```python
a[-3] == a[-3, 0 : len(a[-3])]    # [ True  True  True  True  True  True]
```

对于二维数组的遍历，一种常见的方法是通过两层循环来实现，即

```python
for arr in a:
    for ele in arr:
        print(ele)
```

因为第一层循环拿到的只是一个一维数组，所以必须使用两层循环。另一种实现遍历的方法是，通过ndarray对象的flat属性，该属性为数组元素的迭代器，使用方式如下

```python
for ele in a.flat:
    print(ele)
```

### **针对高维数组的dots(...)**

NumPy中三个点代表省略若干尺度信息。省略信息的多少取决于该符号前后的明确表示的尺度信息，该符号的意思就是若干个连续的` : `，也就是将某个维度全选了。以二维数组为例，下式完全成立

```python
a[-3] == a[-3, 0 : len(a[-3])]    # [ True  True  True  True  True  True]
a[-3] == a[-3, ...]    # [ True  True  True  True  True  True]
```

含义为选中该二维数组的倒数第三行的全部，因为一维数组的信息被省略了，即全选。这种方式主要应用在高维数组中，比如，对于三维数组`np.ceil(10 * np.random.default_rng(1).random((3, 6, 6), np.float64), dtype = np.float64)`，下式必然成立

```python
a[-2, ...] == a[-2]
```

即选中倒数第二个向量组。下式也恒成立

```python
a[-2, ..., -1] == a[-2, 0 : len(a[-2]), -1]
```

即选中倒数第二个向量组的所有行的倒数第一个标量，最终必然是一个列向量（但是以行向量表示）。