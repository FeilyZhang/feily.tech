---
title: "Decrease and conquer algorithm"
date: 2020-04-11T17:48:52+08:00
categories: ["Algorithm"]
tags: ["Decrease and conquer"]
draft: false
---

## 折半查找

对非降序数组元素的查找，通过减常因子实现，效率很高，代码如下

```javascript
function search(arr, l, r, t) {
    if (l <= r) {
        var mid = parseInt((l + r) / 2);
        if (t == arr[mid]) return mid;
        else if (t < arr[mid]) return search(arr, l, mid - 1, t);
        else return search(arr, mid + 1, r, t);
    }
    return -1;
}
```

## 假币问题

在$n$枚外观相同的硬币中，有一枚是假币，已知假币最轻，真币较重，请设计算法求出该假币。

该问题适合减治法求解，减常因子，由于天平一次性可比较任意两组硬币，因此通过减半可以很快发现这枚假币，具体思路如下

+ 若硬币个数为偶数，那么先将硬币分为数目相同的两组，分别计算每组的总重量，第一组大于第二组，那么说明假币在第二组中，递归处理第二组，否则递归处理第一组；
+ 若硬币个数为奇数，那么先将硬币的最后一枚单独提取出来，然后将剩余偶数的硬币分为数目相同的两组，分别计算每组的总重量，若两组相同，那么单独提取出的那一枚一定是假币，否则按情况递归处理某一半；
+ 递归出口一定会存在两种情况，第一种是最终只剩下两枚硬币，那么假币肯定是两枚中的一枚；第二种情况是剩下三枚硬币，那么假币肯定存在于这三者之中。

定义如下函数实现识别假币

+ `odd(l, r)`: 判断某个范围是否为奇数个;
+ `sum(arr, l, r, rst)`: 求某个范围的序列之和;
+ `fakeCoin(arr, l, r)`: 识别假币。

```javascript
function odd(l, r) {
    return (r - l + 1) % 2 != 0;
}

function sum(arr, l, r, rst) {
    while (l <= r) {
        rst += arr[r--];
    }
    return rst;
}

function fakeCoin(arr, l, r) {
    var isOdd = odd(l, r);
    if (!isOdd) {
        var mid = parseInt((l + r) / 2);
        // Compare the last two numbers and get the result.
        if (l == mid) {
            return arr[l] < arr[r] ? l : r;
        // Counterfeit money on the left.
        } else if (sum(arr, l, mid, 0) < sum(arr, mid + 1, r, 0)) {
            return fakeCoin(arr, l, mid);
        // Counterfeit money on the right.
        } else {
            return fakeCoin(arr, mid + 1, r);
        }
    } else {
        var tail = arr[r];
        var mid = parseInt((l + r) / 2);
        // Compare the last three numbers and get the result.
        if ((r - l + 1) == 3) {
            return arr[l] < arr[l + 1] ? l : (arr[l + 1] < arr[r] ? l + 1 : r);
        // Counterfeit money at the end.
        } else if (sum(arr, l, mid, 0) == sum(arr, mid + 1, r, 0)) {
            return tail;
        // Counterfeit money on the left.
        } else if (sum(arr, l, mid, 0) < sum(arr, mid + 1, r, 0)) {
            return fakeCoin(arr, l, mid);
        // Counterfeit money on the right.
        } else {
            return fakeCoin(arr, mid + 1, r);
        }
    }
}
```

## 俄式乘法

俄式乘法或俄式农夫法(Russian peasant method)是两个正整数相乘的非主流算法。假设$n$和$m$是两个正整数，我们要计算他们的乘积，同时，我们用$n$的值作为实例规模的度量标准，这样，如果$n$是偶数，一个规模为原来一半的实例必须要对$n / 2$进行处理，对于该问题较大实例的解和较小实例的解的关系有一个显而易见的公式:

\begin{equation}n \times m = \frac{n}{2} \times 2m\end{equation}

如果$n$是奇数，我们只需要对该公式做轻微的调整

\begin{equation}n \times m = \frac{n - 1}{2} \times 2m + m\end{equation}

通过应用这个公式，以$1 \times m = m$作为算法的停止条件，我们既可以用递归也可以用迭代来实现该算法，计算$n \times m$的乘积，分别如下

```java
public class Russian {

    public static void main(String[] args) {
        System.out.println(rpm(50, 65));    // 3250
        System.out.println(curpm(50, 65, 0));   // 3250
        System.out.println(rpm(65, 50));    // 3250
        System.out.println(curpm(65, 50, 0));   // 3250
    }

    public static long rpm(int n, int m) {
        int a = 0;
        while (n != 1) {
            if (n % 2 == 0) {
                n = n / 2;
                m =  2 * m;
            } else {
                n = (n - 1) / 2;
                a += m;
                m = 2 * m;
            }
        }
        return a + m;
    }
    
    public static long curpm(int n, int m, int a) {
        if (n == 1) return a + m;
        if (n % 2 == 0) return curpm(n / 2, 2 * m, a);
        else return curpm((n - 1) / 2, 2 * m, a + m);
    }
    
}
```

## 以2为底的对数

通过减常因子可以计算以2为底的对数，利用对数运算法则，即

\begin{equation}log_2n = log_2\frac{n}{2} + log_22 = log_2\frac{n}{2} + 1\end{equation}

当$n < 2$时停止递归，所以递归代码如下

```javascript
function log2(n) {
    if (n < 2) return Math.log2(n);
    return log2(n / 2) + 1;
}

log2(5)	// 2.3219280948873626
```

对以2为底的对数结果向下取整，代码为

```javascript
function log2Floor(n, rst) {
    if (n < 2) return Math.floor(Math.log2(n)) + rst;
    return log2Floor(n / 2, rst + 1);
}

log2Floor(5, 0)	// 2
```

## 约瑟夫斯问题

约瑟夫斯问题(Josephus problem)，是以弗拉瓦斯·约瑟夫斯(Flavius Josephus)的名字命名的。约瑟夫斯是一个著名的犹太历史学家，参加并记录了公元33-70年犹太人反抗罗马的起义。约瑟夫斯作为一个将军，设法守住了裘达伯特的堡垒达47天之久，但在城市陷落之后，他和40名顽强的将士在附近的一个洞穴中避难，在那里，这些将士认为投降不如自杀。于是，约瑟夫斯建议每个人应该轮流杀死他旁边的人，而这个顺序是由抽签决定的，而约瑟夫斯有预谋的抓到了最后一签(不是抽签顺序的最后一个，而是杀掉他旁边的人后只剩下他一个人)，并且，作为洞穴中的两个幸存者之一，他说服了他原先的牺牲品一起投降了罗马。

现在问题是，在具有n个人的将士中，按照此规则，哪个人将会是最后的幸存者？对该问题进行建模，首先让n个人围成一个圈，并从1进行编号直至n，然后从编号为1的人开始进行这个残酷的计数，因为是每个人轮流杀死旁边的一个人，因此1号杀死2号然后停止，3号杀死4号然后停止，以此类推，对于$n = 6$的环，杀死的过程如下(某个计数标记为0即杀死该号对应的人)

```
初始状态: 1 2 3 4 5 6
第一次杀: 1 0 3 4 5 6
第二次杀: 1 0 3 0 5 6
第三次杀: 1 0 3 0 5 0
第四次杀: 1 0 0 0 5 0
第五次杀: 0 0 0 0 5 0
```

最终幸存者为5号。因此该问题最终抽象为，对于顺次编号$0 - (n - 1)$的具有n个节点的环，每个节点中0为死亡状态，1为存活状态，然后从0号开始跳跃标记，直至剩下最后一个节点为止，即求解$J(n)$。Java代码如下

```java
public class Josephus {

    public static void main(String[] args) {
        System.out.println(josephus(5));    // 3
        System.out.println(josephus(6));    // 5
        System.out.println(josephus(7));    // 7
        System.out.println(josephus(8));    // 1
        System.out.println(josephus(41));   // 19
    }

    public static int josephus(int n) {
        int[] arr = new int[n];
        for (int i = 0; i < n; i++) {
            arr[i] = 1;
        }
        return mark(arr, n);
    }

    private static int mark(int[] arr, int n) {
        int cur = 0, next = 1, temp = 0;
        while (true) {
            arr[next] = 0;  // Mark it.
            temp = findNext(arr, next + 1, n);
            if (cur != temp) cur = temp;
            else return cur + 1;
            temp = findNext(arr, cur + 1, n);
            if (temp != cur) next = temp;
            else return cur + 1;
        }
    }

    private static int findNext(int[] arr, int base, int n) {
        while (true) {
            if (arr[base % n] == 1) return base % n;
            else base++;
        }
    }
    
}
```

## 猜图片

一个非常流行的解题游戏是这样的：给选手出示42张图片，每行6张，共7行。选手可以给大家做一些是非题，来确定他要寻找的图片，然后进一步要求选手用尽可能少的问题来确定目标图片，给出解决该问题的最有效的算法。

利用减常因子实现该算法的思路为，首先对图片进行顺序编号，从而可以得到一个$7 \times 6$矩阵，矩阵当中，每行非降序排列，且下一行的第一个编号大于上一行的最后一个编号，然后对该矩阵进行搜索，从而确定目标的位置。可以先将二维矩阵映射为一维数组，然后运用折半查找进行搜索，然后再将一维索引映射回去从而得到矩阵的索引。

将二维矩阵映射为一维数组可以利用汇编语言中的段的概念，即二维矩阵的行号可以视为基址，列号可以视为偏移，从而在具有row行col列的二维矩阵中第r行第c列可以等价转化为一维数组的索引$r \times col + c$, $r \times col$表示第r行对应的一维数组始址，$c$表示偏移，从而二者相加便得到一维地址。

将一维索引映射为二维矩阵的索引，不过是二维矩阵索引映射为一维数组索引的反操作，在索引为$ret$的一维数组中，其对应的二维索引为$[ret \div col, ret \mod col]$。代码如下

```java
import java.util.Arrays;

public class GuessPic {

    public static void main(String[] args) {
        int[][] arrs = new int[7][6];
        for (int i = 0; i < arrs.length; i++) {
            for (int j = 0; j < arrs[i].length; j++) {
                arrs[i][j] = i * arrs[i].length + j + 1;
            }
        }
        System.out.println(Arrays.toString(guess(arrs, arrs.length, arrs[0].length, 29)));  // [4, 4]
    }
    
    public static int[] guess(int[][] arrs, int row, int col, int target) {
        int[] arr = new int[row * col];
        // Mapping a two-dimensional matrix to a one-dimensional array。
        for (int i = 0; i < row; i++) {
            for (int j = 0; j < col; j++) {
                arr[i * col + j] = arrs[i][j];
            }
        }
        // Mapping one-dimensional index to two-dimensional index。
        int ret = search(arr, 0, arr.length - 1, target);
        return new int[] {ret / col, ret % col};
    }

    public static int search(int[] arr, int l, int r, int target) {
        if (l <= r) {
            int m = (l + r) / 2;
            if (arr[m] == target) return m;
            else if (arr[m] < target) return search(arr, m + 1, r, target);
            else return search(arr, l, m - 1, target);
        }
        return -1;
    }
    
}
```

## 插值查找

作为减可变规模算法的例子，插值查找同折半查找的前提一致，即保证数组有序，不过折半查找为减常因子算法，其在最坏情况下的时间效率为$\lceil log_2{n + 1} \rceil$。

插值查找假设数组的值线性递增，从另一个角度来说，线性递增意味着元素的索引和该索引对应的元素在二位笛卡尔坐标系中增长，其导数大于等于0(即，要么相邻索引的元素相同，要么相邻索引的元素值递增)，从而可以根据两对端点值绘制一条直线，同样可以求出该直线方程，即两点式，对于端点$(l, arr[l])$和$(r, arr[r])$可得两点式方程，如下

\begin{equation}\frac{x - l}{r - l} = \frac{y - arr[l]}{arr[r] - arr[l]}\end{equation}

从而导出$x$

\begin{equation}x = l + \lfloor\frac{(y - arr[l]) \times (r - l)}{arr[r] - arr[l]} \rfloor\end{equation}

即求得目标值对应的索引，根据计算的索引对应的元素与目标值相比较从而决定是返回还是缩小规模在某一部分继续查找，这是典型的减可变规模算法。代码如下

```java
public class InsertSearch {

    public static void main(String[] args) {
        int[] arr = {6, 17, 22, 29, 42, 51, 56, 62, 68, 73, 83};
        System.out.println(search(arr, 0, arr.length - 1, 56)); // 6
    }

    public static int search(int[] arr, int l, int r, int t) {
        if (t < arr[l] || t > arr[r]) return -1;
        int x = getIndex(arr, l, r, t);
        if (arr[x] == t) return x;
        else if (arr[x] < t) return search(arr, x + 1, r, t);
        else return search(arr, l, x - 1, t);
    }
    
    public static int getIndex(int[] arr, int l, int r, int t) {
        return (int) (l + Math.floor(((t - arr[l]) * (r - l)) / (arr[r] - arr[l])));
    }

}
```

在查找一个包含n个随机键的列表时，平均来说，插值查找的键值比较次数要小于$\lceil log_2{log_2n} + 1 \rceil$次。

## 计算中值和选择问题

选择问题(selection problem)是求一个$n$个数列表的第$k$个最小元素的问题，该数字被称为第$k$个顺序统计量(order statistics)。当然，对于$k = 1$或者$k = n$的情况，我们可以进行一次复杂度为$n$的扫描便可找到最小或者最大元素，但通常情况下，所求的顺序统计量被要求为$k \ne 1$且$k \ne n$，而对于中值问题无非是$k = len / 2$的情况。

一种获得第$k$的顺序统计量的方法是，先对序列进行排序，然后找出第$k$小的元素即可，其效率取决于所选择的排序算法的效率，对于类似于合并排序这样优秀的排序算法来说，其时间复杂度属于$O(nlogn)$。但是显而易见的是，这种做法有点杀鸡用牛刀的感觉，因为该问题要求的是求第$k$小的元素而非排序。我们可以使用一种基于划分(partitioning)的快速选择(quickselect)来实现。

这种方法的基本思想是，先根据划分算法将序列以某一个中轴$p$为基准，该基准左边的元素均小于$p$但不保证有序，基准右边的元素均大于等于$p$依旧不保证有序，然后将该中轴$p$的索引与$k$进行比较，如果中轴$p$的索引$s$满足$s = l + k - 1$那么说明刚好一次划分取得第$k$小的元素，如果$s > l + k - 1$那么说明第$k$小的元素存在于中轴$p$的左半边，递归处理左半边，否则递归处理右半边，其中$l$为子数组的起始位置。这里使用一种称为Lomuto的划分算法来实现快速选择，如下

```java
import java.util.Arrays;

public class Quickselect {

    public static void main(String[] args) {
        int[] arr = initArray(11);
        int[] arrCopy = Arrays.copyOf(arr, arr.length);
        System.out.println("before sort : " + Arrays.toString(arr));
        quickSort(arr, 0, arr.length - 1);
        System.out.println("after sort : " + Arrays.toString(arr));
        int n = 3;
        System.out.println("The No " + n + " small element is " + "" + select(arrCopy, 0, arrCopy.length - 1, 3));
    }

    public static int lomuto(int[] arr, int l, int r) {
        int p = arr[l];
        int s = l;
        for (int i = l + 1; i <= r; i++) {
            if (arr[i] < p) {
                s++;
                swap(arr, s, i);
            }
        }
        swap(arr, l, s);
        return s;
    }
    
    public static int select(int[] arr, int l, int r, int k) {
        int s = lomuto(arr, l, r);
        if (s == l + k - 1) return arr[s];
        else if (s > l + k - 1) return select(arr, l, s - 1, k);
        else return select(arr, s + 1, r, l + k - 1 - s);
    }
    
    public static void quickSort(int[] arr, int l, int r) {
        if (l <= r) {
            int s = lomuto(arr, l, r);
            quickSort(arr, l, s - 1);
            quickSort(arr, s + 1, r);
        }
    }
    
    public static void swap(int[] arr, int s, int i) {
        int t = arr[s];
        arr[s] = arr[i];
        arr[i] = t;
    }
    
    public static int[] initArray(int length) {
        int[] arr = new int[length];
        for (int i = 0; i < length; i++) {
            arr[i] = (int) Math.floor(Math.random() * 100);
        }
        return arr;
    }
    
}
```
为了便于对照，采用了基于Lomuto划分的快速排序。输出如下

```
before sort : [38, 35, 59, 68, 76, 48, 3, 58, 4, 19, 17]
after sort : [3, 4, 17, 19, 35, 38, 48, 58, 59, 68, 76]
The No 3 small element is 17
```

采用Lomuto划分算法解决了一种更一般性的问题，即得出了给定列表的$k$个最小元素和$n - k$个最大元素，而不仅仅是列表的第$k$小元素的值。

## 生成子集

背包问题的解就是解空间集合的一个子集，因此采用穷举查找算法逐个搜索解空间便可找到满足条件的子集，这里使用减一思想枚举一个集合的所有子集。

对于集合$A = \lbrace a_1, \cdots, a_n \rbrace$，其子集可以分为两组：不包含$a_n$的子集和包含$a_n$的子集，前一组实际上就是$\lbrace a_1, \cdots, a_{n - 1} \rbrace$的所有子集，而后一组中的每个元素都可以通过把$a_n$添加到$\lbrace a_1, \cdots, a_{n - 1} \rbrace$的一个子集中来获得。因此，我们一旦得到了$\lbrace a_1, \cdots, a_{n - 1} \rbrace$的所有子集列表，我们就可以把列表中的每一个元素加上$a_n$，然后再整体添加至原列表中，便可得到$\lbrace a_1, \cdots, a_n \rbrace$的所有子集。代码如下

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class Subset {

    public static void main(String[] args) {
        List<Integer[]> subset = getSubset(new int[] {1, 2, 3});
        for (Integer[] ints : subset) {
            System.out.print(Arrays.toString(ints));    // null[1][2][1, 2][3][1, 3][2, 3][1, 2, 3]
        }
    }
    
    public static List<Integer[]> getSubset(int[] arr) {
        List<Integer[]> pre = new ArrayList<Integer[]>();
        pre.add(null);
        for (int i = 0; i < arr.length; i++) {
            List<Integer[]> cur = new ArrayList<Integer[]>();
            cur.add(new Integer[] {arr[i]});
            for (int j = 0; j < pre.size(); j++) {
                if (pre.get(j) != null) {
                    Integer[] temp = new Integer[pre.get(j).length + 1];
                    System.arraycopy(pre.get(j), 0, temp, 0, pre.get(j).length);
                    temp[temp.length - 1] = arr[i];
                    cur.add(temp);
                }
            }
            pre.addAll(cur);
        }
        return pre;
    }
    
}
```

## 生成排列

采用自底向上的最小变化算法，非递归生成排列，代码如下

```java
import java.util.ArrayList;
import java.util.List;

public class Permutation {

    public static void main(String[] args) {
        System.out.println(getPermutation("12").size());  // 2
        System.out.println(getPermutation("123").size()); // 6
        System.out.println(getPermutation("1234").size());    // 24
        System.out.println(getPermutation("12345").size());   // 120
    }
    
    public static List<String> getPermutation(String origin) {
        char[] c = origin.toCharArray();
        List<String> pre = new ArrayList<String>();
        pre.add(String.valueOf(c[0]));
        for (int i = 1; i < c.length; i++) {
            List<String> cur = new ArrayList<String>();
            for (int j = 0; j < pre.size(); j++) {
                char[] c1 = pre.get(j).toCharArray();
                int k = c1.length;
                while (true) {
                    if (k == -1) break;
                    cur.add(String.valueOf(insertAndGet(c1, k--, c[i])));
                }
            }
            pre.clear();
            pre.addAll(cur);
            cur.clear();
        }
        return pre;
    }

    public static char[] insertAndGet(char[] c, int addr, char dat) {
        char[] cn = new char[c.length + 1];
        System.arraycopy(c, 0, cn, 0, c.length);
        for (int i = cn.length - 1; i > addr; i--) {
            cn[i] = cn[i - 1];
        }
        cn[addr] = dat;
        return cn;
    }
}
```