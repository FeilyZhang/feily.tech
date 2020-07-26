---
title: "Lists Iteration and Recursion"
date: 2020-04-01T20:23:14+08:00
categories: ["Racket"]
tags: ["Racket"]
draft: false
---

Racket是Lisp语言的一种方言，Lisp最初代表的是“LISt Processor”，意为列表处理器，因此内置的这种list数据结构依然是该语言的突出特性。

Racket中list函数（过程）用于创建列表，该函数接受任意长度的值，然后返回一个包含该值的列表。示例如下

```lisp
> (list "red" "green" "blue")
'("red" "green" "blue")
```

当然，也可以直接为创建的列表绑定一个标识符

```lisp
> (define color (list "red" "green" "blue"))
> color
'("red" "green" "blue")
```

Racket预定义了一些操作列表的函数，一些示例如下

```lisp
> (length color)	; 列表的长度
3
> (list-ref color 0)	; 根据索引提取列表元素	
"red"
> (append color (list "yellow" "white" "black"))	; 追加列表，形成一个新列表
'("red" "green" "blue" "yellow" "white" "black")
> color
'("red" "green" "blue")
> (define color-ano (append color (list "yellow" "white" "black")))	; 追加列表，形成一个新列表
> color-ano
'("red" "green" "blue" "yellow" "white" "black")
> (reverse color)	; 反转列表
'("blue" "green" "red")
> (member "yellow" color)	; 检查某元素是否为列表的元素
#f
> (member "yellow" color-ano)
'("yellow" "white" "black")
```

上述`member`函数，如果某元素存在于列表之中，则返回以列表中第一次出现该元素为开头直至结尾的所有元素，否则返回`#f`。

## 预定义的list循环

考虑到对list进行迭代操作的必要，Racket预定义了`map`函数，该函数有两类参数，第一类是一个过程，第二类值列表，可以存在多个列表，map函数将会对列表中的每个元素应用输入的过程，然后将过程的返回值重新组合为一个列表，原型如下

```lisp
(map proc lst ...+) → list?
  proc : procedure?
  lst : list?
```

作为示例，此处对上述定义的color列表各元素拼接一个感叹号，然后得到拼接感叹号之后的新列表，如下

```lisp
> (map (lambda (e)
         (string-append e "!"))
       color)
'("red!" "green!" "blue!")
```

需要特别注意的是，当map函数接受多个列表时，这些列表必须具有同样的长度，而且处理这些列表的函数必须接受每个列表的一个参数。如下的示例程序，对color列表中的元素截取

```lisp
> (map (lambda (e i)
         (substring e 0 i))
       color
       (list 2 4 3))
'("re" "gree" "blu")
```

也可以再增加一个列表，用于控制substring的起始索引，如下

```lisp
> (map (lambda (e i j)
         (substring e i j))
       color
       (list 1 2 1)
       (list 2 4 3))
'("e" "ee" "lu")
```

作为map函数的变种，andmap用于判断列表中的所有元素是否符合给定过程的条件，ormap用于判断列表中是否存在元素满足给定过程的条件，示例如下

```lisp
> (andmap string? (list "hello" "world"))
#t
> (andmap string? (list "hello" "world" 2))
#f
> (ormap string? (list "hello" "world" 2))
#t
> (ormap number? (list "hello" "world" 2))
#t
```

filter函数用于过滤满足过程条件的列表元素，并返回新的列表

```lisp
> (filter number? (list "hello" "world" 2 3 4 5))
'(2 3 4 5)
```

一个问题是，如何对一个整型列表数据进行归约操作，比如说求和。在Java之类的非函数式语言中，可以使用循环实现，或者递归，那么对数组进行递归求和的一段js代码如下

```javascript
function sum(i, arr) {
    if (i == 0) return arr[0];
    return arr[i] + sum(i - 1, arr);
}

console.log(sum(2, [1, 2, 3]))	// 6
```

上述代码也可以这样写

```javascript
function sum(i, arr, init) {
    if (i == -1) return init;
    return arr[i] + sum(i - 1, arr, init);
}

console.log(sum(2, [1, 2, 3], 0))	// 6
```

至此，很显而易见，init参数提供了求和的初值，为0，此后不断地对每个元素进行迭代操作，最终得出结果。事实上，参数i与arr共同指明了一个元素，即数组中索引为i的元素，所以本质上，sum函数只有两个参数，一个是数组元素，一个是初值。因此，只要具备一个列表中的某个元素与归约操作的初值，就可以进行完整的归约。Racket中直接提供了进行归约操作的函数，其原型为

```lisp
(foldl proc init lst ...+) → any/c
  proc : procedure?
  init : any/c
  lst : list?
```

proc为具体的规约过程，等同于sum函数本身，init为初值，等同于sum函数的init参数，lst为列表，而列表的元素是由proc显式的给出的，那么利用`foldl`进行列表归约求列表`[1, 2, 3]`的和的Racket代码可以写做

```lisp
> (foldl (lambda (ele init)
           (+ ele init))
         0
         '(1 2 3))
6
```

lambda表达式的参数ele和init分别作为递归操作的两个参数，ele对应的实际参数为列表的每个元素，init对应的实际参数为0。其具体的运作过程为，递归的函数体`(+ ele init)`每次计算的值都会被重新赋值给init参数，这样init参数实际上就是已处理过的列表元素之和，自然而然地加上当前的列表元素，经过有限次递归之后就可以得到最终的值。类似地，对列表求积的代码如下

```lisp
> (foldl (lambda (ele init)
           (* ele init))
         1
         '(1 2 3))
6
```

## 如何自定义列表迭代函数

尽管map函数实现了列表迭代的最初定义，但我们显然不满足于此。那么，如何自定义列表迭代呢？

由于列表实际上就是一个链表，所以构建于非空列表(a non-empty list)的两个关键操作是

- first: 获取非空列表的第一个的元素；
- rest: 获取非空列表的除第一个元素之外的其余元素。

示例如下

```lisp
> (first (list 1 2 3 4 5))
1
> (rest (list 1 2 3 4 5))
'(2 3 4 5)
```

这两个关键操作的共性在于其作用的对象必须是一个非空列表，那么如何判断列表是否为空呢？

empty常量用于创建一个空列表，而cons函数用于在已有的列表前面追加元素，cons函数原型如下

```lisp
(cons a d) → pair?
  a : any/c
  d : any/c
```

只有两个参数，参数类型任意。示例如下

```lisp
> empty
'()
> (define ept empty)
> ept
'()
> (cons "head" empty)
'("head")
> (cons "dead" (cons "head" empty))
'("dead" "head")
```

可以使用empty?函数判断列表是否为空，而cons?函数用于判断列表是否为非空。现在我们就可以构造自己的列表迭代函数了。不妨自定义一个计算列表元素个数的函数my-len，如下

```lisp
> (define (my-len lst)
    (cond
      [(empty? lst) 0]
      [else (+ 1 (my-len (rest lst)))]))
> (my-len empty)
0
> (my-len (list 1 2 3 4 5 6))
6
```

上述代码不过就是递归而已，可以用js代码来解释。由于js中没有类似于rest的函数，因此先实现一个rest函数，然后再实现my_len函数

```javascript
function rest(arr) {
    var ret = new Array(arr.length - 1);
    for (var i = 1; i < arr.length; i++) {
        ret[i - 1] = arr[i];
    }
    return ret;
}

function my_len(arr) {
    if (arr.length == 0) return 0;
    return 1 + my_len(rest(arr));
}

my_len([1, 2, 3, 4, 5, 6])	// 6
```

这样就很容易理解了。类似地，我们可以实现map函数的功能，如下

```lisp
> (define (my-map proc lst)
    (cond
      [(empty? lst) empty]
      [else (cons (proc (first lst))
                  (my-map proc (rest lst)))]))
> (my-map string-upcase (list "Feily" "Zhang"))
'("FEILY" "ZHANG")
```

再继续实现andmap和ormap以及filter的功能，如下

```lisp
> (define (my-andmap proc lst)
    (cond
      [(empty? lst) #t]
      [(proc (first lst)) (my-andmap proc (rest lst))]
      [else #f]))
> (my-andmap string? empty)
#t
> (my-andmap number? empty)
#t
> (my-andmap number? (list 1 2 3))
#t
> (my-andmap number? (list 1 2 3 "str"))
#f
> (my-andmap string? (list 1 2 3 "str"))
#f

> (define (my-ormap proc lst)
    (cond
      [(empty? lst) #f]
      [(proc (first lst)) #t]
      [else (my-ormap proc (rest lst))]))
> (my-ormap string? empty)
#f
> (my-ormap number? empty)
#f
> (my-ormap number? '(1 2 3 "Feily" "Zhang"))
#t
> (my-ormap string? '(1 2 3 "Feily" "Zhang"))
#t

> (define (my-filter proc lst)
    (cond
      [(empty? lst) empty]
      [(proc (first lst)) (cons (first lst) (my-filter proc (rest lst)))]
      [else (my-filter proc (rest lst))]))
> (my-filter number? (list "hello" "world" 2 3 4 5))
'(2 3 4 5)
```

## 递归与尾递归优化

递归是Racket进行列表迭代的基本方式，上述递归操作的执行路线为

```lisp
(my-len (list 1 2 3))
= (+ 1 (my-len (list 2 3)))
= (+ 1 (+ 1 (my-len (list 3))))
= (+ 1 (+ 1 (+ 1 (my-len (list '())))))	; 递归边界
= (+ 1 (+ 1 (+ 1 0)))
= (+ 1 (+ 1 1))
= (+ 1 2)
= 3
```

可以看出，上述递归先是递归前进`(my-len (list 1 2 3))`，在触碰到递归边界`(my-len (list '()))`后，然后递归后退`(+ 1 (+ 1 (+ 1 0)))`，最终得出值。这对于一个长度为n的列表，意味着其空间复杂度达到了_O_(_n_)。有一种称为尾递归优化的技术来使得对列表的迭代操作为常量空间，其只有递归前进没有递归后退，在触碰到递归边界后就退出，显然，这种方式需要额外的变量来支持，my-len函数可重写如下

```lisp
> (define (my-len lst)
    (define (inner-len lst len)
      (cond
        [(empty? lst) len]	; 触碰到边界，直接返回额外参数
        [else (inner-len (rest lst) (+ len 1))]))	; 递归前进的同时更新额外参数
    (inner-len lst 0))
> (my-len (list 1 2 3 4 5 6))
6
```

同样的方法，可以将my-map重写如下

```lisp
> (define (my-map proc lst)
    (define (inner-map lst ret)
      (cond
        [(empty? lst) (reverse ret)]	; 触碰到边界，直接返回额外参数
        [else (inner-map (rest lst) (cons (proc (first lst)) ret))]))	; 递归前进的同时更新额外参数
    (inner-map lst empty))
> (my-map string-upcase (list "Feily" "Zhang"))
'("FEILY" "ZHANG")
```

## 递归之于迭代

从某种程度上说，迭代只是递归的一种特例。多数语言中，尝试将尽可能多的计算适应于迭代是非常重要的，换言之，迭代是多数语言在语言层面提供的重要特性（语法），比如for、while、do-while等循环，因为递归的性能相当低下。Racket中对于递归的这一缺陷使用尾递归优化来弥补，这样可以将递归的空间复杂度由_O_(_n_)将到常数级。

但是，在Racket当中，递归并不会导致糟糕的性能，也不存在堆栈溢出。如果运算涉及太多的上下文，则可能耗尽内存，但是相对于其它语言来说，Racket可以承受更深数量级的递归。基于这些考虑，在加上尾递归优化技术，Racket并没有回避递归这一程序设计思维，而是选择拥抱它。