---
title: "Introduction to The Design and Analysis of Algorithms(1)"
date: 2020-04-05T21:28:07+08:00
categories: ["Algorithm"]
tags: ["Algorithm"]
draft: false
---

## 欧几里得算法

假定输入的第一个参数不小于第二个参数，其递推公式为

\begin{equation}gcd(m, n)=gcd(n, m\ mod\ n)\end{equation}

当n为0时，m即为最大公约数，代码如下

```javascript
function gcd(fst, scd) {
    if (scd == 0) return fst;
    return gcd(scd, fst % scd);
}
```

## 原版欧几里得算法

在欧几里得的书中，欧几里得算法用的并不是除法，而是减法，其递推公式为

\begin{equation}gcd(m, n)=gcd(m\ -\ n, n)\end{equation}

从而在m小于等于n时，结束递归，代码如下

```javascript
function gcd(fst, scd) {
    if (fst <= scd) return fst;
    return gcd(fst - scd, scd);
}
```

另一种方法是减法实现求余操作，从而计算最大公约数，如下

```javascript
function mod(a, b) {
    while (a >= b) {
        a = a - b;
    }
    return a;
}

function gcd(fst, scd) {
    if (scd == 0) return fst;
    return gcd(scd, mod(fst, scd));
}
```

## 找相同元素

给定两个非降序列表，输出其相同的元素，例如`[2, 5, 5, 5]`, `[2, 2, 3, 5, 5, 7]`中输出`2, 5, 5`，代码如下

```javascript
function samele(arr1, arr2) {
    var i = 0, j = 0;
    while (i < arr1.length || j < arr2.length) {
        if (arr1[i] == arr2[j]) {
            console.log(arr1[i]);
            i++, j++;
        } else if (arr1[i] > arr2[j]) {
            j++;
        } else {
            i++;
        }
    }
}
```

## 对开方后元素取整

首先是对开方后元素向下取整，不使用库函数，如下

```javascript
function floor(n) {
    if (n == 0) return 0;
    if (n < 4) return 1;
    for (var i = 2, pre = 0; i < n; i++) {
        if (i * i == n) return i;
        if (i * i < n) pre = i;
        if (i * i > n) return pre;
    }
}
```

其次是开方后元素向上取整，不使用库函数，如下

```javascript
function ceil(n) {
    if (n == 0) return 0;
    if (n < 4) return 2;
    for (var i = 2; i < n; i++) {
        if (i * i == n) return i;
        if (i * i > n) return i;
    }
}
```

## 埃拉托色尼筛选法

该方法可能是古希腊人发明的，用以产生一个不大于给定整数n的连续质数序列。其具体方法为

1. 先初始化一个2 ~ n的连续整数序列，作为候选质数；
2. 分别消去序列中2 ~ n的倍数，以2为例，消去4、6、8...，但是不消去2，以此类推；
3. 最终得到质数序列。

事实上，消去某数的倍数并不需要循环到n，因为某些数已经被消掉了，比如4、6、8已经在消除2的倍数时消掉了。更进一步地，只需要循环到`floor(sqrt(n))`即可，代码如下

```javascript
function siece(n) {
    var seq = new Array(n + 1);
    for (var i = 2; i <= n; i++) {
        seq[i] = i;
    }
    for (var j = 2, k = 0; j <= Math.floor(Math.sqrt(n)); j++) {
        k = j * j;
        while (k <= n) {
            seq[k] = 0;
            k += j;
        }
    }
    var len = 0, i = 0;
    var ret = new Array(n + 1);
    for (var j = 2; j <= n; j++) {
        if (seq[j] != 0) {
            ret[i++] = seq[j];
            len++;
        }
    }
    return ret.slice(0, len);
}

siece(25);  // (9) [2, 3, 5, 7, 11, 13, 17, 19, 23]
```

## 海伦公式计算三角形面积

若已知三角形三边a, b, c, 那么海伦公式如下

\begin{equation}S=\sqrt{p(p - a)(p - b)(p - c)},\ p = (a + b + c) / 2\end{equation}

从而计算三角形面积的算法如下

```javascript
function helen(a, b, c) {
    var p = (a + b + c) / 2.0;
    return Math.sqrt(p * (p - a) * (p - b) * (p - c));
}
```

## 计算序列中元素间最小值

```javascript
function minDistance(arr) {
    var min = Number.MAX_VALUE;
    for (var i = 0; i < arr.length; i++) {
        for (var j = 0; j < arr.length; j++) {
            if ( i != j && Math.abs(arr[i] - arr[j]) < min) {
                min = Math.abs(arr[i] - arr[j]);
            }
        }
    }
    return min;
}
```

## 非降序排列

该算法的思想是，先初始化一个与待排序序列同样长度的数组，然后通过双层循环扫描序列，统计小于当前元素的元素个数，然后将其记录在开头初始化的另一个数组中，扫描完毕后，根据开头初始化的数组信息进行排序。该方法在某种程度上类似于桶排序，但在空间消耗上优于桶排序。

```javascript
function sort(arr) {
    var count = new Array(arr.length);
    for (var i = 0; i < count.length; i++) {
        count[i] = 0;
    }
    for (var i = 0; i < arr.length - 1; i++) {
        for (var j = i + 1; j < arr.length; j++) {
            if (arr[i] > arr[j]) {
                count[i]++;
            } else {
                count[j]++;
            }
        }
    }
    var ret = new Array(arr.length);
    for (var i = 0; i < count.length; i++) {
        ret[count[i]] = arr[i];
    }
    return ret;
}

sort([60, 35, 81, 98, 14, 47])  // (6) [14, 35, 47, 60, 81, 98]
```

## 字符串匹配

```javascript
function match(text, pattern) {
    for (var i = 0; i <= text.length - pattern.length; i++) {
        for (var j = 0; j < pattern.length; j++) {
            if (text[i + j] != pattern[j]) {
                break;
            } else if (j == pattern.length - 1) {
                return true;
            }
        }
    }
    return false;
}
```