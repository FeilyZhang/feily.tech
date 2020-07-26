---
title: "The essence, structure and application of recursive algorithm"
date: 2020-02-04T10:28:14+08:00
categories: ["Algorithm"]
tags: ["Algorithm"]
draft: false
---

## Introduction

In common algorithm strategies, I always think recursion is full of aesthetic feeling. But if you don't have a deep understanding to it, aesthetic feeling will becomes a headache. So this article will thoroughly introduce recursive algorithm, let's enjoy the beauty of recursive algorithm.

## What's the essence of recursion?

Most articles say that recursion is you call yourself in a function or method. Yes, it's true in form, but what is the purpose of recursion and when should we apply recursion algorithm? That's a good question.

The essence of recursion is loop, and its purpose is to solve the problem of structure self-similarity. But what's the problem of structure self-similarity?

The problem of structure self-similarity refers to the substructure of the structure still keeps the nature of the structure itself, but the scale is different. For example, the following data structures belong to structural self similarity:

> Array, linked list, binary tree, sequence and so on.

Strictly speaking, there is no difference between an array and a sequence, but they are distinguished here for the sake of below smooth writing.

Maybe you have some questions about description above. Don't worry, keep looking down.

## What's the structure of recursion?

After careful analysis, it is found that there are two types of recursive algorithm, each of which has a different structure.

+ Recursion for a certain value(RfV): In general, there is a return value, such as finding the maximum value of an array or the sum of array elements.
+ Recursion to entire structure(RtS): Generally, there is no return value, which is directly operated on the object, such as array inversion, clearing binary tree, etc.

For RfV, the common program structure is as follows.

```
/*
 * @param obj A data structure or data which contains data.
 * @param idx Index of element, which can be used to extract element and recursive boundary. It's an optional parameter.
 * @return The final result, such as the maximum.
 */
def RfV(obj, opt(idx))
    if isBoundary
        return current value
    pre = RfV(obj, opt(idx + step size))
    some necessary operations related to "pre" 
    return current value
```

Now, let's solve an algorithm problem.

> Given an array, the maximun and sum are calculated by recursion algorithm.

```java
package tech.feily.acm_icpc.recur;

/*
 * @author Feily Zhang
 */
public class ArrMax {
    
    public static int max(int[] arr, int i) {
        if (i == 0) return arr[0];
        int pre = max(arr, i - 1);
        return pre > arr[i] ? pre : arr[i];
    }
    
    public static int sum(int[] arr, int i) {
        if (i == 0) return arr[0];
        return sum(arr, i - 1) + arr[i];
    }
    
    public static void main(String[] args) {
        int[] arr = {8, 3, 2, 9, 7, 1, 5, 4};
        System.out.println(max(arr, arr.length - 1));
        System.out.println(sum(arr, arr.length - 1));
    }

}
```

Can we solve above RfV problem without return statement? Of course, but we can only solve it through object reference. As shown below.

```java
package tech.feily.acm_icpc.recur;

/*
 * @author Feily Zhang
 */
public class ArrMax1 {


    class Int {
        private int val;
        public void setVal(int val) {
            this.val = val;
        }
        public int getVal() {
            return val;
        }
    }
    
    public static void max(int[] arr, int i, Int j) {
        if (i != arr.length) {
            if (i == 0) {
                j.setVal(arr[0]);
                max(arr, i + 1, j);
            }
            if (arr[i] > j.getVal()) {
                j.setVal(arr[i]);
                max(arr, i + 1, j);
            } else max(arr, i + 1, j);
        }
    }
    
    public static void sum(int[] arr, int i, Int j) {
        if (i != arr.length) {
            if (i == 0) {
                j.setVal(arr[0]);
                sum(arr, i + 1, j);
            } else {
                j.setVal(arr[i] + j.getVal());
                sum(arr, i + 1, j);
            }
        }
    }
    
    public static void main(String[] args) {
        int[] arr = {8, 3, 2, 9, 7, 1, 5, 4};
        Int j = new ArrMax1().new Int();
        max(arr, 0, j);
        System.out.println(j.getVal());
        j.setVal(0);    // clear object j
        sum(arr, 0, j);
        System.out.println(j.getVal());
    }

}
```

Through above statements, we can easily find that RfV problem have two types program structure. One is realized by return value, the other by object reference. The main step of the former is to advance recursively first, return recursively and deal with it after touching the recursion boundary, and the latter is to move forward recursively while processing, until it touches the recursion boundary.

Because I said that the essence of recursion is loop, so recursive algorithm can be realized by "for" loop equivalently. The initial value, cycle condition and step size of cycle variable of the "for" loop can easily get by analyzing recursion algorithm.

For RtS, the common program structure is as follows.

```
def RtS(obj, opt(idx))
    if !isBoundary
        do something
        RtS(obj, opt(idx + step size))
```

Now, let's solve an algorithm problem.

> Invert the elements of a given array.

```java
package tech.feily.acm_icpc.recur;

import java.util.Arrays;

/*
 * @author Feily Zhang
 */
public class Invert {

    public static void invert(int[] arr, int l, int r) {
        if (l < r) {
            arr[l] = arr[l] + arr[r];
            arr[r] = arr[l] - arr[r];
            arr[l] = arr[l] - arr[r];
            invert(arr, l + 1, r - 1);
        }
    }
    
    public static void main(String[] args) {
        int[] arr = {8, 3, 2, 9, 7, 1, 5, 4};
        System.out.println(Arrays.toString(arr));
        invert(arr, 0, arr.length - 1);
        System.out.println(Arrays.toString(arr));
    }

}
```

The above method solve the RtS problem by processing before recursion. Can we solve it by processing after recursion? Of course.

```java
package tech.feily.acm_icpc.recur;

import java.util.Arrays;

/*
 * @author Feily Zhang
 */
public class Invert1 {

    public static void invert(int[] arr, int l, int r) {
        if (l < r) {
            invert(arr, l + 1, r - 1);
            arr[l] = arr[l] + arr[r];
            arr[r] = arr[l] - arr[r];
            arr[l] = arr[l] - arr[r];
        }
    }

    public static void main(String[] args) {
        int[] arr = {8, 3, 2, 9, 7, 1, 5, 4};
        System.out.println(Arrays.toString(arr));
        invert(arr, 0, arr.length - 1);
        System.out.println(Arrays.toString(arr));
    }
    
}
```

## Sequence, a special structure self-similarity

The reason why sequence is a special issue of structure self-similarity is its elements are calculated by formula, so its structure self-similarity is not as obvious as array and linked list. Let's feel it through a question.

> Output digit by digit from high to low, and there is a space after each number.

```java
package tech.feily.acm_icpc.recur;

/*
 * @author Feily zhang
 */
public class Output {

    public static void output(int num) {
        if (num < 10) System.out.print(num + " ");
        else {
            output(num / 10);
            System.out.print(num % 10 + " ");
        }
    }

    public static String output1(int num) {
        if (num < 10) return num + " ";
        String s = output1(num / 10);
        return s + num % 10 + " ";
    }
    
    public static void main(String[] args) {
        output(13579);
        System.out.println("\n" + output1(13579));
    }

}
```

In fact, the sequence given by `13579 / 10` constitutes an array of elements `[13579, 1357, 135, 13, 1]`. Because the elements are computed in real time, the index can be omitted.