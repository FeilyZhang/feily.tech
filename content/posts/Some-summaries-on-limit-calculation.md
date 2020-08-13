---
title: "Some summaries on limit calculation"
date: 2020-08-11T10:09:14+08:00
categories: ["Calculus"]
tags: ["Calculus"]
draft: false
---

本文是对极限计算的一些总结和摘要。在结构编排上可能与相关教材有所出入，但是内容是无差异的，本文将尽可能揭示常用极限计算的通用性方法。

## **极限计算基础及简单极限计算**

对于函数的极限，总的来说，只存在两种，一种是自变量趋于有限值($x \to x_0$)时的极限，另一种是自变量趋于无限值($x \to \infty$)时的极限。

对于前者，优先代入原式，若可得有意义的解，那么便是要求的极限，但是大多数情况下带入是没有意义的，这就需要分情况讨论之，其本质上就是化简，有时候化简到一定程度便可以代入计算，有时候则会直接化简出解。

对于后者，唯一的办法就是化简，化简到一定程度之后便可以观察其变化趋势，以得最终解。

在本文中，简单的极限计算包括三种，分别是多项式、两个重要极限以及等价无穷小代换等。

### **多项式极限计算**

多项式极限计算是最简单的一种，当然包括$x \to x_0$和$x \to \infty$两种，由于$x \to x_0$时若代入有意义则计算较为简单，因此这里不予讨论，只讨论多项式除法分子分母代入均为0以及$x \to \infty$时的多项式除法。

对于前者，若分子分母代入后均为0，那么便需要化简，对其有需要分几种情况

1. 若分子分母最低次项次数相同且$x \to 0$，则分子分母同除最低次项以对分子分母化出常数项，从而代入0便可得解，其解为常数项的比（有的多项式不用化简就存在常数项，那么便可直接代入）；
2. 若分子分母最高项次数均为2且$x\ !\to 0$（此处!表示不趋于），则直接使用交叉相乘化简，便可以代入求解（还有其余的多项式可以通过提公因子等形式进行约项以化简，本质上是一样的）；
3. 若原式形式为多项式除法相减，则先将其整理为一个相除的多项式，再综合运用上述两种办法求解。

相关示例如下

1. \begin{equation}\lim_{x \to 0}\frac{4x^3 - 2x^2 + x}{3x^2 + 2x} = \lim_{x \to 0}\frac{4x^2 - 2x + 1}{3x + 2} = \frac{1}{2}\end{equation}

2. \begin{equation}\lim_{x \to 1}\frac{x^2 - 2x + 1}{x^2 - 1} = \lim_{x \to 1}\frac{(x - 1)^2}{(x + 1)(x - 1)} = \lim_{x \to 1}\frac{x - 1}{x + 1} = 0\end{equation}

3. \begin{equation}\lim_{x \to 1}\frac{1}{1 - x} - \frac{3}{1 - x^3} = \lim_{x \to 1}\frac{1 + x + x^2 - 3}{(1 - x)(1 + x + x^2)} = -\lim_{x \to 1}\frac{(1 - x)(x + 2)}{(1 - x)(1 + x + x^2)} = -1\end{equation}

对于后者，无论什么情况，直接给分子分母多项式除以分子或分母的最高项，便可得解，其本质在一定程度上是使用无穷小与无穷大的关系求解，更一般地，可总结如下

\begin{equation}\lim_{x \to \infty}\frac{a_0x^m + a_1x^{m - 1} + \dots + a_m}{b_0x^n + b_1x^{n - 1} + \dots + b_n} = 
\begin{cases}
\frac{a_0}{b_0},\ if \ n = m, \newline
0,\ if \ n > m, \newline
\infty,\ if \ n < m.
\end{cases}\end{equation}

### **两个重要极限**

以下两个重要极限在形式上很容易辨别，当$x$趋向的值分别满足两个重要极限的条件时，便可使用，特别地，需要对其进行广义化操作。

\begin{equation}\lim_{x \to 0} \frac{\sin x}{x} = 1, \lim_{x \to \infty}(1 + \frac{1}{x})^x = e\end{equation}

上述两个重要极限的本质是等价无穷小，具体推导过程如下

对于第一个重要极限，首先将$\sin x$在$x = 0$处泰勒展开，如下

\begin{equation}\sin x = x - \frac{x^3}{3!} + o(x^3)\end{equation}

实际上只需要展开到第一项即可，即$\sin x = x + o(x)$，那么便可得等价无穷小

\begin{equation}\lim_{x \to 0} \frac{\sin x}{x} = \frac{x + o(x)}{x} = 1\end{equation}

第二个重要极限的推导稍微复杂一点，需要说明的是我们的最终目标是验证等式两边的确相等。首先对该重要极限两边取对数，并化简得

\begin{equation}\ln[\lim_{x \to \infty}(1 + \frac{1}{x})^x] = \ln e,[\ln\lim_{x \to \infty}(1 + \frac{1}{x})]^x = 1,\lim_{x \to \infty}[x\ln(1 + \frac{1}{x})]= 1\end{equation}

令$t = \frac{1}{x}$，则$x = \frac{1}{t}$，从而$x \to \infty$变为$t \to 0$，那么上式变为

\begin{equation}\lim_{t \to 0}[\frac{\ln(1 + t)}{t}]= 1\end{equation}

将$\ln(1 + t)$按第一项展开，得$\ln(1 + t) = t - \frac{t^2}{2} + \frac{t^3}{3} + o(t^3)$，只需要展开到第一项即可，从而

\begin{equation}\lim_{t \to 0}[\frac{\ln(1 + t)}{t}]= \frac{t + o(t)}{t} = 1\end{equation}

从而验证第二个重要极限等式两边相等，至此重要极限均得证。

对于此类问题，需要特别注意其变形，即广义化。特别地对于第一个重要极限，要时刻记得其本质就是等价无穷小，因此可以替换。示例如下

\begin{equation}\lim_{x \to 0}\frac{\sin \omega x}{x} = \lim_{x \to 0}\frac{\omega x}{x} = \omega, \ \ \ 
\lim_{x \to 0}\frac{\sin 2x}{\sin 5x} = \lim_{x \to 0}\frac{2x}{5x} = \frac{2}{5}\end{equation}

与$\sin x$相关的三角函数恒等式仍然可以使用第一个重要极限背后的无穷小替换，例如

\begin{equation}\lim_{x \to 0}\frac{\tan 3x}{x} = \lim_{x \to 0}\frac{\sin 3x}{x \cos 3x} = \lim_{x \to 0}\frac{3}{\cos 3x}  = 3\end{equation}

事实上，$\tan x$等价于$x$，因此广义化之后可以直接替换，而不必使用第一个重要极限绕弯子。

第二个重要极限的广义化一般使用换元法，例如求下式的极限

\begin{equation}\lim_{x \to 0}(1 - x)^{\frac{1}{x}}\end{equation}

令$x = -\frac{1}{t}$，从而$t = -\frac{1}{x}$，于是

\begin{equation}\lim_{x \to 0}(1 - x)^{\frac{1}{x}} = \lim_{t \to \infty}(1 + \frac{1}{t})^{-t} = \frac{1}{e}\end{equation}

### **等价无穷小代换等**

如果$\lim\frac{\beta}{\alpha} = 1$，就说$\beta$与$\alpha$是等价无穷小，记作$\alpha \sim \beta$. 其本质上就是$\beta$与$\alpha$在$x \to 0$时的速度相同，因此比值为1。

等价无穷小的推导可以通过上下同阶的泰勒展开式实现，上述对重要极限的推导已经演示过了，此处不再赘述。作为结论，当$x \to 0$时，常用的等价无穷小有

\begin{equation} \sin x \sim x,\ \tan x \sim x,\ \arcsin x \sim x,\ \arctan x \sim x,\ \ln(1 + x) \sim x\end{equation}
\begin{equation} \ e^x - 1 \sim x,\ a^x - 1 \sim x \ln a,\ 1 - \cos x \sim \frac{1}{2}x^2, (1 + x)^ \alpha - 1 \sim \alpha x\end{equation}

使用时，仍然要注意广义化。特别地，当分子或分母是加减法时，不可拆分单独替换。示例如下

\begin{equation}\lim_{x \to 0}{\frac{\sin x}{x^3 + 3x}} = \lim_{x \to 0}{\frac{x}{x^3 + 3x}} = \lim_{x \to 0}{\frac{1}{x^2 + 3}} = \frac{1}{3}\end{equation}

\begin{equation}\lim_{x \to 0}{\frac{(1 + x^2)^\frac{1}{3} - 1}{\cos x - 1}} = \lim_{x \to 0}\frac{\frac{1}{3}x^2}{-\frac{1}{2}x^2} = -\frac{2}{3}\end{equation}

除过等价无穷小代换，还可以使用无穷小运算规则以及无穷大和无穷小的关系计算极限，如下

1. 有限个无穷小的和是无穷小；
2. 有限个无穷小的乘积是无穷小；
3. 有界函数与无穷小的乘积是无穷小；
4. 在自变量的同一变化过程中，若$f(x)$为无穷大，则$\frac{1}{f(x)}$为无穷小；反之，如果$f(x)$为无穷小，且$f(x) \neq 0$，则$\frac{1}{f(x)}$为无穷大.

以上三种均为极限计算的简单形式，为基础，务必熟练于心。

## **未定式的计算**

未定式的计算一方面以上述简单极限的计算为基础，另一方面需要引进新的方法，例如泰勒展开式以及洛必达法则等。

### **泰勒展开式**

泰勒展开式可以将复杂的函数解析式在某点处展开为多项式，借助于多项式优良的性质，可以很容易求得函数在某点处的极限值。

虽然函数在某点处的泰勒多项式很容易求得极限，但是其前提是求导数乃至$n$阶导数，这也就意味着，如果函数求导困难的话，用泰勒展开势必会加大计算难度和强度而得不偿失。特别地，在求解函数的极限上，泰勒展开式可以广泛应用在下面两种形式的函数上

1. $\frac{A}{B}$型，当“上下同阶”时便停止展开，从而将原式化为多项式除法，而方便求得极限；
2. $A - B$型，当“幂次最低”时便停止展开，从而将原式化为多项式，而方便求得极限。

无论对于上述哪种形式，都有一个前提，那就是函数求导比较容易，便会事半功倍。示例如下

\begin{equation}\lim_{x \to 0}\frac{x - \sin x}{x^3} = \lim_{x \to 0}\frac{x - [x - \frac{1}{6}x^3 + o(x^3)]}{x^3} = \lim_{x \to 0}\frac{\frac{1}{6}x^3 + o(x^3)}{x^3} = \frac{1}{6}\end{equation}

上题属于上述两种形式的复合，其本质上属于$\frac{0}{0}$未定式。再看一题，依然是$\frac{0}{0}$未定式

\begin{equation}\lim_{x \to 0}\frac{\arcsin x - \arctan x}{\sin x - \tan x} = \lim_{x \to 0}\frac{x + \frac{x^3}{3!} + o(x^3) - (x - \frac{x^3}{3} + o(x^3))}{x - \frac{x^3}{3!} + o(x^3) - (x + \frac{x^3}{3} + o(x^3))} = \lim_{x \to 0}\frac{\frac{1}{2}x^3}{-\frac{1}{2}x^3} = -1\end{equation}

综上，可做出结论：**算符两边均为函数且容易展开并且展开之后容易相互抵消的函数式（其实就是算符两边是基本初等函数及部分初等函数）用泰勒多项式展开更容易**，因为抵消之后可以减少计算量。

再比如对于下述函数的极限

\begin{equation}\lim_{x \to 0}\frac{\sqrt{1 + \tan x} - \sqrt{1 + \sin x}}{x\ln(1 + x) - x^2}\end{equation}

上式中分母算符两边的函数为基本初等函数的有限次复合，因此进行泰勒展开很复杂，因为随着导数阶数的变大求导会变得困难重重，再加上展开式仍然需要计算，因此不适合泰勒展开。

另一方面，对于根号差形式的项，可以通过有理化来化简，那么上式可化为

\begin{equation}\lim_{x \to 0}\frac{\sqrt{1 + \tan x} - \sqrt{1 + \sin x}}{x\ln(1 + x) - x^2} = \lim_{x \to 0}\frac{\tan x - \sin x}{x[\ln(1 + x) - x]}·\frac{1}{\sqrt{1 + \tan x} + \sqrt{1 + \sin x}}\end{equation}

从而可以单独计算出算符`·`后面的函数的极限然后提到`lim`之前，即

\begin{equation}\lim_{x \to 0}\frac{\tan x - \sin x}{x[\ln(1 + x) - x]}·\frac{1}{\sqrt{1 + \tan x} + \sqrt{1 + \sin x}} = \frac{1}{2}\lim_{x \to 0}\frac{\tan x·(1 - \cos x)}{x[\ln(1 + x) - x]}\end{equation}

上式仍然不能使用泰勒展开，虽然说泰勒展开的难度已经降低了，但是分子分母泰勒展开之后算符`-`两边的项不容易抵消，运算难度还是比较大，这种情况下就必须考虑另一种技术，即洛必达法则。

### **洛必达法则**

洛必达法则用于计算$\frac{0}{0}$与$\frac{\infty}{\infty}$型未定式，它可以将求导用于极限计算的函数式化简，甚至直接化简得出计算结果。并且，在化简出的函数式满足$\frac{0}{0}$与$\frac{\infty}{\infty}$型未定式时，可以继续进行求导化简。

例如对于上题，现在便可以使用洛必达法则处理

\begin{equation}\frac{1}{2}\lim_{x \to 0}\frac{\tan x·(1 - \cos x)}{x[\ln(1 + x) - x]} = \frac{1}{2}\lim_{x \to 0}\frac{\sin x}{-\frac{x}{1 + x}} = -\frac{1}{2}\end{equation}

但是并泰勒公式无法使用时或者有理化之后便可以使用洛必达法则，当然大多数情况下这么做是合理的，但是洛必达法则也有局限性。二者对比如下

+ 泰勒公式的优点在于将任意形式的函数可以转化为多项式，付出的代价可能是较为繁复的`n`阶导数计算以及整理多项式的计算，但多项式的优良性质使得求解极限非常容易，只是转化多项式的过程限制了泰勒公式在手动计算极限方面的应用。

+ 洛必达法则计算极限的优点在于应用广泛，只要满足$\frac{0}{0}$与$\frac{\infty}{\infty}$型未定式且已经对根号差有理化，那么都可以使用洛必达法则，但是洛必达法则并没有计算导数，应用的只是导函数，这就导致有时候导函数的计算会越来越复杂，比如指数函数的复合（$e^{-\frac{1}{x^2}}$）,由于指数函数的导函数形式稳定，再加上有限次复合，会导致其导函数计算越来越复杂，这便是其弊端。

在手动计算极限上面，泰勒公式的弊端一定程度上可以通过洛必达法则弥补，那么洛必达法则的弊端呢，谁来弥补？

一般地，对于其导函数形式较为稳定的经基本初等函数经有限次复合的函数可以尽可能通过换元法先处理，再进一步做决定。例如求下列函数的极限

\begin{equation}\lim_{x \to 0}\frac{e^{-\frac{1}{x^2}}}{x^{100}}\end{equation}

该题属于$\frac{0}{0}$未定式，不适合泰勒展开，以为很复杂且无法抵消，自然用洛必达法则，但是分子为指数函数且经过有限次复合，求其导函数会越难越复杂，因此先换元。令$\frac{1}{x^2} = t$，则$x^2 = t^{-1}$，$x^{100} = t^{-50}$，由于$x \to 0$，那么$t \to +\infty$，从而

\begin{equation}\lim_{x \to 0}\frac{e^{-\frac{1}{x^2}}}{x^{100}} = \lim_{t \to +\infty}\frac{e^{-t}}{t^{-50}} = \lim_{t \to +\infty}\frac{t^{50}}{e^t} = \lim_{t \to +\infty}\frac{50t^{49}}{e^t} = \lim_{t \to +\infty}\frac{50·49·t^{48}}{e^t} = \lim_{t \to +\infty}\frac{50!}{e^t} = 0\end{equation}

### **其余形式的未定式**

#### **$0·\infty$型未定式**

对于$0·+\infty$型未定式，可以通过变形将其转化为$\frac{0}{0}$与$\frac{\infty}{\infty}$型未定式，从而便可以使用洛必达法则，比如求

\begin{equation}\lim_{x \to 0^+}x^n\ln x\ (n > 0)\end{equation}

这是$0·+\infty$型未定式。但是由于

\begin{equation}x^n\ln x = \frac{\ln x}{\frac{1}{x^n}}\end{equation}

所以原式可化为

\begin{equation}\lim_{x \to 0^+}x^n\ln x\ = \lim_{x \to 0^+}\frac{\ln x}{\frac{1}{x^n}}\end{equation}

从而变成了$\frac{\infty}{\infty}$型未定式，便可使用洛必达法则

\begin{equation}\lim_{x \to 0^+}x^n\ln x\ = \lim_{x \to 0^+}\frac{\ln x}{\frac{1}{x^n}} = \lim_{x \to 0^+}\frac{\frac{1}{x}}{-nx^{-n-1}} = \lim_{x \to 0^+}\frac{-x^n}{n} = 0\end{equation}

再比如下题，求极限

\begin{equation}\lim_{x \to -\infty}x(\sqrt{x^2 + 100} + x)\end{equation}

同样属于$0·+\infty$型未定式，一样通过变形将其转化为$\frac{0}{0}$与$\frac{\infty}{\infty}$型未定式。由于存在根号差$\sqrt{x^2 + 100} + x$，而且

\begin{equation}(\sqrt{x^2 + 100} + x)(\sqrt{x^2 + 100} - x) = 100\end{equation}

从而很容易变形，得$\frac{\infty}{\infty}$型未定式，如下

\begin{equation}\lim_{x \to -\infty}x(\sqrt{x^2 + 100} + x) = \lim_{x \to -\infty}\frac{100x}{\sqrt{x^2 + 100} - x}\end{equation}

然后令$t = -x$便可以将$x \to -\infty$转化为$t \to +\infty$

\begin{equation}\lim_{x \to -\infty}x(\sqrt{x^2 + 100} + x) = \lim_{x \to -\infty}\frac{100x}{\sqrt{x^2 + 100} - x} = \lim_{t \to +\infty}\frac{-100t}{\sqrt{t^2 + 100} + t}\end{equation}

依然是$\frac{\infty}{\infty}$型未定式，然后再应用洛必达法则，得

\begin{equation}\lim_{x \to -\infty}x(\sqrt{x^2 + 100} + x) = \lim_{x \to -\infty}\frac{100x}{\sqrt{x^2 + 100} - x} = \lim_{t \to +\infty}\frac{-100t}{\sqrt{t^2 + 100} + t} = \lim_{t \to +\infty}\frac{-100}{\sqrt{\frac{t^2}{t^2 + 100} } + 1}\end{equation}

由于下式恒成立

\begin{equation}\sqrt{\frac{t^2}{t^2 + 100}} = \sqrt{\frac{1}{\frac{t^2 + 100}{t^2}}} = \frac{1}{\sqrt{\frac{t^2 + 100}{t^2}}} = \frac{1}{\sqrt{1 + \frac{100}{t^2}}}\end{equation}

从而得原式极限值

\begin{equation}\lim_{t \to +\infty}\frac{-100}{\sqrt{\frac{t^2}{t^2 + 100} } + 1} = \lim_{t \to +\infty}\frac{-100}{\sqrt{1 + \frac{100}{t^2}} + 1} = -50\end{equation}

再举一例，求极限

\begin{equation}\lim_{x \to 1^-}\ln x \ln(1 - x)\end{equation}

由于$x \to 0$时，$\ln(1 + x) \sim x$，那么$x \to 1$时，有$\ln(1 + x - 1) \sim x - 1$，同时$\ln(1 + x - 1) = \ln x$，即

\begin{equation}\ln x = \ln (1 + x - 1) \sim x - 1(x \to 1)\end{equation}

那么原式可化为

\begin{equation}\lim_{x \to 1^-}\ln x \ln(1 - x) = \lim_{x \to 1^-}(x - 1)\ln(1 - x)\end{equation}

令$t = 1 - x$，那么$t \to 0^+$，容易求得极限

\begin{equation}\lim_{x \to 1^-}\ln x \ln(1 - x) = \lim_{x \to 1^-}(x - 1)\ln(1 - x) = -\lim_{t \to 0^+}t \ln t = 0\end{equation}

#### **$\infty·\infty$型未定式**

对于$\infty·\infty$型未定式，主要思路有两种

1. 如果函数中存在分式，比如分式相加减等，则先通分，将加减法变形为乘除法，然后便可以使用其它技术进行进一步处理；
2. 如果函数中没有分母，则可以通过提取公因式，或者做倒代换做出分母，然后通分（如果必要的话），再进一步处理。

示例如下，求下列极限

\begin{equation}\lim_{x \to 0}(\frac{1}{\sin^2x} - \frac{\cos^2x}{x^2})\end{equation}

首先通分，再根据三角恒等式及等价无穷小替换，得

\begin{equation}\lim_{x \to 0}(\frac{1}{\sin^2x} - \frac{\cos^2x}{x^2}) = \lim_{x \to 0}\frac{x^2 - \sin^2x\cos^2x}{x^2\sin^2x} = \lim_{x \to 0}\frac{x^2 - (\sin x\cos x)^2}{x^2\sin^2x} = s\lim_{x \to 0}\frac{x^2 - \frac{1}{4}sin^22x}{x^4}\end{equation}

然后应用洛必达法则，得

\begin{equation}\lim_{x \to 0}\frac{x^2 - \frac{1}{4}sin^22x}{x^4} = \lim_{x \to 0}\frac{2x - \frac{1}{2}\sin 4x}{4x^3} = \lim_{x \to 0}\frac{1 - \cos 4x}{6x^2} = \lim_{x \to 0}\frac{\frac{1}{2}(4x)^2}{6x^2} = \frac{4}{3}\end{equation}