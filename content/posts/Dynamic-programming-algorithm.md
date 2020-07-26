---
title: "Dynamic programming algorithm"
date: 2020-04-15T10:47:37+08:00
categories: ["Algorithm"]
tags: ["Dynamic programming"]
draft: false
---

## 最值问题

对于最大值问题，常规解法是对序列进行一趟扫描，从而找到最大值，该问题依旧适合动态规划来解。对于长度为$n$的序列$arr[1..n]$，定义$F[n]$为长度为$n$的序列的最大值，由于最大值一定存在于$F[n - 1]$和$arr[n]$之间，那么可以得到如下状态转移方程

\begin{equation}F[n] = max \lbrace F[n - 1], arr[n] \rbrace, \ n > 0\end{equation}

\begin{equation}F[0] = arr[0]\end{equation}

上述状态转移方程需要记录$1$到$n$的所有最大值$F$，事实上，我们可以只使用一个变量$max$追踪状态变化而无须声明状态序列，新的状态转移方程如下

\begin{equation}max = max \lbrace max, arr[n - 1] \rbrace, \ n > 0\end{equation}

\begin{equation}max = arr[0]\end{equation}

```java
public class DpMax {

    public static void main(String[] args) {
        int[] arr = {2, 7, 1, 6, 4, 9, 2, 3};
        System.out.println(getMax(arr));    // 9
        System.out.println(getMax1(arr));   // 9
    }

    public static int getMax(int[] arr) {
        int max = arr[0];
        for (int a : arr) {
            max = Math.max(max, a);
        }
        return max;
    }
    
    public static int getMax1(int[] arr) {
        int[] f = new int[arr.length];
        f[0] = arr[0];
        for (int i = 1; i < arr.length; i++) {
            f[i] = Math.max(f[i - 1], arr[i]);
        }
        return f[f.length - 1];
    }
    
}
```

同时，最小值的解法与最大值类似。

## 币值最大化问题

给定一排$n$个硬币，其面值均为正整数$c_1, c_2, \dots , c_n$，这些正整数并不一定两两相同。请问如何选择硬币，使得在原始位置互不相邻的条件下，所选硬币的总金额最大。

首先定义状态，定义$F(n)$为序列中$n$个硬币在原始位置互不相邻条件下的最大金额。由于条件是硬币的原始位置互不相邻，也就是说，对于序列$1, 2, 5, 8, 1$来说，在$n = 0$时，$F(0) = 0$；在$n = 1$时，$F(1) = 1$；在$n = 2$时，$F(2) = 2$；在$n = 3$时，$F(3) = 6$等，因为在$n = 2$时，在原始位置互不相邻的条件下，$F(n)$要么选择$n = 1$时的$F(1)$，要么选择$n = 0$时的$F(0)$与$c_2$之和，只有这么做，才能满足$c_1$和$c_2$原始位置互不相邻的条件。因此可得状态转移方程

\begin{equation}F[n] = max \lbrace c_n + F(n - 2), F(n - 1) \rbrace, \ n > 1\end{equation}

\begin{equation}F[0] = 0, F[1] = c_1\end{equation}

```java
public class CoinValue {

    public static void main(String[] args) {
        int[] arr = {1, 2, 5};
        int[] arr1 = {5, 1, 2, 10, 6, 2};
        System.out.println(coinMax(arr));   // 6
        System.out.println(coinMax(arr1));  // 17
    }

    public static int coinMax(int[] coin) {
        int[] newCoin = new int[coin.length + 1];
        System.arraycopy(coin, 0, newCoin, 1, coin.length);
        int[] f = new int[newCoin.length];
        f[1] = newCoin[1];
        for (int i = 2; i < newCoin.length; i++) {
            f[i] = Math.max(f[i - 2] + newCoin[i], f[i - 1]);
        }
        return f[newCoin.length - 1];
    }
    
}
```

## 最长上升子序列

给定一个序列，长度为$n$，求这个序列的最长上升子序列的长度。

定义$F(n)$为长度为$n$的序列$A$中以第$n$项为结尾的最长上升子序列的长度，由于该问题满足最优子结构性质，则状态转移方程可表示为

\begin{equation}F(n) = max \lbrace F(i) \| A_i < A_n, i \in (1 \dots n - 1) \rbrace + 1, n > 1\end{equation}

\begin{equation}F(0) = 0, F(1) = 1\end{equation}

我们最终的目标是求

\begin{equation}max \lbrace F(1) \dots F(n) \rbrace \end{equation}

```java
public class SubArr {

    public static void main(String[] args) {
        int[] arr = {1, 7, 2, 8, 3, 4};
        System.out.println(longest(arr));   // 4
    }

    public static int longest(int[] arr) {
        int[] newArr = new int[arr.length + 1];
        int[] f = new int[newArr.length];
        System.arraycopy(arr, 0, newArr, 1, arr.length);
        f[1] = 1;
        for (int i = 2; i < newArr.length; i++) {
            f[i] = max(f, newArr, i) + 1;
        }
        return max(f);
    }

    private static int max(int[] f) {
        int rst = 0;
        for (int i = 0; i < f.length; i++) {
            if (f[i] > rst) rst = f[i];
        }
        return rst;
    }

    private static int max(int[] f, int[] arr, int n) {
        int rst = 0;
        for (int i = 1; i < n; i++) {
            if (f[i] > rst && arr[i] < arr[n]) rst = f[i];
        }
        return rst;
    }
    
}
```

## 找零问题

现有面额为$d_1, d_2, \dots, d_m$的硬币，其满足$d_1 < d_2 < \dots < d_m$，对于需找零金额$n$，最少需要几枚硬币？

一种解决办法是使用贪心算法，每次都对$n$减去最大的面值，直至$n = 0$时停止，代码如下

```java
public class Coin {

    public static void main(String[] args) {
        int[] coin = {1, 3, 4};
        System.out.println(greedy(coin, 6));    // 3
    }

    public static int greedy(int[] coin, int n) {
        int i = coin.length - 1;
        int rst = 0;
        while (n != 0) {
            if (n >= coin[i]) {
                n -= coin[i];
                rst++;
            } else i--;
        }
        return rst;
    }
    
}
```

贪心算法的解为$3$，显然不是最优解，因为如果使用面值为$3$的硬币对$n = 6$找零的话，只需要$2$枚即可。该问题的全局最优解适合用动态规划来求，我们定义$F(n)$为总金额为$n$的数量最少的硬币数目，显然获得$n$的途径为：在总金额为$n - d_j$的一堆硬币上加入一个面值为$d_j$的硬币，其中$j = 1, 2, \dots, m$，并且$n \ge d_j$。因此，我们只需要考虑所有满足要求的$d_j$并选择使得$F(n - d_j) + 1$最小的$d_j$即可，由于$1$是常量，所以我们先找出最小的$F(n - d_j)$，然后加$1$即可。即状态转移方程为

\begin{equation}F(n) = min \lbrace F(n - d_j) \| n \ge d_j \rbrace + 1, n > 0\end{equation}

\begin{equation}F(0) = 0 \end{equation}

```java
public class Coin {

    public static void main(String[] args) {
        int[] coin = {1, 3, 4};
        System.out.println(dp(coin, 6));    // 2
    }

    public static int dp(int[] coin, int n) {
        int[] newCoin = new int[coin.length + 1];
        int[] f = new int[n + 1];
        System.arraycopy(coin, 0, newCoin, 1, coin.length);
        for (int i = 1; i <= n; i++) {
            int temp = Integer.MAX_VALUE, j = 1;
            while (j < newCoin.length && i >= newCoin[j]) {
                temp = Math.min(f[i - newCoin[j++]], temp);
            }
            f[i] = temp + 1;
        }
        return f[n];
    }
    
}
```