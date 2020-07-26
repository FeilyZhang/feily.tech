---
title: "From vector group to linear system of equations"
date: 2020-07-16T01:35:05+08:00
categories: ["linear algebra"]
tags: ["linear algebra"]
draft: false
---

$n$个数构成的有序数组$\left[a_1,a_2,\cdots,a_n\right]$称为一个$n$维向量，记作$\pmb{\alpha} = \left[a_1,a_2,\cdots,a_n\right]$，并称$\pmb{\alpha}$为$n$维行向量，$\pmb{\alpha}^T = \left[a_1,a_2,\cdots,a_n\right]^T$称为$n$维列向量，其中$a_i$称为向量$\pmb{\alpha}$(或$\pmb{\alpha}^T$)的第$i$个分量。

例如在三维空间中，可分别使用三个向量描述其标准正交基，分别为$\pmb{\alpha_1} = \left[1, 0, 0\right]^T, \ \pmb{\alpha_2} = \left[0, 1, 0\right]^T, \ \pmb{\alpha_3} = \left[0, 0, 1\right]^T$，亦可由向量组$\pmb{\alpha} = [\pmb{\alpha_1}, \pmb{\alpha_2}, \pmb{\alpha_3}]$描述之。

### **向量组的线性相关性**

线性相关的标准定义为

对$m$个$n$维向量$\pmb{\alpha_1, \alpha_2, \cdots, \alpha_m}$，若存在一组不全为零的数$k_1, k_2, \cdots, k_m$，使得

\begin{equation}k_1\pmb{\alpha_1} + k_2\pmb{\alpha_2} + \cdots + k_m\pmb{\alpha_m} = 0,\end{equation}

则称向量组$\pmb{\alpha_1, \alpha_2, \cdots, \alpha_m}$线性相关。

上述定义实际上意味着向量组$\pmb{\alpha_1, \alpha_2, \cdots, \alpha_m}$对零向量的表示不唯一(即$m$个$n$维向量$\pmb{\alpha_1, \alpha_2, \cdots, \alpha_m}$的系数不全为零)。

举个例子，对于4个3维向量，分别为$\pmb{\alpha_1} = \left[1, 0, 0\right]^T, \pmb{\alpha_2} = \left[0, 1, 0\right]^T, \pmb{\alpha_3} = \left[0, 0, 1\right]^T, \pmb{\alpha_4} = \left[0, 0, 3\right]^T$组成的向量组$\pmb{\alpha} = [\pmb{\alpha_1}, \pmb{\alpha_2}, \pmb{\alpha_3}, \pmb{\alpha_4}]$，其对零向量的表示除过各向量系数分别为$\left[0, 0, 0, 0\right]^T$外，还存在另一种表示，即系数分别为$\left[0, 0, 1, -\frac{1}{3}\right]^T$。可见，向量组$\pmb{\alpha} = [\pmb{\alpha_1}, \pmb{\alpha_2}, \pmb{\alpha_3}, \pmb{\alpha_4}]$对零向量的表示不唯一，即该向量组线性相关。

与线性相关相反的是，线性无关要求向量组对零向量的表示唯一，即只能存在一种情况，那就是各系数分别为0，也就是$\left[0, 0, \cdots, 0\right]^T$。

继续以上述向量组为例，现在去掉向量组的第四个向量$\pmb{\alpha_4} = \left[0, 0, 3\right]^T$,即向量组由$\pmb{\alpha_1} = \left[1, 0, 0\right]^T, \pmb{\alpha_2} = \left[0, 1, 0\right]^T, \pmb{\alpha_3} = \left[0, 0, 1\right]^T$三个向量组成，该向量组对零向量的表示方法一定唯一，即$\pmb{\alpha_1, \alpha_2, \alpha_3}$的系数分别为0，即将这三个向量分别压缩到0，从而实现对零向量的唯一表示。那么，线性无关的标准定义如下

若不存在不全为零的数$k_1, k_2, \cdots, k_m$，使得

\begin{equation}k_1\pmb{\alpha_1} + k_2\pmb{\alpha_2} + \cdots + k_m\pmb{\alpha_m} = 0\end{equation}

成立，就称向量组$\pmb{\alpha_1, \alpha_2, \cdots, \alpha_m}$线性无关。即只有当$k_1 = k_2 = \cdots = k_m = 0$时，才使得下式成立

\begin{equation}k_1\pmb{\alpha_1} + k_2\pmb{\alpha_2} + \cdots + k_m\pmb{\alpha_m} = 0\end{equation}

### **从向量组的线性相关性到线性表示**

首先要解释线性组合的概念。

#### **线性组合与线性表示**

设有$m$个$n$维向量$\pmb{\alpha_1, \alpha_2, \cdots, \alpha_m}$及$m$个数$k_1, k_2, \cdots, k_m$，则向量

\begin{equation}k_1\pmb{\alpha_1} + k_2\pmb{\alpha_2} + \cdots + k_m\pmb{\alpha_m}\end{equation}

称为向量组$\pmb{\alpha_1, \alpha_2, \cdots, \alpha_m}$的线性组合。

从这个角度看，不管向量组线性相关还是无关，零向量都是向量组$\pmb{\alpha_1, \alpha_2, \cdots, \alpha_m}$的线性组合。换句话说，零向量可以由向量组$\pmb{\alpha_1, \alpha_2, \cdots, \alpha_m}$线性表示。线性组合与线性表示的内涵其实是一致的，目标向量是向量组的线性组合，也可以说成目标向量可以被向量组线性表示。

#### **有关线性无关的向量组的线性表示**

若$m$个$n$维向量组成的向量组线性无关，那么在由该线性无关的向量组张成的$m$维空间当中，所有的向量均可被这线性无关的$m$个$n$维向量线性表示。例如

+ 对于由1个3维向量$\left[1, 0, 0\right]^T$张成的一维空间当中(即三维空间当中的x轴)，如果对该三维向量进行释放的放缩(数乘)，该向量可表示一维空间当中的任意向量；
+ 对于由2个3维向量$\left[1, 0, 0\right]^T,\left[0, 1, 0\right]^T$张成的二维空间(即三维空间中由x轴和y轴构成的平面)，如果对这两个三维向量分别施加适当的放缩等(数乘及向量加法)，那么这两个向量可以表示二维空间当中的任意向量；
+ 对于由3个3维向量$\left[1, 0, 0\right]^T,\left[0, 1, 0\right]^T,\left[0, 0, 1\right]^T$张成的三维空间(即三维空间中由x轴、y轴和z轴构成的超平面)，如果对这三个三维向量分别施加适当的放缩等(数乘及向量加法)，那么这三个向量可以表示三维空间当中的任意向量。

以上便是对线性无关的向量组对其张成的空间中向量的线性表示的说明。线性无关的向量组中，是不存在某一向量被其余向量线性表示的，比如在由3个3维向量$\left[1, 0, 0\right]^T,\left[0, 1, 0\right]^T,\left[0, 0, 1\right]^T$张成的三维空间中，向量$\left[0, 1, 0\right]^T$是无法被其余向量线性表示的。这从侧面说明了，线性无关的任意向量提供的信息对于其组成的向量组来说都是不可替代的，是唯一的，其构成了组成向量组的基本尺度。但是对于线性相关的向量组却不尽然。

#### **有关线性相关的向量组的线性表示**

继续以向量$\pmb{\alpha_1} = \left[1, 0, 0\right]^T, \pmb{\alpha_2} = \left[0, 1, 0\right]^T, \pmb{\alpha_3} = \left[0, 0, 1\right]^T, \pmb{\alpha_4} = \left[0, 0, 3\right]^T$组成的向量组来说明。该向量组对零向量的表示通过移项可化为

\begin{equation}k_1\left[1, 0, 0\right]^T + k_2\left[0, 1, 0\right]^T + k_3\left[0, 0, 1\right]^T = \left[0, 0, 3\right]^T\end{equation}

不难发现，$k_1 = 0, k_2 = 0, k_3 = 3$。另一方面，上式也说明了，向量$\left[0, 0, 3\right]^T$可以被向量组$\pmb{\alpha_1}, \pmb{\alpha_2}, \pmb{\alpha_3}$线性表示。

从而，我们可以得知，若向量组线性相关(即对零向量的表示法不唯一，也就是各向量的系数不恒为0)，那么该向量组中一定存在向量可以被其余线性无关的向量线性表示。究其缘由，是因为可被线性表示的向量本身并未对向量空间的张成做出任何不可被替代的贡献，即被线性表示的向量实质上属于表示被线性表示的向量张成的线性空间中的一个具体的向量，仅此而已，因此可被线性表示。

更进一步地，可以得到如下关于判别线性相关性的定理。

1. 向量组$\pmb{\alpha_1, \alpha_2, \cdots, \alpha_m}(m\ge2)$线性相关的充要条件是向量组中至少有一个向量可以由其余$m - 1$个向量线性表示.
2. 若向量组$\pmb{\alpha_1, \alpha_2, \cdots, \alpha_m}(m\ge2)$线性无关，而$\pmb{\beta, \alpha_1, \alpha_2, \cdots, \alpha_m}$线性相关，则$\beta$可由$\pmb{\alpha_1, \alpha_2, \cdots, \alpha_m}$线性表示，且表示法唯一.
3. 如果向量组$\pmb{\beta_1, \beta_2, \cdots, \beta_t}$可由向量组$\pmb{\alpha_1, \alpha_2, \cdots, \alpha_s}$线性表示，且$t > s$，则$\pmb{\beta_1, \beta_2, \cdots, \beta_t}$线性相关.
4. 向量组$\pmb{\alpha_1, \alpha_2, \cdots, \alpha_m}$线性相关的充分必要条件是齐次线性方程组$\left[\pmb{\alpha_1, \alpha_2, \cdots, \alpha_m}\right]\left[\pmb{x_1, x_2, \cdots, x_m}\right]^T = \pmb{0}$有非零解(零解之外的解)，其中$m$个$n$维向量$\pmb{\alpha_m}$的形式分别为$\left[a_{1m},a_{2m},\cdots,a_{nm}\right]^T$.
5. 若向量$\pmb{\beta}$可由向量组$\pmb{\alpha_1, \alpha_2, \cdots, \alpha_m}$线性表示<->非齐次线性方程组$\left[\pmb{\alpha_1, \alpha_2, \cdots, \alpha_m}\right]\left[\pmb{x_1, x_2, \cdots, x_m}\right]^T = x_1\pmb{\alpha_1} +  x_2\pmb{\alpha_2} + \cdots +  x_m\pmb{\alpha_m} = \pmb{\beta}$有解<->$r(\left[\pmb{\alpha_1, \alpha_2, \cdots, \alpha_m}\right]) = r(\left[\pmb{\alpha_1, \alpha_2, \cdots, \alpha_m, \beta}\right])$.
6. 若向量组$\pmb{\alpha_1, \alpha_2, \cdots, \alpha_m}$中有部分组线性相关，那么整个向量组也线性相关.
7. 如果一组$n$维向量$\pmb{\alpha_1, \alpha_2, \cdots, \alpha_m}$线性无关，那么对这些向量各任意添加$s$个分量所得到的新向量($m+s$维)组$\pmb{\alpha_{1}^* \alpha_{2}^*, \cdots, \alpha_{m}^*}$也是线性无关的；如果$\pmb{\alpha_1, \alpha_2, \cdots, \alpha_m}$线性相关，那么它们各自去掉相同的若干分量所得到的新向量组也是线性相关的.

### **从向量组的线性相关性到线性方程组**

向量组的线性相关性是判断线性方程组是否有解的利器，通过上述铺垫，接下来便可利用向量组的线性相关性解释线性方程组的解。

#### **对齐次线性方程组是否有解的解释**

方程组

\begin{equation}
\begin{cases}
a_{11}x_{1} + a_{12}x_{2} + \cdots + a_{1n}x_{n} = 0,\newline
a_{21}x_{1} + a_{22}x_{2} + \cdots + a_{2n}x_{n} = 0,\newline
\cdots\cdots\newline
a_{m1}x_{1} + a_{m2}x_{2} + \cdots + a_{mn}x_{n} = 0\newline
\end{cases}
\end{equation}

称为$m$个方程$n$个未知量的齐次线性方程组，其向量形式为

\begin{equation}x_1\pmb{\alpha_1} + x_2\pmb{\alpha_2} + \cdots + x_n\pmb{\alpha_n} = \pmb{o}\end{equation}

其中

\begin{equation}\pmb{\alpha_j} = 
\begin{bmatrix} a_{1j} \newline a{2j} \newline \vdots \newline a_{mj}\end{bmatrix},j = 1, 2, \cdots, n.
\end{equation}

其矩阵形式为

\begin{equation}\pmb{A}_{m\times n}\pmb{x} = \pmb{o}\end{equation}

其中

\begin{equation}\pmb{A_{m\times n}} = 
\begin{bmatrix} a_{11} & a_{12} & \cdots & a_{1n} \newline  a_{21} & a_{22} & \cdots & a_{2n} \newline \vdots & \vdots & & \vdots \newline a_{m1} & a_{m2} & \cdots & a_{mn} \end{bmatrix}, \pmb{x} = 
\begin{bmatrix}x_1 \newline x_2 \newline \vdots \newline x_n\end{bmatrix}
\end{equation}

对于齐次线性方程组，很容易发现，其本质就是判断给定的向量组$\pmb{A}$对零向量的表示是否唯一，也就是判断向量组$\pmb{A}$是否线性无关，或者说是否满秩。若向量组$\pmb{A}$满秩，那么必然线性无关(或者$\pmb{A}$线性无关，其必然满秩)，那么此时方程只有唯一的的零解；否则方程有非零解。

以上便是通过向量组的线性相关性对齐次线性方程组的解进行的解释。

#### **对非齐次线性方程组是否有解的解释**

方程组

\begin{equation}
\begin{cases}
a_{11}x_{1} + a_{12}x_{2} + \cdots + a_{1n}x_{n} = b_1,\newline
a_{21}x_{1} + a_{22}x_{2} + \cdots + a_{2n}x_{n} = b_2,\newline
\cdots\cdots\newline
a_{m1}x_{1} + a_{m2}x_{2} + \cdots + a_{mn}x_{n} = b_m\newline
\end{cases}
\end{equation}

称为$m$个方程$n$个未知量的非齐次线性方程组，其向量形式为

\begin{equation}x_1\pmb{\alpha_1} + x_2\pmb{\alpha_2} + \cdots + x_n\pmb{\alpha_n} = \pmb{b}\end{equation}

其中

\begin{equation}\pmb{\alpha_j} = 
\begin{bmatrix} a_{1j} \newline a{2j} \newline \vdots \newline a_{mj}\end{bmatrix},j = 1, 2, \cdots, n.\pmb{b} = 
\begin{bmatrix} b_{1} \newline b{2} \newline \vdots \newline b_{m}\end{bmatrix}.
\end{equation}

其矩阵形式为

\begin{equation}\pmb{A}_{m\times n}\pmb{x} = \pmb{b}\end{equation}

其中

\begin{equation}\pmb{A_{m\times n}} = 
\begin{bmatrix} a_{11} & a_{12} & \cdots & a_{1n} \newline  a_{21} & a_{22} & \cdots & a_{2n} \newline \vdots & \vdots & & \vdots \newline a_{m1} & a_{m2} & \cdots & a_{mn} \end{bmatrix}, \pmb{x} = 
\begin{bmatrix}x_1 \newline x_2 \newline \vdots \newline x_n\end{bmatrix}
\end{equation}

矩阵$\left[\pmb{A,b}\right]$称为矩阵$\pmb{A}$的增广矩阵，定义为

\begin{equation}\left[\pmb{A,b}\right] = 
\begin{bmatrix} a_{11} & a_{12} & \cdots & a_{1n} & b_1 \newline  a_{21} & a_{22} & \cdots & a_{2n} & b_2 \newline \vdots & \vdots & & \vdots & \vdots \newline a_{m1} & a_{m2} & \cdots & a_{mn}  & b_m\end{bmatrix}
\end{equation}

对于非齐次线性方程组，判断其是否有解的本质就是判断由系数矩阵$\pmb{A}$与向量$\pmb{b}$构成的增广矩阵中系数矩阵是否能够线性表示向量$\pmb{b}$。若要使得矩阵(或向量组)$\pmb{A}$可以线性表示向量$\pmb{b}$，根据上述定理2可得矩阵$\pmb{A}$必须线性无关，其增广矩阵$\left[\pmb{A,b}\right]$必须线性相关，即必须满足$r(A) = r(\left[\pmb{A,b}\right])$，方程组才有解。

实际上对于式$r(A) = r(\left[\pmb{A,b}\right])$，若其满足$r(A) = r(\left[\pmb{A,b}\right]) = n$，从而根据定理2可知(定理2本质上满足$r(\pmb{\alpha_1, \alpha_2, \cdots, \alpha_m}) = r(\pmb{\beta, \alpha_1, \alpha_2, \cdots, \alpha_m}) = n$，其中$n$为线性无关的向量的维数)，方程组有唯一解。更通俗的说，由于矩阵$\pmb{A}$线性无关且满秩，那么解方程组必然会得到一组唯一的向量$\pmb{x}$，这组向量$\pmb{x}$便是以矩阵$\pmb{A}$的每个向量为基，对其进行适当的与基对应的$\pmb{x}$的分量倍的放缩等(数乘及向量加法)，便可得到向量$\pmb{b}$，即向量$\pmb{b}$可被向量组$\pmb{A}$线性表示。

对于另一种情况，即$r(A) = r(\left[\pmb{A,b}\right]) < n$，这种情况意味着矩阵$\pmb{A}$线性相关，增广矩阵$\left[\pmb{A,b}\right]$从而也必然线性相关(根据定理6，部分组相关，则整体相关)，但是对矩阵$\pmb{A}$和其增广矩阵$\left[\pmb{A,b}\right]$进行初等行变换之后，其仍然满足$r(A) = r(\left[\pmb{A,b}\right])$，这意味着其本质上是满足方程组有解的条件的，只是此时向量组中有效信息的向量的数量少于未知数的数量。本来若向量组中有效信息的向量的数量等于未知数的数目的话，求出来的每个未知数都是对应的线性无关的向量组中对应向量缩放的尺寸，但是此时却少于这个数目，这就意味着方程组必然包含$n - r(A)$个自由未知量，$r(A)$个非自由未知量取决于自由未知量的取值，从而因自由未知量，方程组包含无穷多解。

对于最后一种情况，其实可以通过如下方程组的解给出直观的解释。有方程组

\begin{equation}
\begin{cases}
a + b + c = 1,\newline
2a + 2b + 2c = 2,\newline
3a + 3b + 3c = 3
\end{cases}
\end{equation}

理想的情况下，对于3个3维向量构成的非齐次线性方程组，是可以唯一的求出每个未知数的量的。但是对于上述三个方程，其实后两个都是多余的，即后两个可以被第一个方程线性表示，即一个是2倍，一个是3倍，被线性表示的向量均未向线性空间提供不可替代的有效信息，从而上述方程组的系数矩阵和增广矩阵的秩为1，这就意味着存在2个自由变量，也就是未知数$b$和$c$，非自由变量未知数$a$满足$a = 1 - b - c$。也就是确定了$b$和$c$的取值，$a$必然会被确定，非自由变量随自由变量而动，从而由于自由变量$b$和$c$，上述方程组存在无穷多解。

本文完