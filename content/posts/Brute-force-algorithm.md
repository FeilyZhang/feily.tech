---
title: "Brute force algorithm"
date: 2020-04-10T21:26:27+08:00
categories: ["Algorithm"]
tags: ["Brute force"]
draft: false
---

## 选择排序

```javascript
function selectionSort(arr) {
    var minIndex;
    for (var i = 0; i < arr.length - 1; i++) {
        minIndex = i;
        for (var j = i + 1; j < arr.length; j++) {
            if (arr[j] < arr[minIndex]) {
                minIndex = j;
            }
        }
        if (minIndex != i) {
            arr[i] = arr[i] + arr[minIndex];
            arr[minIndex] = arr[i] - arr[minIndex];
            arr[i] = arr[i] - arr[minIndex];
        }
    }
    return arr;
}
```

该算法的递归版本为

```javascript
function recurSelectionSort(arr, n) {
    if (n == 0) return arr;
    for (var i = 0; i < n; i++) {
        if (arr[i + 1] < arr[i]) {
            arr[i + 1] = arr[i] + arr[i + 1];
            arr[i] = arr[i] - arr[i + 1];
            arr[i + 1] = arr[i] - arr[i + 1];
        }
    }
    recurSelectionSort(arr, n - 1);
}
```
