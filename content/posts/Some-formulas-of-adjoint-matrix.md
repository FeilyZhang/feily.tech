---
title: "Some formulas of adjoint matrix"
date: 2020-07-13T11:03:05+08:00
categories: ["linear algebra"]
tags: ["linear algebra"]
draft: false
---

关于伴随矩阵的几个数学公式的推导，首先伴随矩阵的代数定义为

\begin{equation}AA^* = A^*A = \big|A\big|E\end{equation}

### **逆矩阵的伴随矩阵求法**

对于上式，若对等式$AA^* = \big|A\big|E$两边左乘$A$的逆$A^{-1}$(或对等式$A^*A = \big|A\big|E$右乘$A$的逆$A^{-1}$)，并整理可得矩阵$A$的逆矩阵$A^{-1}$的伴随矩阵求法

\begin{equation}A^* = A^{-1}\big|A\big|,\ \ \ A^{-1} = \frac{1}{\big|A\big|}A^*\end{equation}

### **伴随矩阵的性质与重要公式**

对任意$n$阶方阵$A$，都有伴随矩阵$A^*$，且有公式

\begin{equation}AA^* = A^*A = \big|A\big|E\,\ \ \ \big|A^*\big| = \big|A\big|^{n - 1}\end{equation}

那么后者如何得出的呢？

根据方阵乘积的行列式性质，若$A$,$B$均为同阶方阵，则$\big|AB\big| = \big|A\big|\big|B\big|$。那么对伴随矩阵的定义等式两边同取行列式，再利用同阶方阵乘积的行列式性质，最终化简可得

\begin{equation}\big|AA^*\big| = \big|\big|A\big|E\big|, \ \ \ \big|A\big|\big|A^*\big| = \big|A\big|^n,\ \ \ \big|A^*\big| = \big|A\big|^{n - 1}\\end{equation}

上面第二个式子右部之所以为$\big|A\big|^n$，是因为第一个式子右部中$\big|A\big|$为一个数，数$\big|A\big|$与矩阵$E$的乘积均会施加到矩阵的每一行(或每一列)上，若对数乘矩阵$\big|A\big|E$取行列式$\big|\big|A\big|E\big|$，那么对每一行的数乘都应该提出来，由于矩阵A为$n$阶方阵，因此必然会提取数乘$n$次，即$\big|A\big|^n$，单位阵$E$的行列式为1，因此等式右部为$\big|A\big|^n$。因此最终得，$A$的伴随的行列式为$A$的行列式的$n - 1$次方。

若对$AA^* = \big|A\big|E$右乘$(A^*)^{-1}$(或对$A^*A = \big|A\big|E$左乘$(A^*)^{-1}$)，那么可整理得下式

\begin{equation}A = \big|A\big|(A^*)^{-1}\end{equation}

表明方阵$A$可表示为方阵$A$的行列式与方阵$A$的伴随矩阵的数乘。

### **对伴随矩阵定义的推广**

对于伴随矩阵的代数定义

\begin{equation}AA^* = A^*A = \big|A\big|E\end{equation}

若矩阵$A$分别为$kA$,$A^T$,$A^{-1}$,$A^*$，则可推广出如下式

\begin{equation}(kA)(kA)^* = \big|kA\big|E\end{equation}

\begin{equation}A^T(A^T)^* = \big|A^T\big|E\end{equation}

\begin{equation}A^{-1}(A^{-1})^* = \big|A^{-1}\big|E\end{equation}

\begin{equation}A^*(A^*)^* = \big|A^*\big|E\end{equation}

### **方阵A的转置、逆、伴随的伴随**

#### **方阵A的转置的伴随**

方阵$A$的转置的伴随等于方阵$A$的伴随的转置，即

\begin{equation}(A^T)^* = (A^*)^T\end{equation}

那么如何得来的呢？对于上式右部，有$A^* = \big|A\big|A^{-1}$，并且对于逆的转置为转置的逆，从而得

\begin{equation}(A^*)^T = (\big|A\big|A^{-1})^T = \big|A\big|(A^T)^{-1}\end{equation}

由于行列式$\big|A\big|$与其转置行列式相等，即对于方阵，有$\big|A^T\big| = \big|A\big|$，那么对于上式有

\begin{equation}\big|A\big|(A^T)^{-1} = \big|A^T\big|(A^T)^{-1} = (A^T)^*\end{equation}

从而证明

\begin{equation}(A^T)^* = (A^*)^T\end{equation}

#### **方阵A的逆的伴随**

方阵$A$的逆的伴随为方阵$A$的伴随的逆，即

\begin{equation}(A^{-1})^* = (A^*)^{-1}\end{equation}

对于等式左部，根据推广式$A^{-1}(A^{-1})^* = \big|A^{-1}\big|E$，可知

\begin{equation}(A^{-1})^* = A\big|A^{-1}\big|E\ = A\big|A\big|^{-1}\end{equation}

对于等式右部，由于$A^* = \big|A\big|A^{-1}$，那么$(A^*)^{-1}$就为

\begin{equation}(A^*)^{-1} = (\big|A\big|A^{-1})^{-1} = \frac{1}{\big|A\big|}A\end{equation}

从而等式左右部均相等，进而等式得到推导。

#### **方阵A的伴随的伴随**

方阵A的伴随的伴随为

\begin{equation}(A^*)^* = \big|A\big|^{n-2}A\end{equation}

由于$A^* = \big|A\big|A^{-1}$，那么

\begin{equation}(A^*)^* = \big|\big|A\big|A^{-1}\big|(\big|A\big|A^{-1})^{-1} = |A|^n\big|A\big|^{-1}\big|A\big|^{-1}A = \big|A\big|^{n-2}A\end{equation}

可见，该定义是递归的。

### **两方阵乘积的伴随**

对于方阵$A, B$，其乘积的伴随定义为

\begin{equation}(AB)^* = B^*A^*\end{equation}

对于该等式的推导，从其右部开始。$B$和$A$的伴随分别定义为

\begin{equation}B^* = \big|B\big|B^{-1}\end{equation}

\begin{equation}A^* = \big|A\big|A^{-1}\end{equation}

将二者相乘，最终可得推导

\begin{equation}B^\*A^\* = \big|B\big|B^{-1}\big|A\big|A^{-1} = \big|A\big|\big|B\big|B^{-1}A^{-1} = \big|AB\big|(AB)^{-1} = (AB)^*\end{equation}