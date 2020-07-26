---
title: "Introduction to The Design and Analysis of Algorithms(2)"
date: 2020-04-09T21:22:21+08:00
categories: ["Algorithm"]
tags: ["Algorithm"]
draft: false
---

## 检查序列元素是否唯一

最简单的办法就是遍历，时间复杂度为$n^2$，如下

```javascript
function unique(arr) {
    for (var i = 0; i < arr.length - 1; i++) {
        for (var j = i + 1; j < arr.length; j++) {
            if (arr[i] == arr[j]) {
                return false;
            }
        }
    }
    return true;
}
```

另一种方式是借助哈希表，两次扫描即可完成，Java代码如下

```java
import java.util.HashMap;
import java.util.Map;

public class CheckUnique {

    public static void main(String[] args) {
        String[] arr = {"he", "ll", "o", "wor", "ld", "ld"};
        Integer[] arr1 = {1, 2, 3, 4, 5, 6};
        System.out.println(isUnique(arr));  // false
        System.out.println(isUnique(arr1)); // true
    }

    public static <T> boolean isUnique(T[] arr) {
        Map<T, Integer> map = new HashMap<T, Integer>();
        for (int i = 0; i < arr.length; i++) {
            if (map.containsKey(arr[i])) {
                map.put(arr[i], map.get(arr[i]) + 1);
            } else {
                map.put(arr[i], 1);
            }
        }
        for (Integer val : map.values()) {
            if (val != 1) {
                return false;
            }
        }
        return true;
    }
    
}
```

## 矩阵乘法

矩阵乘法规则为，对于矩阵$A$($m \times n$)和矩阵$B$($n \times o$)而言，其结果矩阵$A \times B$($m \times o$)的每个元素为矩阵$A$对应行和矩阵$B$对应列的点积。定义如下函数实现矩阵乘法：

+ `getRow(arr, row)`: 获取矩阵某行;
+ `getCol(arr, col)`: 获取矩阵某列;
+ `dotProduct(arr1, arr2)`: 计算行与列的点积;
+ `multiply(arr1, arr2)`: 计算矩阵乘积.

```javascript
function getRow(arr, row) {
    var ret = new Array(arr[0].length);
    for (var i = 0; i < arr[row].length; i++) {
        ret[i] = arr[row][i];
    }
    return ret;
}

function getCol(arr, col) {
    var ret = new Array(arr.length);
    for (var i = 0; i < arr.length; i++) {
        ret[i] = arr[i][col];
    }
    return ret;
}

function dotProduct(arr1, arr2) {
    var rst = 0;
    for (var i = 0; i < arr1.length; i++) {
        rst += arr1[i] * arr2[i];
    }
    return rst;
}

function multiply(arr1, arr2) {
    var retMatrix = new Array();
    var allRow = arr1.length;
    var allCol = arr2[0].length;
    for (var i = 0; i < allRow; i++) {
        retMatrix[i] = new Array();
        for (var j = 0; j < allCol; j++) {
            retMatrix[i][j] = dotProduct(getRow(arr1, i), getCol(arr2, j));
        }
    }
    return retMatrix;
}

var arr1 = [[1, 3, 2], [1, 0, 0], [1, 2, 2]];
var arr2 = [[0, 0, 2], [7, 5, 0], [2, 1, 1]];
multiply(arr1, arr2)

-------output-------
(3) [Array(3), Array(3), Array(3)]
0: (3) [25, 17, 4]
1: (3) [0, 0, 2]
2: (3) [18, 12, 4]
length: 3
__proto__: Array(0)
```

## 英文字母排序

一种走弯路的思路为，先根据字母顺序初始化一个字母表序列，然后将输入字母映射到该序列上得到输入字母对应的索引，然后对索引排序，最终再映射回去，代码如下

```javascript
function map(arr, ele) {
    for (var i = 0; i < arr.length; i++) {
        if (ele.toLowerCase() == arr[i]) {
            return i;
        }
    }
    return -1;
}

function alphabetSort(arr) {
    var master = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z'];
    var arrsindex = new Array();
    for (var i = 0; i < arr.length; i++) {
        arrsindex[i] = map(master, arr[i]);
    }
    arrsindex.sort(function (a, b) {
        return a - b;
    });
    var ret = new Array();
    for (var i = 0; i < arrsindex.length; i++) {
        ret[i] = master[arrsindex[i]];
    }
    return ret;
}

alphabetSort(['h', 'e', 'l', 'l', 'o']) // (5) ["e", "h", "l", "l", "o"]
```

另一种办法是利用字符ASCII码进行排序，再将排序后的ASCII码转换为字符，这种情况下可实现大小写字母的排序(第一种方法也可以)，当然也可以忽略大小写，如下

```javascript
function alphabetSort(arr) {
    var ascii = new Array();
    for (var i = 0; i < arr.length; i++) {
        ascii[i] = arr[i].charCodeAt(0);
    }
    ascii.sort(function (a, b) {
        return a - b;
    });
    var ret = new Array();
    for (var i = 0; i < ascii.length; i++) {
        ret[i] = String.fromCharCode(ascii[i]);
    }
    return ret;
}
```

## 若干递归算法

### 阶乘

对于任意非负整数$n$，计算阶乘函数$F(n) = n!$的值，由于其递推公式为

\begin{equation}n! = 1 \times 2 \times ··· \times (n - 1) \times n = (n - 1)! \times n, \ if \  n \ge 1\end{equation}

其代码如下

```javascript
function f(n) {
    if (n == 0) return 1;
    return f(n - 1) * n;
}
```

上述递归算法可用尾递归优化重写，如下

```javascript
function f(n, rst) {
    if (n == 0) return rst;
    return f(n - 1, rst * n);
}

f(4, 1) // 24
```

### 十进制数的二进制位数

```javascript
function bit(n) {
    if (n == 1) return 1;
    return bit(Math.floor(n / 2)) + 1;
}
```

同样利用尾递归进行优化，如下

```javascript
function bit(n, rst) {
    if (n == 1) return rst + 1;
    return bit(Math.floor(n / 2), rst + 1);
}

bit(8, 0)   // 4
```

### 前n个立方之和

其递推公式为

\begin{equation}S(n) = 1^3 + 2^3 + ··· + n^3 = S(n - 1) + n^3\end{equation}

```javascript
function sum(n) {
    if (n == 0) return 0;
    return sum(n - 1) + n * n * n;
}

function sumTail(n, rst) {
    if (n == 0) return rst;
    return sumTail(n - 1, rst + n * n * n);
}

sum(10) == sumTail(10, 0);  // true
```

### 又一个递归算法

请基于公式$2^n = 2^{n - 1} + 2^{n - 1}$设计一个递归算法，当$n$是任意非负整数时，该算法能够计算$2^n$的值。

```javascript
function power(n) {
    if (n == 0) return 1;
    return 2 * power(n - 1);
}

function power(n, rst) {
    if (n == 0) return rst;
    return power(n - 1, rst * 2);
}
```