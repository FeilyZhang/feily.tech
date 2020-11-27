---
title: "Love guide to backtracking algorithm"
date: 2020-11-26T19:26:05+08:00
categories: ["Backtracking"]
tags: ["Backtracking"]
draft: false
---

@FeilyZhang微信未读消息累计$594$条，遂突发奇想未读消息序列能否凑成自然数$520$，以及能够凑成多少个$520$，以此tell小可爱🐕😘🐖。于是便有了这篇文章，其本质上是子集求和问题。本文采用回溯算法的思想搜索解空间，通过多种剪枝策略减少不必要的解空间搜索。

### **问题形式化**

该问题的解空间可形式化为$n$元组$X = (X_1, X_2, \dots, X_n)$，其中$X_i = (x_1, x_2, \dots, x_m)$, $i = (1, 2, \dots, n)$, $x_j \in S$, $j = (1,2,\dots,m)$,$m \le |S|$，$S$为有限自然数集。从而$n$元组$X$中满足如下约束条件的元素构成该问题的一个解

\begin{equation} \sum_{i = 1}^{m} x_i = t, t = 520\tag{1}\end{equation}

设当前已满足约束条件$(1)$式的部分解为元组$P = (X_1, X_2, \dots, X_i)$，若存在$n$元组$X$中的元素满足$(1)$式，则将其添加进元组$P$中构成该问题的另一个解，即$P = (X_1, X_2, \dots, X_i, X_{i + 1})$，如此反复进行，直至$n$元组$X$中不再存在该问题的有效解或证明该问题无解为止。

### **剪枝策略**

由于子集问题解向量的维数$d$不固定，极端情况下存在满足约束条件$(1)$式的$1$维或$n$维解向量（其中$n$为有限自然数集$S$的元素个数），但多数情况下解向量的维数$d$满足$d \in (1, n)$，受有限自然数集$S$元素的不同数量的组合方式的影响，该问题的解空间异常庞大，且解空间中解的数目随有限自然数集$S$元素个数的增加呈指数级增长。因此解空间的搜索必须辅之以一定的剪枝策略，用以提升算法性能。具体的剪枝策略如下

+ 预剪枝：该算法最外层的遍历方向为反向遍历，即方向为$(length - 1) \to 0$，从而通过对排序后的序列正向累计求和与$t$相比较来确定反向遍历的终止条件。若正向累计和大于等于$t$则返回上一个元素的索引作为终止条件；若正向累计求和最终仍然小于$t$，则返回$-1$，算法终止。该剪枝策略算法如下

```java
/**
 * This method realizes pre pruning.
 */
public boolean isPrePruning(int end) {
    return end == -1 ? true : false;
}

public int calEnd(int[] arr, int i, int t, int rst) {
    return i == -1 ? -1 : rst >= t ? i - 1 : calEnd(arr, i + 1, t, rst + arr[i]);
}
```

+ 运行时剪枝：在算法运行时，通过判断当前反向推进的元素索引之后的所有值的和是否大于等于做差后的$t$，来决定是否剪枝。若大于等于$t$，则继续推进当前解的搜索；若小于$t$，则算法回溯搜索下一个解。该剪枝策略算法如下

```java
/**
 * This method implements runtime pruning.
 */
public boolean isRunPruning(int sum, int t) {
    return sum < t ? true : false;
}

public int calSum(int[] arr, int begin, int sum) {
    return begin == 0 ? sum + arr[begin] : calSum(arr, begin - 1, sum + arr[begin]);
}
```

### **迭代搜索算法**

对于解空间$n$元组$X$，本文采用回溯算法的思想进行迭代搜索，其中回溯通过栈来实现。算法图示如下

![迭代搜索算法图示](https://raw.githubusercontent.com/FeilyZhang/pic/master/images/article/egg.png)

1. 步骤①为最外层循环，先反向固定元素，从而当固定元素索引满足预剪枝条件时算法终止；
2. 步骤②正向将截至当前固定元素索引的所有索引依次入栈，该栈作为回溯栈；
3. 步骤③回溯元素按顺序依次出栈，通过基于当前出栈元素索引的反向迭代来做差，从而容易通过做差后结果是否为0来判断能否搜索到解，其终止策略如下
   + 若当前固定元素及出栈元素约束下的子空间无解，则当前回溯元素反向迭代终止，继续处理下一个回溯元素；
   + 若找到一个有效解，则当前回溯元素反向迭代终止，继续处理下一个回溯元素；
   + 若满足运行时剪枝条件，则当前回溯元素反向迭代终止，继续处理下一个回溯元素；
4. 直至回溯栈为空，然后反向固定当前元素的下一个元素，继续重复上述过程。

上述算法过程的代码实现如下

```java
import java.util.*;

public class SumOfSubsets {

    private int target;
    private Deque<Integer> os = new LinkedList<>();
    private Set<List<Integer>> rst = new HashSet<>();
    private List<Integer> temp = new LinkedList<>();

    public Set<List<Integer>> findAll(int[] arr, int t) {
        int end = calEnd(arr, 0, t, 0);
        if (isPrePruning(end)) return null;    // pre pruning.
        Arrays.sort(arr);
        for (int i = arr.length - 1, target = t; i >= end; i--) {
            if (arr[i] > target) continue;
            os = fillOS(i - 1, 0, os);
            while (!os.isEmpty()) {
                target -= arr[i];
                temp.add(arr[i]);
                for (int j = os.pop(); j > -1; j--) {
                    if (isRunPruning(calSum(arr, j, 0), target)) break;    // runtime pruning.
                    if (arr[j] > target) continue;
                    if (target == 0) break;
                    if (j == 0) os.clear();
                    target -= arr[j];
                    temp.add(arr[j]);
                }
                if (target == 0) rst.add(temp);
                temp = new LinkedList<>();
                target = t;
            }
        }
        return rst;
    }

    /**
     * This method is used to fill the backtrace stack.
     */
    public Deque<Integer> fillOS(int end, int i, Deque<Integer> offset) {
        if (i == end + 1) return offset;
        offset.push(i);
        return fillOS(end, i + 1, offset);
    }

    // The pruning algorithm is omitted here.

}
```

### **彩蛋·回到主题**

$594$条微信消息序列为[3, 3, 13, 1, 12, 3, 13, 5, 11, 4, 5, 2, 1, 1, 12, 14, 4, 6, 8, 12, 5, 3, 13, 3, 11, 8, 13, 1, 11, 6, 8, 2, 12, 5, 9, 3, 2, 14, 12, 4, 10, 3, 12, 1, 9, 8, 11, 10, 1, 11, 5, 9, 2, 6, 1, 3, 1, 11, 2, 8, 9, 8, 2, 8, 3, 10, 9, 2, 5, 12, 9, 11, 13, 2, 6, 11, 2, 5, 3, 4, 1, 8, 3, 6, 5, 1, 5, 1, 5, 1, 6, 1, 2, 4, 1, 1, 1, 1, 1, 1, 1, 2]，对该消息序列应用上述算法，并进行一定程度的消息封装，如下

```java
public class I {

    private int target;
    private int[] set = new int[]{3, 3, 13, 1, 12, 3, 13, 5, 11, 4, 5, 2, 1, 1, 12, 14, 4, 6, 8, 12, 5, 3, 13, 3, 11, 8, 13, 1, 11, 6, 8, 2, 12, 5, 9, 3, 2, 14, 12, 4, 10, 3, 12, 1, 9, 8, 11, 10, 1, 11, 5, 9, 2, 6, 1, 3, 1, 11, 2, 8, 9, 8, 2, 8, 3, 10, 9, 2, 5, 12, 9, 11, 13, 2, 6, 11, 2, 5, 3, 4, 1, 8, 3, 6, 5, 1, 5, 1, 5, 1, 6, 1, 2, 4, 1, 1, 1, 1, 1, 1, 1, 2};
    private String who;
    private double from;
    private int to;

    public I say(int t) {
        target = t;
        return this;
    }

    public I to(String w) {
        who = w;
        return this;
    }

    public I from(double f) {
        from = f;
        return this;
    }

    public void to(int t) {
        int rst = new SumOfSubsets().findAll(set, target).size();
        System.out.println("If the full score of love for you is 10, then I must be. Is 10 ? " + (rst == 10));
    }

    public static void main(String[] args) {
        new I().say(520).to("@YuanLing").from(9.22).to(999);
    }

}
```

输出为

```
If the full score of love for you is 10, then I must be. Is 10 ? true
```

Do you know what I mean?