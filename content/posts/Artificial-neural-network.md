---
title: "Artificial neural network"
date: 2019-03-26T15:55:25+08:00
categories: ["Machine Learning"]
tags: ["Artificial neural network"]
draft: false
---

## 一、从生物神经网络到人工神经网络
如同人脑使用一个被称为神经元的相互连接的细胞网络来创建一个巨大的并行处理器一样，人工神经网络使用人工神经元或节点的网络来解决学习问题。既然，人工神经网络基于生物神经网络，那么有必要了解一下生物神经细胞或神经元的组成。

### 1.1 生物神经网络

![神经元结构图](https://feily.tech/pic/images/article/shenjingyuan.jpg)

如上图所示，每个神经元由包含一个细胞核的一个细胞体组成。从细胞体分支扩展出许多被称为**树突**的神经纤维和一根长的称为**轴突**的神经纤维。其中，树突接受神经冲动作为神经元的输入信号，但神经元并非对所有的神经冲动进行处理，而是在神经冲动信号达到一个阈值后才会处理并将输出信号通过轴突传递给下层神经元。**突触**则是神经元的连接，与轴突相区分，突触包含以下三种：

+ 轴突-树突突触：一个神经元的轴突与下一个神经元的树突相连；
+ 轴突-胞体突触：一个神经元的轴突与下一个神经元的胞体相连；
+ 轴突-轴突突触：一个神经元的轴突与下一个神经元的轴突相连；

![突触示意图](https://feily.tech/pic/images/article/tuchu.jpg)

神经元对神经信号的处理是有条件的，即必须达到一个阈值。如何判断呢？答案是通过神经冲动相对重要性或频率的加权和。如果加权和达到该阈值，那么神经元将会处理(激活)该神经信号，否则不予理会。事实上，每个神经元都是这么干的。那么直观上，神经元的功能就是**求和**与**激活**。

### 1.2 人工神经网络
人工神经网络作为生物神经网络的仿真，那么将概念迁移过来，首先图示如下

![神经元对比示意图](https://feily.tech/pic/images/article/artificial-neural.png)

可以看出，一个有向网络图定义了神经元对输入信号(变量X)的接收，每个特征对应一个输入，每个输入连向神经元的有向线段上表明了该信号的权重，那么众多输入信号在每个神经元处首先被加权求和，然后将加权和结果作为神经元激活函数的自变量就可以得到该神经元的输出，该输出将会作为下一个神经元的输入，下一个神经元仍然会进行同样的加权求和和激活操作。

一个典型的具有n个输入树突的神经元可以用如下公式来表示

![具备n个输入的神经元](https://feily.tech/pic/images/article/sum-function.png)

其中，权重w可以控制n个输入(x)中的每个输入对输入信号之和所做出贡献的大小。激活函数f(x)使用净总和，结果信号y(x)就是输出轴突。

### 1.3 神经网络的三个特征
定义一个人工神经网络，需要具备三个概念，如下

+ **激活函数**：将神经元的净输入(加权和)信号转化为单一的输出信号，以便进一步在网络中传播；
+ **网络拓扑**：描述神经网络中神经元的数目及层数以及它们之间的连接方式；
+ **训练算法**：指定如何设置连接权重，以便抑制或者增加神经元在输入信号中的比重。

它们之间的关系为，**网络拓扑**由一层层神经元组成，每层包含若干个神经元，这些(不同层)神经元组成了人工神经网络的基本骨架；其中每个神经元都会进行加权求和与激活操作，实质上就是每个神经元通过自己的**激活函数**将净输入转化为输出(如上面的公式所示)，以便进一步传播。而**训练算法**则是对神经网络的调整，即通过后向传播算法进行神经元权重的修正。

## 二、激活函数
激活函数是神经元处理信号的一个过程，涉及对总的输入信号进行求和，然后确定加权和是否满足阈值，如果满足，就产生输出，否则不进行任何操作。

这里只介绍带0/1输出的硬阈值激活函数(图a)和logistic激活函数(图b)，图像如下

![激活函数](https://feily.tech/pic/images/article/active-function.png)

### 2.1 带0/1输出的硬阈值激活函数
带0/1输出的硬阈值激活函数只适合问题线性可分的情况，即训练集属性组成的n维空间可以被n-1维超平面二分类。我们以训练集只有两个输入的样本为例，试说明带0/1输出的硬阈值激活函数的实际情况

下图显示了两个输入x1(横轴)和x2(纵轴)，分别代表了地震的深层和表层的震级

![地震与地下爆炸](https://feily.tech/pic/images/article/geogebra-export.png)

其中，红点代表核爆，蓝点代表真正的地震，我们可以看出，如果我们对属性x1-x2进行线性回归，那么我们会得到一条直线，直线上下方分别是分类的结果，上方为地震，下方为核爆，我们先求直线的方程式，如下

![直线方程](https://feily.tech/pic/images/article/fangchengshi.png)

那么也就是说如果直线方程求解大于0那么就属于核爆，如果小于0就属于地震。我们观察一下直线方程，可以看出1.7是变量x1的系数，也可以说是x1的**权重**，同理，-1则是x2的**权重**，而截距-4.9我们称之为**偏置**。

那么我们就可以这样定义上面的方程式

![直线方程转带权求和形式](https://feily.tech/pic/images/article/sum.png)

那么就成了，如果X大于0那么就是核爆，如果小于0就是地震，明显是个二分类问题，不妨用1代表核爆，用0代表地震，那么就有如下关系式

![带0/1输出的硬阈值激活函数表达式](https://feily.tech/pic/images/article/0-1.png)

上述关系式正是带0/1输出的硬阈值激活函数的表达式。

到这里我们可以看出，激活函数实际上屏蔽了线性回归求权重与偏置的过程。由于神经网络伊始不包含先验知识，所以刚开始随机设置权重(而不是像我们一样通过拟合方程求权重)，然后将最终的输出信号与训练集的真实目标值进行比较，如果不同，则通过后向传播算法通过梯度下降修正权重，最终得到合理的输出结果(即与训练集目标值的误差最小)。

但是实际应用当中，很少使用带0/1输出的硬阈值激活函数，因为该激活函数在X = 0处是不可微的，而且在X != 0处导数处处为0，这就导致无法在后向传播中通过梯度下降来修正权重。对于这样的分类器而言，模型的调整(权重的修正)总是会受该激活函数输出值的两个极端(0或者1)的影响,而且模型的调整(几何上就是那条一刀切的直线的调整，对应的就是权重的修正)显得困难重重而低效。而且对于线性不可分的问题无能为力，因为无法一刀切！

那么我们就需要找一个可微的激活函数，这样，在后向传播中就可以充分利用梯度下降算法来修正神经元权重。

### 2.2 logistic激活函数
该激活函数的图像如上图所示，可以看出，该激活函数是可微的，这样对于创建高效的人工神经网络优化算法是至关重要的。与带0/1输出的硬阈值激活函数比较，区别主要在

+ 带0/1输出的硬阈值激活函数的输出是二分类，不是0就是1；但是logistic激活函数输出的则是0或1的概率；
+ 带0/1输出的硬阈值激活函数是不可微的，只适合处理线性可分问题；但是logistic激活函数是可微的，可以通过梯度下降来修正神经元权重，这样模型的调整非常高效。

logistic激活函数对于线性可分的情况，回归收敛速度较慢但表现得更加可预期；对于数据含噪音，且非线性可分的情况，logistic激活函数回归收敛速度明显更快且更稳定。

## 三、网络拓扑
### 3.1 层的数目
单层网络只有一组连接权重，适用于基本的模式分类，特别适用于线性可分的问题；

多层网络添加了一个或者多个隐藏层，它们(隐藏层)在信号到达输出节点之前处理来自节点的信号。

### 3.2 信息传播方向

![前馈网络](https://feily.tech/pic/images/article/qiankuiwangluo.jpg)

前馈网络：神经网络中的信号在一个方向上从一个节点到另一个节点连续的传送，直至到达输出层；

![递归网络](https://feily.tech/pic/images/article/diguiwangluo.gif)

递归网络：允许信号使用循环在两个方向上传播；

![多层前馈网络](https://feily.tech/pic/images/article/duocengwangluo.jpg)

多层前馈网络：或称为多层感知器，有一个输入层，中间有一个或多个隐含层，有一个输出层，是人工神经网络拓扑结构中的事实标准。

### 3.3 每一层节点数
输入节点的个数由训练集数据的特征数量而定。而输出节点的个数由结果中分类水平数目而定，但是隐藏层节点的个数确定起来则没有这么容易，要根据输入节点的个数、训练数据的数量、噪声数据的数量，以及许多其他因素的影响而定。

需要注意的是，过多的隐藏层神经元有理由训练出更严格的模型，但也可能会有过度拟合的风险。一般情况下，至少有一个多神经元隐藏层。

## 四、训练算法(梯度下降与后向传播)

### 4.1 后向传播算法
该算法通过两个过程的多次循环进行迭代，每次迭代称为一个**新纪元**。由于初始的神经网络里面不包含先验知识，所以在开始之前先随机设定网络拓扑中每个输入的权重，然后算法通过过程循环，直到达到一个停止准则。该循环过程包括：

+ 在前向阶段中，神经元在输入层到输出层的序列中被激活，沿途应用每一个神经元的权重和激活函数，一旦到达最后一层，就产生一个输出信号。
+ 在后向阶段中，由前向阶段产生的网络输出信号与训练数据中的真实目标值进行比较，网络的输出信号与真实目标值之间的差异产生的误差在网络中向后传播，从而来修正神经元之间的连接权重，从而减少神经网络最终输出值与训练集数据目标值的误差。

但是，每个神经元的输入与输出之间的关系很复杂，我们又如何确定神经元的权重该改变多少呢？

答案是通过**梯度下降算法**来实现。

### 4.2 梯度下降算法
首先看一下什么是梯度下降算法。

### 4.2.1 一个例子
首先我们有一个可微的函数，假设这个函数代表一座山，一个人因为大雾被困在山顶，由于山上浓雾很大，他想下山应该怎么做？注意，由于大雾的影响他只能看见较近的地方，这样他每次只能走最陡峭的地方，因为下降的幅度更大，更容易接近山底(虽然现实当中很危险，但是这里是在解释算法，不必较真)，他每走一步都选择最陡峭的地方走，这样就会很快下山。

![慢慢就会走到山底](https://feily.tech/pic/images/article/valley.png)

这座山的前提是一个可微函数，那么最陡峭也就意味着斜率最大，由于是下山，那么斜率必然是负数，那么就可以用如下数学形式定义这个人的下次落脚点

![下一步的位置](https://feily.tech/pic/images/article/luojiaodian.png)

其中Θ1表示下一次的位置，Θ0表明本次的位置，α表示步长，最后一个参数表示我们山的陡峭程度，如果我们的山越陡峭，同时这个人的步长α越大，那么他走的肯定很快，但是同样的，他有可能会错过最佳的山底位置，因为他看不到。如果步长小，那么他会走的慢一点，这样他就不会错过最佳的山底位置。

以上是梯度下降法在下山上的应用，你只需要记住一点，就是**步长(学习率)的大小决定了下山的快慢以及与目标的接近程度**。

我们回过头来再看梯度下降法的数学定义。

梯度实际上就是多变量微分的一般化，即对每个变量分别微分，然后组合在一起，形成一个向量，那么必然的有以下结论：

+ **在单变量函数中，梯度其实就是函数的微分，代表着函数在某个给定点的切线的斜率，即变化率**；
+ **在多变量函数中，梯度是一个向量，梯度的方向就指出了函数在给定点的上升(或下降，因符号而异)最快的方向**。

### 4.2.2 单变量函数梯度
那么在单变量情况下，我们求一下单变量函数的梯度下降，假定我们有如下单变量函数

![单变量函数](https://feily.tech/pic/images/article/danbianliangtidu.png)

初始化起点θ0 = 1，学习率α = 0.4，然后进行梯度下降计算

![计算单变量梯度](https://feily.tech/pic/images/article/tidudanbianliang.png)

如果这个人登山的函数就是这个单变量函数的话，那么也就意味着他只需要四次就可以下山(到达函数最低点)

![单变量梯度下降](https://feily.tech/pic/images/article/jieguotidu.png)

### 4.2.3 多变量函数梯度
现假设有如下多变量函数

![多变量函数](https://feily.tech/pic/images/article/duobianliangtidu.png)

初始化起点与步长Θ0 = (1,3)，α = 0.1，该多变量函数梯度如下

![多变量函数梯度](https://feily.tech/pic/images/article/tidu.png)

然后进行梯度下降计算

![计算多变量梯度](https://feily.tech/pic/images/article/duobianliangtidujisuan.png)

然后，随着梯度下降，我们发现，实际上已经慢慢的到达了多变量函数的最小值点

![多变量梯度下降](https://feily.tech/pic/images/article/resultduobianliang.png)

### 4.2.4 回到后向传播算法
上述通过对激活函数的阐述，我们可以知道，影响神经网络输出值与训练集目标值之间误差大小的参数就是各神经元的权重以及偏置，若各神经元权重与偏置构成的多变量成本函数**J(w, b)**越大，最终神经网络输出值与训练集目标值之间的误差越大，那么相应的，我们如果求多变量成本函数**J(w, b)**最小值，那么也就意味着神经网络输出值与训练集目标值之间的误差越小。

![成本函数最小值](https://feily.tech/pic/images/article/chengbenhanshu.png)

所以，在后向传播算法的后向阶段，我们修正神经元的核心技术就是通过梯度下降算法最小化我们的成本函数**J(w, b)**,对应的就是求由w、b以及目标成本函数J(w, b)间关系构成的二次曲面的最低点，也就是成本函数的最小值。

那么运用梯度下降，以此来修正神经元权重与偏置

![梯度下降](https://feily.tech/pic/images/article/luojiaodian.png)

事实上，在后向传播中与学习率α(步长)相乘的成本函数的梯度就是总误差对相邻节点(神经元)权重的导数，这里需要通过链式求导来计算，因为神经网络输出的神经元还进行了一次求加权和的操作，然后将此加权和作为当前节点(或神经元)的激活函数的输入最终产生输出。

### 4.3 通过梯度下降法更新神经元权重

我们考虑一个全新的例子，这里基本上将会综合上述所有内容。假定有如下神经网络拓扑

![神经网络拓扑](https://feily.tech/pic/images/article/example0.png)

我们给输入赋初值，并加上权重以及偏置，并对输出再赋值以进行比较确定误差

![带权重、偏置与初值的神经网络拓扑](https://feily.tech/pic/images/article/neural_network-9.png)

接下来**前向传播**

计算h1神经元的输入加权和以及以logistic作为激活函数的输出

![h1神经元加权和](https://feily.tech/pic/images/article/example1.png)

![h1神经元激活函数输出](https://feily.tech/pic/images/article/example2.png)

同样的方法计算h2神经元的激活函数输出

![h2神经元激活函数输出](https://feily.tech/pic/images/article/example3.png)

同样的方法再计算o1和o2的激活函数输出

![o1加权和与激活函数输出](https://feily.tech/pic/images/article/example4.png)

![o2激活函数输出](https://feily.tech/pic/images/article/example5.png)

前向传播中我们得到的目标输入为[0.75136079 , 0.772928465]，但是训练集目标值为[0.01 , 0.99]，可见误差还很大。接下来，我们通过后向传播来训练神经网络，修正神经元权重，降低误差


接下来就是**后向传播**并通过梯度下降法修正权重，其中成本函数的梯度就是总误差对某神经元链式求导。首先计算它们的总误差，公式为

![o2激活函数输出](https://feily.tech/pic/images/article/latex.png)

先分别计算o1与o2对应训练集目标值的误差，然后加总

![o1相对于训练集目标值误差](https://feily.tech/pic/images/article/latex1.png)

![o2相对于训练集目标值误差](https://feily.tech/pic/images/article/latex2.png)

![总误差](https://feily.tech/pic/images/article/latex3.png)

先求成本函数的梯度，即总误差关于神经元h1上权重w5的导数，这是一个链式求导的过程，如下图所示

![链式求导](https://feily.tech/pic/images/article/output_1_backprop-4.png)

开始求导，分别求各部分

![第一部分](https://feily.tech/pic/images/article/example6.png)

![第二部分](https://feily.tech/pic/images/article/examle7.png)

![第三部分](https://feily.tech/pic/images/article/example8.png)

然后各部分相乘

![三部分相乘](https://feily.tech/pic/images/article/example9.png)

然后我们就可以更新权重w5的值了，如下

![更新w5权重](https://feily.tech/pic/images/article/latex4.png)

类似的方式我们可以更新其余权重，如下

![更新其余权重](https://feily.tech/pic/images/article/example10.png)

以上是与总误差最关联的输出层的权重更新，对于隐含层而言，更新权重示意图及公式如下

![更新隐藏层神经元权重示意图](https://feily.tech/pic/images/article/nn-calculation.png)

可见链式传导路径为`out(h1) ——> net(h1) ——> w1`, 在前向传播中out(h1)会影响out(o1)和out(o2), 那么对应的在后向传播中计算E(total)关于out(h1)的导数必然要考虑out(h1)它对两个输出神经元的影响，那么E(total)关于out(h1)的导数如下计算

![E(total)关于out(h1)的导数计算方式](https://feily.tech/pic/images/article/latex5.png)

下面开始梯度下降更新w1的权重

![](https://feily.tech/pic/images/article/latex6.png)

![](https://feily.tech/pic/images/article/latex7.png)

![](https://feily.tech/pic/images/article/latex8.png)

![](https://feily.tech/pic/images/article/latex9.png)

![](https://feily.tech/pic/images/article/latex10.png)

以上计算出了E(o1)关于out(h1)的导数，下面用同样的方式计算E(o2)关于out(h1)的导数，过程略

![](https://feily.tech/pic/images/article/latex11.png)

根据公式，二者相加，得到E(total)关于out(h1)的导数

![](https://feily.tech/pic/images/article/latex12.png)

下面就简单了，计算out(h1)关于net(h1)的导数

![](https://feily.tech/pic/images/article/latex13.png)

![](https://feily.tech/pic/images/article/latex14.png)

然后再计算net(h1)关于w1的导数，如下

![](https://feily.tech/pic/images/article/latex15.png)

![](https://feily.tech/pic/images/article/latex16.png)

最终三者相乘链式求导得到E(total)关于w1的导数

![](https://feily.tech/pic/images/article/latex17.png)

![](https://feily.tech/pic/images/article/latex18.png)

然后就可以根据梯度下降公式更新w1的权重

![](https://feily.tech/pic/images/article/latex19.png)

同样的方式更新w2、w3、w4的权重，如下，过程略

![](https://feily.tech/pic/images/article/latex20.png)

![](https://feily.tech/pic/images/article/latex21.png)

![](https://feily.tech/pic/images/article/latex22.png)

这样，我们就梯度不断下降，最终已经找到了多变量成本函数**J(w, b)**的最小值，那么也就意味着神经网络输出节点与训练集目标值之间的误差已经最小了。

我们再次通过训练后的神经网络重新计算，输出节点得到的值为[0.015912196,0.984065734]，而训练集目标值为[0.01 , 0.99], 可见，误差已经相当低了。

## 五、R语言中神经网络实现与应用
### 5.1 R语言中神经网络函数用法
我们使用neuralnet添加包中的`neuralnet()`函数来实现神经网络，函数原型为

首先建立模型
```R
m <- neuralnet(target ~ predictors, data = mydata, hidden = 1)
```
参数解释如下

+ target：数据框mydata中需要建模的输出变量；
+ predictors：是数据框mydata中用于预测的特征集(是一个公式)；
+ data：训练集数据框；
+ hidden：神经网络隐藏层神经元数目，默认为1。

该函数返回一个神经网络模型对象

然后进行预测
```R
p <- compute(m, test)
```

参数解释如下

+ m：`neuralnet()`函数创建的模型对象；
+ test：测试集数据框。

该函数返回一个二元素列表，名称分别是`$neurons`和`$net.result`，前者用于保存神经网络中每一层的神经元，后者用于保存模型的预测值。

### 5.2 R语言中neuralnet()函数应用示例
以《Machine Learning With R》一书中的根据混合物水分特征预测混凝土强度的数据集为例

先读取数据，然后进行数据标准化操作，因为神经网络运行时最好的情况就是将输入数据缩放到0附近的狭窄范围内，我们使用离差标准化，将数据映射到[0, 1]区间，由于数据集已经是随机排列，那么直接提取训练集与测试集
```R
> getwd()
[1] "C:/Users/Administrator/Documents"
> setwd("C:\\Users\\Administrator\\Desktop\\docs\\Machine-Learning-with-R-datasets-master")
> concrete <- read.csv("concrete.csv")
> str(concrete)
'data.frame':	1030 obs. of  9 variables:
 $ cement      : num  540 540 332 332 199 ...
 $ slag        : num  0 0 142 142 132 ...
 $ ash         : num  0 0 0 0 0 0 0 0 0 0 ...
 $ water       : num  162 162 228 228 192 228 228 228 228 228 ...
 $ superplastic: num  2.5 2.5 0 0 0 0 0 0 0 0 ...
 $ coarseagg   : num  1040 1055 932 932 978 ...
 $ fineagg     : num  676 676 594 594 826 ...
 $ age         : int  28 28 270 365 360 90 365 28 28 28 ...
 $ strength    : num  80 61.9 40.3 41 44.3 ...
> normalize <- function(x) {
+     return ((x - min(x)) / (max(x) - min(x)))
+ }
> concrete_norm <- as.data.frame(lapply(concrete, normalize)) 
> concrete_train <- concrete_norm[1 : 700, ]
> concrete_test <- concrete_norm[701 : 1030, ]
```

可以通过`summary()`函数查看标准前后的描述性统计量，可以发现已经成功标准化了
```R
> summary(concrete)
     cement           slag            ash             water        superplastic   
 Min.   :102.0   Min.   :  0.0   Min.   :  0.00   Min.   :121.8   Min.   : 0.000  
 1st Qu.:192.4   1st Qu.:  0.0   1st Qu.:  0.00   1st Qu.:164.9   1st Qu.: 0.000  
 Median :272.9   Median : 22.0   Median :  0.00   Median :185.0   Median : 6.400  
 Mean   :281.2   Mean   : 73.9   Mean   : 54.19   Mean   :181.6   Mean   : 6.205  
 3rd Qu.:350.0   3rd Qu.:142.9   3rd Qu.:118.30   3rd Qu.:192.0   3rd Qu.:10.200  
 Max.   :540.0   Max.   :359.4   Max.   :200.10   Max.   :247.0   Max.   :32.200  
   coarseagg         fineagg           age            strength    
 Min.   : 801.0   Min.   :594.0   Min.   :  1.00   Min.   : 2.33  
 1st Qu.: 932.0   1st Qu.:731.0   1st Qu.:  7.00   1st Qu.:23.71  
 Median : 968.0   Median :779.5   Median : 28.00   Median :34.45  
 Mean   : 972.9   Mean   :773.6   Mean   : 45.66   Mean   :35.82  
 3rd Qu.:1029.4   3rd Qu.:824.0   3rd Qu.: 56.00   3rd Qu.:46.13  
 Max.   :1145.0   Max.   :992.6   Max.   :365.00   Max.   :82.60  

> summary(concrete_norm)
     cement            slag              ash             water       
 Min.   :0.0000   Min.   :0.00000   Min.   :0.0000   Min.   :0.0000  
 1st Qu.:0.2063   1st Qu.:0.00000   1st Qu.:0.0000   1st Qu.:0.3442  
 Median :0.3902   Median :0.06121   Median :0.0000   Median :0.5048  
 Mean   :0.4091   Mean   :0.20561   Mean   :0.2708   Mean   :0.4774  
 3rd Qu.:0.5662   3rd Qu.:0.39775   3rd Qu.:0.5912   3rd Qu.:0.5607  
 Max.   :1.0000   Max.   :1.00000   Max.   :1.0000   Max.   :1.0000  
  superplastic      coarseagg         fineagg            age         
 Min.   :0.0000   Min.   :0.0000   Min.   :0.0000   Min.   :0.00000  
 1st Qu.:0.0000   1st Qu.:0.3808   1st Qu.:0.3436   1st Qu.:0.01648  
 Median :0.1988   Median :0.4855   Median :0.4654   Median :0.07418  
 Mean   :0.1927   Mean   :0.4998   Mean   :0.4505   Mean   :0.12270  
 3rd Qu.:0.3168   3rd Qu.:0.6640   3rd Qu.:0.5770   3rd Qu.:0.15110  
 Max.   :1.0000   Max.   :1.0000   Max.   :1.0000   Max.   :1.00000  
    strength     
 Min.   :0.0000  
 1st Qu.:0.2664  
 Median :0.4001  
 Mean   :0.4172  
 3rd Qu.:0.5457  
 Max.   :1.0000 
```

然后建立神经网络模型，如下
```R
> library(neuralnet)
> concrete_model <- neuralnet(strength ~ ., data = concrete_train)
```
可以通过`plot()`函数查看神经网络拓扑结构，如下
```R
> plot(concrete_model)
```
结构为

![神经网络拓扑结构](https://feily.tech/pic/images/article/neuralnet-img.png)

图中给出了训练的步数Steps为1230步，还给出了误差平方和Error为5.23158，这两个参数对于度量模型性能很重要。图中仍然给出了每个神经元连接处的权重还有偏差项。

接下来进行预测
```R
> model_result <- compute(concrete_model, concrete_test[1 : 8])
> predicted_strength <- model_result$net.result
> cor(predicted_strength, concrete_test$strength)
          [,1]
[1,] 0.7811192
```

通过`cor()`函数获取神经网络预测值与测试集实际值的相关性，结果为0.7811192，可见具有很强的相关性，这意味着我们神经网络模型还是不错的。我们继续提升神经网络性能。

### 5.3提升模型性能
`neuralnet()`函数中还有一个参数没有使用，即`hidden`参数，该参数控制神经网络中隐藏层的神经元数目，刚开始默认为1，我们可以试着调整该参数。
```R
> concrete_model <- neuralnet(strength ~ ., data = concrete_train, hidden = 5)
> plot(concrete_model)
```
我们设置隐藏层神经元数目为5，那么最终的网络拓扑如下

![隐藏层多神经元神经网络拓扑结构](https://feily.tech/pic/images/article/neuralnet-img-hidden.png)

明显复杂了很多。但是可以看出，我们的步数Steps变成了13950，而误差平方和Error变成了1.394552，也就是说神经网络复杂了，步数自然会上升，而误差则会下降，再预测一次，如下
```R
> model_result <- compute(concrete_model, concrete_test[1 : 8])
> predicted_strength <- model_result$net.result
> cor(predicted_strength, concrete_test$strength)
          [,1]
[1,] 0.7889097
```
从相关性来看，我们之前隐藏层为1个神经元的模型还是相当不错的，而隐藏层神经元调整为5之后降低了模型偏差，但是对预测结果的提升并没有改变多少。

需要注意的是，过多的隐藏层是会提升模型的精度，但是也会有过度拟合的风险。

全文完！

###### 推荐阅读
1. http://galaxy.agh.edu.pl/~vlsi/AI/backp_t_en/backprop.html

###### 参考文献
1. 《Artificial Intelligence: A Modern Approach》
2. 《Machine Learning With R》
3. https://towardsdatascience.com/gradient-descent-in-a-nutshell-eaf8c18212f0
4. https://mattmazur.com/2015/03/17/a-step-by-step-backpropagation-example/