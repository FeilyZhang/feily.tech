---
title: "How to understand the geometric meaning of determinant"
date: 2020-08-21T12:42:05+08:00
categories: ["linear algebra"]
tags: ["linear algebra"]
draft: false
---

行列式的几何意义是或者本质定义为：$n$阶行列式是由$n$个$n$维向量$\alpha_1 = [a_{11}, a_{12}, \cdots, a_{1n}], \alpha_2 = [a_{21}, a_{22}, \cdots, a_{2n}],\cdots, \alpha_n = [a_{n1}, a_{n2}, \cdots, a_{nn}]$组成的，其运算规则下的结果为以这$n$个向量为邻边的$n$维图形的体积。那么如何理解行列式的这一本质定义呢？不妨以三阶行列式为例来说明之。

对于三阶行列式而言，其几何意义就是以组成三阶行列式的三个三维向量($\alpha_1, \alpha_2,\alpha_3$)为邻边的平行六面体的体积。该平行六面体的体积表达式为**底面积×高**。不妨以$\alpha_1, \alpha_2$为邻边构成平行六面体的底面，其中向量$\alpha_1$的模$\alpha_1$作为底面底长，设向量$\alpha_1,\alpha_2$的夹角为$\theta$，那么底面高就等于$|\alpha_2|\sin\theta$，从而该平行六面体的底面积为$|\alpha_1||\alpha_2|\sin\theta$，这刚好是向量$\alpha_1,\alpha_2$的向量积的模，即

\begin{equation}S = |\alpha_1 \times \alpha_2| = |\alpha_1||\alpha_2|\sin\theta\end{equation}

**即平行六面体的底面积就等于构成该平行六面体的底面的两向量的向量积之模**。

接下来还剩一个高长，由于构成平行六面体的底面的两向量的向量积垂直于这两向量构成的平面，即为法向量$f$，记向量$\alpha_3$与该法向量$f$的夹角为$\beta$，那么向量$\alpha_3$到该法向量$f$的投影就是以向量$\alpha_1,\alpha_2$的向量积的模为底面积的平行六面体的高，为$|\alpha_3|\cos\beta$，也就是

\begin{equation}h = Prj_f\alpha_3 = |\alpha_3|\cos\beta\end{equation}

所以整个平行六面体的体积为

\begin{equation}V = Sh = |\alpha_1 \times \alpha_2|Prj_f\alpha_3 = \overbrace{\underbrace{|\alpha_1||\alpha_2|\sin\theta|}_{(a)}\alpha_3|\cos\beta}^{(b)}\end{equation}

上式中，$(a)$为向量$\alpha_1, \alpha_2$的向量积的模，即垂直于向量$\alpha_1, \alpha_2$构成的平面的法向量（即分别垂直于向量$\alpha_1, \alpha_2$）的模，$(b)$为该法向量（垂直于向量$\alpha_1, \alpha_2$构成的平面的法向量）与向量$\alpha_3$的数量积，从而上式实际上定义了向量$\alpha_1, \alpha_2, \alpha_3$的混合积$\left[\alpha_1\ \alpha_2\ \alpha_3\right]$。即满足

\begin{equation}V = Sh = \left[\alpha_1\ \alpha_2\ \alpha_3\right] = |\alpha_1 \times \alpha_2|Prj_f\alpha_3 = |\alpha_1||\alpha_2|\sin\theta|\alpha_3|\cos\beta \tag{c}\end{equation}

**也就是说三向量的混合积本质上就是这三个向量构成的平行六面体的体积**。

上述已经给出了三个向量构成的平行六面体的体积的混合积表达式，接下来将这一本质上是求体积的混合积表达式转化为也是本质上求体积的行列式表达形式，即求三向量构成平行六面体的体积的不同表达形式。

向量$\alpha_1,\alpha_2$的向量积的坐标表达式为

\begin{equation}\alpha_1 \times \alpha_2 = \begin{vmatrix}i & j & k\newline a_{11} & a_{12} & a_{13}\newline a_{21} & a_{22} & a_{23}\end{vmatrix} = \begin{vmatrix}a_{12} & a_{13}\newline a_{22}&a_{23}\end{vmatrix}i - \begin{vmatrix}a_{11} & a_{13}\newline a_{21}&a_{23}\end{vmatrix}j + \begin{vmatrix}a_{11} & a_{12}\newline a_{21}&a_{22}\end{vmatrix}k\end{equation}

再求上述向量（法向量）与向量$\alpha_3$的数量积，整体上即为混合积。由数量积的坐标表达式，得

\begin{equation}\left[\alpha_1\ \alpha_2\ \alpha_3\right] = (\alpha_1 \times \alpha2)·\alpha_3 = a_{31}\begin{vmatrix}a_{12} & a_{13}\newline a_{22}&a_{23}\end{vmatrix} - a_{32}\begin{vmatrix}a_{11} & a_{13}\newline a_{21}&a_{23}\end{vmatrix} + a_{33}\begin{vmatrix}a_{11} & a_{12}\newline a_{21}&a_{22}\end{vmatrix} = \begin{vmatrix}a_{11} & a_{12} & a_{13}\newline a_{21} & a_{22} & a_{23}\newline a_{31} & a_{32} & a_{33}\end{vmatrix}\tag{d}\end{equation}

从而$(c),(d)$式即为三向量构成平行六面体的体积的不同表达式。思考这样一个问题，三向量构成的平行六面体的体积何时为0？

从$(c)$式看，若向量$\alpha_1,\alpha_2$的夹角$\theta$为0，即$\sin\theta = \sin 0 = 0$，那么$(c)$式将为0；另一方面，若向量$\alpha_3$与法向量$f$垂直，即$\beta = \pi / 2$，那么$\cos\beta = \cos \pi / 2 = 0$。这两种情况都会导致平行六面体的体积为0。

+ 对于第一种情况，若$\sin\theta = 0$，即代表向量$\alpha_1,\alpha_2$共线；
+ 对于第二种情况，由于法向量$f$垂直于向量$\alpha_1,\alpha_2$，而向量$\alpha_3$又垂直于法向量$f$，可得向量$\alpha_3$与向量$\alpha_1$或$\alpha_2$共线。

综合上述两种情况，若存在任意向量共线，那么平行六面体的体积必然为0，这一结论是基于平行六面体的表达式$(c)$得出的，现将这一结论迁移到行列式表达形式$(d)$上。

将$(d)$式表达为矩阵形式，为一方阵，记为$(e)$式，

\begin{equation}\begin{bmatrix}a_{11} & a_{12} & a_{13}\newline a_{21} & a_{22} & a_{23}\newline a_{31} & a_{32} & a_{33}\end{bmatrix}\tag{e}\end{equation}

从而$(d)$式为方阵$(e)$式的行列式。

若该方阵（$(e)$式）满秩，即矩阵线性无关，也就是不存在共线的向量，那么根据上述两种情况可知，该矩阵构成的平行六面体的体积不为0；若该方阵为奇异矩阵，则矩阵线性相关，那么必然存在共线的向量，从而构成的平行六面体的体积必然为0。这便是迁移之后的结论，但是这一结论有什么意义呢？

由于矩阵本质上是映射，更具体而言，该映射对应着空间的基变换，即将自然基映射到非自然基上（不一定是标准正交基）。特别地，矩阵逆映射的前提之一是该矩阵必须是一个方阵（对其施加初等变换之前），若方阵（比如式(e)）线性无关，即满秩，那么变换后的基一定不共线，也就是说空间的维度未发生任何变化，只是在该空间中的向量的坐标会发生改变。若矩阵满秩，则线性方程组必然有唯一解，即为单射，那么必然存在逆矩阵可以实现逆映射。我们判断一个矩阵是否可逆的手段之一，是通过判断该矩阵的行列式是否为0，若矩阵的行列式不为0，那么根据$(c)$式，组成该矩阵行列式的向量必然不共线，即矩阵线性无关，那么必然满秩，即存在逆矩阵。这就是我们以方阵的行列式是否为0来判断矩阵是否可逆的理论依据。

若方阵的行列式为0，那么必然存在共线的向量，从而矩阵一定不满秩，也就是说该矩阵进行基变换之后压缩了空间，从而变成了多对一映射，那么该矩阵必然不存在逆矩阵。

以上便是行列式几何意义的推导与应用。