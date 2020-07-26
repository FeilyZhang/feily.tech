---
title: "Simple Definitions and Expressions"
date: 2020-03-29T18:10:14+08:00
categories: ["Racket"]
tags: ["Racket"]
draft: false
---

## Racket标识符

Racket对于定义标识符(identifiers)的语法相当宽松，没有像Java、C之类的多个保留字，原则上除过构成数字常量的字符序列外，均可以定义为标识符，即`+`, `-`, `*`, `/`等符号可以随意组合为标识符而不受任何限制，但是数字字面量不可以定义为标识符。例如，如下定义的标识符均合法

```lisp
(define identifiers 5)
(define + 5)
(define _ 5)
(define / 5)
```

而，如下标识符非法

```lisp
(define 5 5)
(define . 5)
```

`.`之所以不能作为标识符，是因为`.`是构成数字字面量的字符(浮点数)。

## Racket过程

Racket过程(_procedure_)在功能上类似于C函数或者Java方法，只不过叫法不同，比如`*`就是一个过程，其用于计算若干个数值之积，`string-append`是进行字符串拼接的过程。

```lisp
> string-append
#<procedure:string-append>
> *
#<procedure:*>
```

## Racket定义

Racket定义(definitions)可以将字面量(数字或者字符(串)等)与标识符绑定，类似于Java当中声明变量并赋值；同样，定义也可以将表达式或者过程与标识符绑定，类似于Java当中定义方法。形式如下

```lisp
( define ‹id› ‹expr› )
```
意为将标识符id绑定到表达式expr的结果上，类似于Java中对变量的定义。而如下的形式则是将标识符绑定到过程上，其本质就是一个函数

```lisp
( define ( ‹id› ‹id›* ) ‹expr›+ )
```

其中`*`与`+`的含义与正则表达式中的一致，前者表示零个或多个标识符`<id>`，后者表示一个或多个表达式`‹expr›`。而`( ‹id› ‹id›* )`中的第一个标识符是过程名，其后的零个或多个标识符为过程参数，`‹expr›+`为函数体。示例如下

```lisp
> (define pi 3.14)
> (define (area r)
    (* pi r r))
```

上述定义了一个圆周率，就是将标识符`pi`与圆周率绑定，然后定义了一个过程`area`，该过程只有一个参数`r`，函数体为计算圆的面积的表达式`(* pi r r)`，就是将过程`(area r)`与函数体`(* pi r r)`绑定，接下来我们就可以使用该过程计算圆的面积，如下

```lisp
> (area 2)
12.56
> (area 5)
78.5
> (area 10)
314.0
```

如下代码则是打印固定的“hello, world!”的一个过程，意味着无参。

```lisp
> (define (print)
    (printf "hello, world!"))
> print
#<procedure:print>
> (print)
hello, world!
```

一个过程的定义可以在函数体内包含多个表达式，在这种情况下，过程被调用时仅有最后一个表达式会被计算并返回，其余的表达式仅仅被计算从而产生一些副作用(side-effect)，例如打印语句，但不会被作为返回值返回。如下

```lisp
(define (bake flavor)
  (printf "preheating oven...\n")
  (string-append flavor " pie"))
 
> (bake "apple")
preheating oven...

"apple pie"
```

## Racket过程应用

Racket过程应用(_procedure applications_)就是函数调用，如上述的计算圆面积的area调用，其他的一些过程应用如下

```lisp
> (string-append "hello" ", " "world")	; 拼接字符串
"hello, world"
> (substring "hello, world" 0 5)	; 截取字符串
"hello"
> (string? "hello, world")	; 判断给定参数是否为字符串
#t
> (string? 3.14)
#f
> (sqrt 64)	; 开方
8
> (* 1 2 3 4)	; 计算若干参数之积
24
> (>= 5 4)	; 第一个参数是否大于等于第二个参数
#t
> (number? 3.14)	; 输入是否为数字
#t
> (equal? 6 6)	; 是否相等
#t
> (equal? 6 6.0)
#f
```

## Racket条件判断

Racket if条件语句(if conditional)的形式如下

```lisp
( if ‹expr› ‹expr› ‹expr› )
```

可见，if条件语句本质上也是一个过程，具有三个参数，第一个参数用于计算布尔值，若为真则执行第二个表达式参数，否则执行第三个表达式参数。

```lisp
> (define (display-large x y)
    (if (>= x y)
        (display x)
        (display y)))
> (display-large 10 9)
10
```

可以使用`and`和`or`将多个逻辑表达式连接，等同于Java中的`&&`和`||`，如下

```lisp
> (define (display-large x y)
    (if (or (> x y) (= x y))
        (display x)
        (display y)))
> (display-large 10 9)
10
```

Racket中的多重判断可以使用cond过程实现，形式如下

```lisp
( cond {[ ‹expr› ‹expr›* ]}* )
```

示例代码如下

```lisp
> (define (grade score)
    (cond [(> score 90) "excellent"]
          [(> score 80) "good"]
          [(> score 60) "pass"]
          [else "fail"]))
> (grade 100)
"excellent"
> (grade 20)
"fail"
```

## 再看过程应用

上述过程应用不过是最简单的一种情况，即过程调用的参数为某种数据类型的值；Racket中另一种形式的过程调用可以直接将表达式或者过程当作参数传入过程当中，基本形式如下

```lisp
( ‹expr› ‹expr›* )
```

如下定义一个二元操作，使用传入的过程对其余两个参数进行操作

```lisp
> (define (op procedure x y)
    (procedure x y))
> (op string-append "hello, " "world")
"hello, world"
> (op * 1 6)
6
```

此种类型的过程应用与条件判断依旧可以结合，以下根据不同参数类型对其进行字符串拼接或者数值的加法

```lisp
> (define (plus x y)
    (if (and (number? x) (number? y)) (+ x y)
        (if (and (string? x) (string? y)) (string-append x y)
            "Format not supported")))
> (plus 1 2)
3
> (plus 1 "hi")
"Format not supported"
> (plus "hello, " "world.")
"hello, world."
```

## 匿名函数与lambda表达式

有些使用频率不高的函数没必要完整的进行一次定义，对于这种情况我们可以直接使用lambda表达式来产生一个匿名函数(anonymous functions)，这种情况一般就是函数本身是作为参数传递的，这便是lambda表达式的用武之地。带lambda表达式的匿名函数如下定义

```lisp
( lambda ( ‹id›* ) ‹expr›+ )
```

其中，`‹id›*`为函数参数，`‹expr›+`为函数体。示例如下

```lisp
> (define (twice f v)
    (f (f v)))
> (twice (lambda (s) (string-append s "!")) "hello")
"hello!!"
```

上述过程调用的含义应该通过该过程的函数体来解释。首先，过程调用的第二个参数`hello`先传递给了lambda表达式的函数体，即拼接了一次`!`，然后lambda表达式返回值的整体又作为lambda表达式函数的参数被传入，再拼接了一次`!`，所以最终结果为`hello!!`。

## Racket本地绑定

Racket本地绑定(local binding)类似于Java中的局部变量，即在Racket过程内部当中进行的一次定义，之前的定义都只是在过程开头定义一次，类似于全局性的定义，这里要谈的本地绑定可以理解为在过程内部的局部定义，既可以是标识符与某种类型的变量的绑定，也可以是标识符与一个内部过程的绑定。首先是通过define进行这种绑定，如下定义过程求一个参数的三次方。

```lisp
> (define (three-power x)
    (define (power x)
      (* x x))
    (* (power x) x))
> (three-power 10)
1000
```

在求该三次方的过程当中，我们先定义了求该参数平方的过程，然后再调用该过程先求出该参数的平方，最后再与该参数相乘得出最终结果。很明显，在过程`three-power`的函数体内部进行了一个过程定义，即本地绑定。

除通过define进行本地绑定之外，还可以通过let形式进行绑定，其形式如下

```lisp
( let ( {[ ‹id› ‹expr› ]}* ) ‹expr›+ )
```

`( {[ ‹id› ‹expr› ]}* )`该部分是let绑定的变量列表，其后的`‹expr›+`是对前面绑定的变量进行操作的表达式，同样也可以是一个过程。如下定义一个过程，该过程通过let绑定两个随机数，然后通过比较这两个随机数来判断谁大。

```lisp
> (define (who-large)
    (let ([x (random 4)]
          [y (random 4)])
      (if (> x y) "x bigger" "y bigger")))
> (who-large)
"y bigger"
> (who-large)
"x bigger"
```

可以发现，let形式的绑定中，绑定的变量列表与表达式同属于let过程，而define过程的绑定中二者是分开的，这是二者在形式上的差别，那么用let形式进行绑定有哪些好处呢？如下

+ let形式一次性可以绑定多个标识符，而define形式每次则只能绑定一个;
+ let形式可以出现在表达式的任何位置。

而let绑定的缺陷在于其绑定的标识符不能相互引用，仅仅能在其主体中引用，即后面的表达式中引用。let*形式的绑定则在保持let优势的同时避免了let的这一缺陷，使得绑定的标识符可以在绑定列表中引用，而不仅仅局限于主体中。如下

```lisp
> (define (who-large)
    (let* ([x (random 4)]
          [y (random 4)]
          [who (if (> x y) "x" "y")])	; 此处引用了绑定列表中的x与y
      (if (equal? who "x") "x bigger" "y bigger")))
> (who-large)
"y bigger"
> (who-large)
"y bigger"
> (who-large)
"x bigger"
```

## Racket缩进

Racket不强制缩进，但是缩进可以提升代码可读性。