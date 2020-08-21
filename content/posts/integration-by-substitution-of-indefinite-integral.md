---
title: "Integration by substitution of indefinite integral"
date: 2020-08-15T17:42:14+08:00
categories: ["Calculus"]
tags: ["Calculus"]
draft: false
---

本文讨论和总结求函数的不定积分的换元积分法的一般形式。求被积函数的不定积分的本质就是求作为导函数的被积函数的原函数，即通过导函数反推原函数。

首先从相关概念入手。

## **一、原函数与不定积分**

### **1.1 原函数**

如果在区间$I$上，可导函数$F(x)$的导函数为$f(x)$，即对任一$x \in I$，都有

\begin{equation}F^{'}(x) = f(x)\ \ \ or\ \ \ dF(x) = f(x)dx,\end{equation}

那么函数$F(x)$就称为$f(x)$（或$f(x)dx$）在区间$I$上的原函数.

### **1.2 原函数存在定理**

如果函数$f(x)$在区间$I$上连续，那么在区间$I$上存在可导函数$F(x)$，使对任一$x \in I$都有

\begin{equation}F^{'}(x) = f(x),\end{equation}

即**连续函数一定有原函数.**

### **1.3 不定积分**

在区间$I$上，函数$f(x)$的带有任意常数项的原函数称为$f(x)$（或$f(x)dx$）在区间$I$上的不定积分，记作

\begin{equation}\int f(x)dx,\end{equation}

其中记号$\int$称为积分号，$f(x)$称为被积函数，$f(x)dx$称为被积表达式，$x$称为积分变量.

本文不涉及一些简单的积分计算（主要是通过基本积分表来计算的），比如幂函数$\int x^2dx$的积分，如下

\begin{equation}\int x^2dx = \frac{x^3}{3} + C.\end{equation}

### **1.4 不定积分的性质**

性质1：设函数$f(x)$及$g(x)$的原函数存在，则

\begin{equation}\int[f(x) + g(x)]dx = \int f(x)dx + \int g(x)dx.\end{equation}

即，被积函数和的不定积分为被积函数不定积分之和.

性质2：设函数$f(x)$的原函数存在，$k$为非零常数，则

\begin{equation}\int kf(x)dx = k\int f(x)dx.\end{equation}

即，被积函数数乘的不定积分为被积函数不定积分之数乘.

**利用基本积分表以及不定积分的这两个性质，可以求一些有限个基本被积函数通过加法或数乘运算组成的被积函数的不定积分**。比如，求下述不定积分

\begin{equation}\int\sqrt{x}(x^2 - 5)dx = \int x^{\frac{5}{2}}dx - \int 5x^{\frac{1}{2}}dx = \frac{2}{7}x^{\frac{7}{2}} - 5·\frac{2}{3}x^{\frac{3}{2}} + C = \frac{2}{7}x^3\sqrt{x} - \frac{10}{3}x\sqrt{x} + C.\end{equation}

## **二、不定积分的换元积分法**

将复合函数微分法反过来用于求不定积分，利用中间变量代换，得到复合函数积分法的方法，被称为换元积分法。共包括两类。

### **2.1 第一类换元法**

定理：设$f(x)$具有原函数，$u = \varphi(x)$可导，则有换元公式

\begin{equation}\int f[\varphi(x)]\varphi^{'}(x)dx = [\int f(u)du]_{u = \varphi(x)}\end{equation}

关于上述定理的解释如下

设原复合函数为$F(u),u = \varphi(x)$，那么根据复合函数微分法，该复合函数关于$x$的导函数为

\begin{equation}\frac{dF(x)}{dx} = \frac{dF(x)}{du}·\frac{du}{dx} = F^{'}(u)u^{'} = f[\varphi(x)]\varphi^{'}(x)\end{equation}

其微分形式为

\begin{equation}dF(x) = F^{'}(u)u^{'}dx = f[\varphi(x)]\varphi^{'}(x)d(x)\tag{a}\end{equation}

上式右侧即被积表达式的复合函数形式。更进一步地，由于$d\varphi(x) = \varphi^{'}(x)d(x),du = u^{'}dx$，所以上式可以变为

\begin{equation}dF(u) = F^{'}(u)du = f[\varphi(x)]d\varphi(x)\tag{b}\end{equation}

上述式(a)、(b)含义一致，只是表达形式上有差异。二者都表示复合函数的微分就是复合函数关于被复合函数的导数与被复合函数关于$x$的微分之积。现在就可以很容易的解释第一类换元法：

由于某些被积函数形式复杂，无法通过查基本积分表得出其原函数，但是若此类函数容易找到$\varphi(x)$，通过$\varphi(x)$关于$x$的微分容易凑出$d\varphi(x)$（即(a)式等号右侧的$\varphi^{'}(x)d(x)$），那么便可以将被积表达式转化为(a)式（其本质仍然是被积函数关于$x$的不定积分），从而根据复合函数微分法容易转化为(b)式（其本质变成了被积函数关于$d\varphi(x)$的不定积分），由于被积函数得以简化，通过查基本积分表便可以得到复合函数的不定积分（即(b)式由右侧被积函数转化为左侧原函数），然后将$\varphi(x)$回代即可得到被积函数关于$x$的不定积分。

所以，第一类换元法的本质就是复合函数微分法的反运用。**其核心在于从被积函数中找到原函数的复合函数$\varphi(x)$，并据该被复合函数的微分凑出$d\varphi(x)$以求得被积函数的不定积分.更进一步地，我们的目标是将被积函数关于$x$的不定积分转化为关于$d\varphi(x)$的不定积分，从而简化被积函数，然后通过基本积分表就可以得到被积函数关于$d\varphi(x)$的不定积分，最后回代便可以得到被积函数关于$x$的不定积分.**

试举一例，求下述被积函数的不定积分

\begin{equation}\int 2\cos2xdx.\end{equation}

令$\varphi(x) = 2x$，从而$d\varphi(x) = 2dx$，那么（本行相当于式(a)）

\begin{equation}\int 2\cos2xdx = \int \cos\varphi(x)d\varphi(x) = \sin\varphi(x) + C = \sin2x + C.\end{equation}

或者更直接一点

\begin{equation}\int 2\cos2xdx = \underbrace{\int \cos2x2dx}_{(a)} = \overbrace{\int \cos2xd(2x)}^{(b)} = \sin2x + C.\end{equation}

再举一例，求下述不定积分

\begin{equation}\int \frac{1}{3 + 2x}dx = \frac{1}{2}\int\frac{1}{3 + 2x}d(3 + 2x) = \frac{1}{2}\ln|3 + 2x| + C.\end{equation}

对于$x$只出现一次的被积函数来说，可以直接写出由式(a)到式(b)的转化等式，进而得出不定积分，比如上述两题。但是对于$x$出现了不止一次的被积函数来说，若非复合的$x$部分与$dx$无法凑出被复合函数的微分，那么应该显式的进行换元，即写出$\varphi(x)$，因为需要导出非复合部分的$x$的$\varphi(x)$表示形式，比如下题

\begin{equation}\int \frac{x^2}{(x + 2)^3}dx\end{equation}

令$\varphi(x) = x + 2$，那么$x = \varphi(x) - 2$，则可得不定积分

\begin{equation}\begin{aligned}&\int \frac{x^2}{(x + 2)^3}dx = \int \frac{(\varphi(x) - 2)^2}{\varphi(x)^3}d\varphi(x) \newline = & \int \frac{\varphi(x)^2 - 4\varphi(x) + 4}{\varphi(x)^3}d\varphi(x) = \int (\varphi(x)^{-1} - 4\varphi(x)^{-2} + 4\varphi(x)^{-3}d\varphi(x)) \newline = & \ln|\varphi(x)| + 4\varphi(x)^{-1} - 2\varphi(x)^{-2} + C = \ln|x + 2| + \frac{4}{x + 2} - \frac{2}{(x + 2)^2} + C.\end{aligned}\end{equation}

但是如果可以凑出被复合函数的微分，那么直接写即可，例如

\begin{equation}\int 2xe^{x^{2}}dx.\end{equation}

上式中，非复合的$x$部分$2x$与$dx$可以凑出$x^2$的微分，因此直接可以转化为(b)式，即求得不定积分

\begin{equation}\int 2xe^{x^{2}}dx = \int e^{x^{2}}2xdx = \int e^{x^{2}}d(x^2) = e^{x^{2}} + C.\end{equation}

再比如，求下述不定积分

\begin{equation}\int x\sqrt{1 - x^2}dx.\end{equation}

该被积函数虽然$x$出现了不止一次，但是非复合$x$可以与$dx$凑出一个微分，因此不用麻烦地将复合形式写出来，直接计算即可

\begin{equation}\int x\sqrt{1 - x^2}dx = -\frac{1}{2}\int\sqrt{1 - x^2}(-2xdx) = -\frac{1}{2}\int\sqrt{1 - x^2}d(1 - x^2) = -\frac{1}{3}(1 - x^2)^{\frac{3}{2}} + C.\end{equation}

我们上述是以被复合函数为基准，以凑微分为手段的形式来计算不定积分的，即遵循的是从找被复合函数到凑被复合函数微分的技术路线，这种方法适用于被复合函数较容易寻找的情况。但是如果被复合函数不容易寻找，我们应该尽可能将被积函数中的一些表达式凑微分，然后便可以找到被复合函数，即遵循的是凑被复合函数微分到找被复合函数的技术路线。比如下题

\begin{equation}\int \frac{dx}{x(1 + 2\ln x)}\end{equation}

该题的被复合函数并不明显，但是考虑到被积函数中存在$\frac{dx}{x} = d\ln x$，因此便找到了被复合函数，即

\begin{equation}\int \frac{dx}{x(1 + 2\ln x)} = \int\frac{d(\ln x)}{1 + 2\ln x} = \frac{1}{2}\int \frac{d(1 + 2\ln x)}{1 + 2\ln x} = \frac{1}{2}\ln|1 + 2\ln x| + C.\end{equation}

上述这些例题中，之所以尽可能直接写成由式(a)到式(b)的形式，是因为为了说明第一类换元法的内在逻辑，但是可以发现有些许不便之处，因为对于有些题，凑被复合函数的微分明显复杂。有没有一种办法简化呢？当然有的，那就是将代换过程显式表达出来，注意，这种方法略显繁琐，但是计算的可操作性更强。以上题为例

考虑到$\frac{dx}{x} = d\ln x$，因此下式成立

\begin{equation}\int \frac{dx}{x(1 + 2\ln x)} = \int\frac{d(\ln x)}{1 + 2\ln x}.\end{equation}

令$u = 1 + 2\ln x$，对$\ln x$求微分，得$du = 2d(\ln x)$，从而$d(\ln x) = \frac{du}{2}$，那么上式可转化为

\begin{equation}\int\frac{d(\ln x)}{1 + 2\ln x} = \int\frac{du}{2u} = \frac{1}{2}\int\frac{du}{u}.\end{equation}

从而可以直接求出复合函数的不定积分，然后回代即可

\begin{equation}\frac{1}{2}\int\frac{du}{u} = \frac{1}{2}\ln u + C = \frac{1}{2}\ln|1 + 2\ln x| + C.\end{equation}

上述等式中只有式(b)，实际上式(a)就在我们的显式代换中已经实现了.