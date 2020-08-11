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

实际上只需要展开到第一项即可，即$\sin x = x + o(x^3)$，那么便可得等价无穷小

\begin{equation}\lim_{x \to 0} \frac{\sin x}{x} = \frac{x}{x} = 1\end{equation}

第二个重要极限的推导稍微复杂一点，需要说明的是我们的最终目标是验证等式两边的确相等。首先对该重要极限两边取对数，并化简得

\begin{equation}\ln[\lim_{x \to \infty}(1 + \frac{1}{x})^x] = \ln e,[\ln\lim_{x \to \infty}(1 + \frac{1}{x})]^x = 1,\lim_{x \to \infty}[x\ln(1 + \frac{1}{x})]= 1\end{equation}

令$t = \frac{1}{x}$，则$x = \frac{1}{t}$，从而$x \to \infty$变为$t \to 0$，那么上式变为

\begin{equation}\lim_{t \to 0}[\frac{\ln(1 + t)}{t}]= 1\end{equation}

将$\ln(1 + t)$按第一项展开，得$\ln(1 + t) = t - \frac{t^2}{2} + \frac{t^3}{3} + o(t^3)$，只需要展开到第一项即可，从而

\begin{equation}\lim_{t \to 0}[\frac{\ln(1 + t)}{t}]= \frac{t}{t} = 1\end{equation}

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