---
title: "Decision tree learning algorithm"
date: 2019-03-23T15:55:25+08:00
categories: ["Machine Learning"]
tags: ["Decision tree"]
draft: false
---

## 简介

决策树学习算法以树形结构建立模型，类似于流程图，该模型本身包含一系列逻辑决策。几个概念如下

+ 决策节点：根据某一特征(属性)作出决定；
+ 决策分支：从决策节点引出的分支表示做出的选择；
+ 终端节点(叶节点)：决策树由叶节点终止，叶节点表示遵循决策组合的结果。

其运行过程为：数据的分类从根节点开始(根节点是第一个决策节点)，根据特征值遍历树上的各个决策节点。数据采用的是一个漏斗形的路径，它将每一条记录汇集到一个节点，在叶节点为该记录分配一个预测类。

## 理论原理

决策树是决策函数的可视化描述，决策函数是由训练集的特征空间训练出来的，不同属性的不同程度上的不同组合会形成不同的候选函数(或决策树)，拟合优度因函数(或者不同特征的不同程度上的不同组合)而异，有的明显性能出色，有的明显性能低劣，我们不可能将所有的候选函数全部生成然后一一测试，因为多数情况下随着特征的增加，候选函数呈指数级增长。我们的目的就是找到最优的候选函数，何为最优？浅显易懂的来说就是

<u>通过较少的测试达到正确分类，即树中所有路径都较短，整棵树较浅。</u>

要做到路径较短，那么也就意味着每一次的分裂都要是高效的，即每次分裂都可以提高子集的同质性或者纯度，这样减少了不必要的分割，整棵树必然会变浅。那么专业的来说就是

<u>每一次决策节点的分裂，得到的子集中的预测类的水平的比例与父集合中的预测类的水平的比例最大程度上更低。</u>

## 数学定义

决策树算法对以上最大程度上的定义是通过信息增益实现的。要解释信息增益，先要解释熵与条件熵。

熵：熵是随机变量的不确定性度量，信息的获取对应于熵的减少。熵越大说明信息量越大该事件内部越不同质，不确定性越大。熵越小说明信息量越小该事件内部越同质，不确定性越小。举个例子

+ 明天早上太阳必然升起。这个事件，即随机变量或该事件没有不确定性，是完全同质的(因为只有这一种可能，还是必然的)；
+ 抛一枚硬币，出现正反面朝上的概率皆为0.5。这个事件的熵为1，即随机变量或该事件具有不确定性，是不同质的(因为样本空间存在两种可能，而不是一种)。

所以，我们可以通过事件的熵来定义该事件内部(样本空间)的同质性或混乱性。熵越大，说明该事件样本空间越不同质越杂乱，实验结果的不确定性更大；熵越小，说明事件样本空间越同质越不杂乱，实验的不确定性更小。其计算公式为

\begin{equation}H(X)=-\sum_{i=1}^{n} p\left(x_{i}\right) \log p\left(x_{i}\right)\end{equation}

条件熵：条件熵表示在已知随机变量X的条件下，随机变量Y的不确定性。将其定义为给定X的条件下Y的条件概率分布的熵对X的数学期望。其本质仍然是熵。其计算公式为

\begin{equation}\mathrm{H}(Y \| X) \equiv \sum_{x \in \mathcal{X}} p(x) \mathrm{H}(Y \| X=x)\end{equation}

在决策树算法当中，使用熵值来计算由每一个可能特征的分割所引起的同质性(均匀性)变化，该计算称为信息增益。对于特征F，信息增益的计算方法是分割前的数据分区(S_1)的熵值减去由分割产生的数据分区(S_2)的熵值。其中S_1就是分裂前集合预测类水平的熵值，但是集合被通过该特征划分为了若干个子集，怎样计算它们的熵值呢？

由于一次分割后，数据被划分到了多个分区中，因此计算S_2需要考虑所有分区的熵值总和，这并不意味着简单加总，而是求他们的期望，也就是在分割集合的条件下子集的熵对该次分割的数学期望，即S_2是分割后产生的若干个子集的预测类水平的熵值的期望(即从一个分割得到的总熵就是根据案例落入分区的比例加权的n个分区的熵值的总和)，这样就可以通过条件熵的方式来度量由分割产生的效果，即总熵减少到了多少(因为分割为了多个子集，所以总熵只能通过期望来计算)。这是一种度量方式，但是不够直观，最直观的度量方式就是计算信息增益，如下

\begin{equation}I G(T, a)=\mathrm{H}(T)-\mathrm{H}(T \| a)\end{equation}

由于子集有且只有一个父集(可能会共享同一个父集，但是绝不会同时拥有两个或两个以上的直接父集)，那么也就意味着分割前的熵是相同的，那么分割后的子集的条件熵越小就越说明当前训练集在该选择该特征进行划分后效果最好，因为分割后的总熵(期望熵)越小，即内部越同质越均匀纯度越高。为了便于与其余的特征进行比较，通过计算信息增益，那么也就意味着对同一训练集根据不同特征进行分裂，哪个特征的信息增益越大那么划分的结果越好越同质越均匀越纯。

到此为止，可以看到，决策树算法是通过信息增益来确定合理的或最好的分割点的，信息增益在此程度上意味着一次分割获得的信息多少，信息增益越大，那么得到的信息量越多，那么分割的越成功，分割的次数越少，最终树中的所有路径越短，整棵树越浅。

简言之：

熵越小越说明当前训练集(或子集)内部纯度越高越同质，这是我们的最终目标。刚开始，熵肯定是很大的，我们的目的就是通过根据合适的特征的分裂降低子集的熵值以实现分类预测，而合适的特征的选择是通过信息增益来衡量的。

## 过度拟合与剪枝

过度拟合的专业定义显得过于繁琐和难懂，不妨使用《Artificial Intelligence: A Modern Approach》一书中关于过度拟合的形象化描述，如下

Consider the problem of trying to predict whether the roll of a die will come up as 6 or not Suppose that experiments are carried out with various dice and that the attributes describing each training example include the color of the die, its weight, the time when the roll was done, and whether the experimenters had their fingers crossed. If the dice are fair, the right thing to learn is a tree with a single node that says "no," But the DECISION-TREE-LEARNING algorithm will seize on any pattern it can find in the input. If it turns out that there are 2 rolls of a 7-gram blue die with fingers crossed and they both come out 6, then the algorithm may construct a path that predicts 6 in that case. This problem is called overfitting. 

 对决策树而言，一种被称为决策树剪枝的技术可以减轻过度拟合。包括两种，分别是预剪枝和后剪枝。
 
所谓预剪枝就是一旦决策树达到了一定数量的决策，或者说决策节点仅含有少量的案例，那么就停止树的生长，因为这样的继续生长是毫无意义的，决策节点只含有少量的案例是不具备一般性意义的。或者说当不存在可用于分裂的好属性时，就停止生长，这种情况就是允许该子集存在一定的不同质以防止过度拟合，因为再此基础上继续生长仍然不具备一般性意义，还是因为案例太少，不具备观测与分析价值。

预剪枝的好处就是及时停止树的生长，而不是等树生长之后再修剪；简单而直接，适用于解决大规模问题，因为数据量很大的情况下生成被后剪的不必要决策节点是毫无意义的。但是要精确的实现决策树的修剪并不容易，如果修剪的过早那么预测分类不同质的可能性更大，影响决策树的优度；而如果修剪的过晚，又会造成一定程度的过度拟合。

预剪枝的几种方法如下

1. 定义一个高度，当决策树达到该高度时就停止决策树的生长，这是最简单的一种办法；
2. 达到某叶节点的案例(或记录)具有相同的特征向量，即使这些案例不属于同一类，那么也可以停止决策树的生长，因为既然当前节点的案例集具备相同的特征向量，那么就有很大的可能性认为其与当前预测类同质，这个度还是要把握好，如果停止的过早仍然会使得叶节点预测类不同质可能性变大；
3. 定义一个阈值，当达到某个节点的案例个数小于该阈值时就可以停止该决策树的生长，原因仍然是少量样本不具备观测与分析价值，对其分析(选择合适的分割点生成决策节点)容易造成过度拟合；
4. 定义一个阈值，通过计算每次扩张对系统性能的增益，并比较增益值与该阈值的大小来决定是否停止决策树的生长。

所谓后剪枝就是先构造完整的决策树，允许树过度拟合训练数据，然后对那些置信度不够的节点子树用叶节点来代替，该叶节点的预测类型为该节点子树中最频繁的类型。几种后剪枝方法如下

1. Reduced-Error Pruning(REP, 错误率降低剪枝)
2. Pessimistic Error Pruning(PEP, 悲观错误剪枝)
3. Cost-Complexity Pruning(CCP, 代价复杂度剪枝)

其中REP剪枝的原理与操作如下

该方法将可用数据集合分为两个样例集合，一个训练集用于训练决策树，一个验证集用来评估修剪这个决策树后得到的预测分类的结果的精度以决定是否修剪相应子树。其操作方法如下

1. 删除以此节点为根的子树；
2. 使其成为叶子节点；
3. 赋予该叶子节点关联的训练数据的最常见分类，即将原子树最最频繁的类型赋给该叶子节点；
4. 当修建后的树对于验证集合的性能不会比原来的树差时，才真正删除该子树或者用叶节点替换该子树。

由于使用独立的验证集，与原始决策树相比，修建后的决策树可能偏向于过度修剪。这是因为一些不会在验证集中出现的稀少的但是在训练集中出现的案例会被修剪，但是这并不意味着该案例在其后的测试集中不会在出现。如果训练集较小，不建议使用REP剪枝算法。

## R语言中C5.0决策树算法

C5.0算法是C4.5的改进版，适用于大多数类型的问题。与其它机器学习模型相比，该算法建立的决策树一般都表现的与它们的模型几乎一模一样，且更容易理解与部署。该算法的优点之一就是它可以自动修剪，其总体策略就是后剪枝，它先生成一个过度拟合的决策树，然后删除对分类误差影响不大的节点和分枝。在某些情况下，整个分枝会被进一步向上移动或者被一些简单的决策所取代，这两个移植分枝的过程被称为子树提升和子树替换。C5.0算法的优点之一就是它很容易调整训练方案以提升决策树性能。

该算法在C50添加包当中，其函数原型为

先创建分类器

```r
m <- C5.0(train, class, trials = 1, costs = NULL)
```

+ train : 一个包含训练数据的数据框;
+ class : 包含训练数据每一行的分类的一个因子向量;
+ trials: 可选数值，用于控制自助法循环次数(默认为1)，可用于提升模型性能;
+ costs : 可选矩阵，用于给出与各种类型错误相对应的成本，可用于提升模型性能。

该函数返回一个C5.0模型对象，该对象能够用于预测。

进行预测

```r
p <- predict(m, test, type = "class")
```

+ m : 由C5.0函数得到的训练模型;
+ test : 一个包含测试数据的数据框，该数据框和用来创建分类器的训练数据有同样的特征;
+ type : 取值为class或者prob, 标识预测是最有可能的类别值或者原始的预测概率;

该函数返回一个向量，对应test测试集的每一个案例的预测分类结果或概率值(由type决定)。

## R语言C5.0算法应用示例

以《Machine Learning With R》一书中的根据银行贷款数据来建立模型来预测贷款是否违约为例来说明C5.0算法

```r
> getwd()
[1] "C:/Users/Administrator/Documents"
> setwd("C:\\Users\\Administrator\\Desktop\\docs\\Machine-Learning-with-R-datasets-master")
> credit <- read.csv("credit.csv")
> str(credit$default)
 int [1:1000] 1 2 1 1 2 1 1 1 1 2 ...
> table(credit$default)

  1   2 
700 300 
> credit$default <- factor(credit$default, levels = c(1, 2), labels = c("no", "yes"))
> table(credit$default)

 no yes 
700 300 
> set.seed(12345)
> credit_rand <- credit[order(runif(1000)), ]
> credit_train <- credit_rand[1 : 900, ]
> credit_test <- credit_rand[901 : 1000, ]
> prop.table(table(credit_train$default))

       no       yes 
0.7022222 0.2977778 
> prop.table(table(credit_test$default))

  no  yes 
0.68 0.32 
```

上述将数据框credit按照均匀分布的1000个随机数重组，最终为credit_rand，然后分别分割为训练集credit_train和测试集credit_test，通过校验其分布发现基本上与原数据框分布无异。
然后应用C5.0算法创建分类器

```r
> library(C50)
Warning message:
程辑包‘C50’是用R版本3.5.3 来建造的 
> credit_model <- C5.0(credit_train[-17], credit_train$default)
```

我们可以通过输出分类器名称来查看关于决策树的一些基本数据

```r
> credit_model

Call:
C5.0.default(x = credit_train[-17], y = credit_train$default)

Classification Tree
Number of samples: 900 
Number of predictors: 20 

Tree size: 57 

Non-standard options: attempt to group attributes
```

前面的文字反映了关于该决策树的一些基本情况，包括生成决策树的函数调用、特征数(predictors)、用于决策树增长的案例(Samples)。同时列出了树的大小是57，表明该决策树包含57个决策。
如果要查看决策树，可以通过`summary()`函数来实现，部分输出为

```r
> summary(credit_model)

Call:
C5.0.default(x = credit_train[-17], y = credit_train$default)


C5.0 [Release 2.07 GPL Edition]  	Sat Mar 23 20:33:44 2019
-------------------------------

Class specified by attribute `outcome'

Read 900 cases (21 attributes) from undefined.data

Decision tree:

checking_balance = unknown: no (358/44)
checking_balance in {< 0 DM,> 200 DM,1 - 200 DM}:
:...foreign_worker = no:
    :...installment_plan in {none,stores}: no (17/1)
    :   installment_plan = bank:
    :   :...residence_history <= 3: yes (2)
    :       residence_history > 3: no (2)
    foreign_worker = yes:


Evaluation on training data (900 cases):

	    Decision Tree   
	  ----------------  
	  Size      Errors  

	    57  127(14.1%)   <<


	   (a)   (b)    <-classified as
	  ----  ----
	   590    42    (a): class no
	    85   183    (b): class yes


	Attribute usage:

	100.00%	checking_balance
	 60.22%	foreign_worker
	 57.89%	credit_history
	 51.11%	months_loan_duration
	 42.67%	savings_balance
	 30.44%	other_debtors
	 17.78%	job
	 15.56%	installment_plan
	 14.89%	purpose
	 12.89%	employment_length
	 10.22%	amount
	  6.78%	residence_history
	  5.78%	housing
	  3.89%	dependents
	  3.56%	installment_rate
	  3.44%	personal_status
	  2.78%	age
	  1.56%	property
	  1.33%	existing_credits


Time: 0.0 secs
```

该输出包含一个混淆矩阵，是一个交叉列表，表示模型对训练数据错误分类的记录数。可以看出共有42个原本是no的案例被预测为yes，共有85个原本是yes的案例被预测为no。即该决策树对训练集中127个案例错误的分类了，占比为14.1%

现在有了分类器，我们可以用于预测，如下

```r
> credit_prediction <- predict(credit_model, credit_test)
```

预测的结果向量为

```r
> credit_prediction
  [1] no  yes no  no  no  yes no  no  yes yes no  no  no  yes yes yes no  no  yes
 [20] no  yes no  yes no  yes no  yes no  no  no  yes yes no  yes no  no  no  no 
 [39] yes yes no  no  no  yes yes yes yes no  yes no  no  no  yes no  no  no  no 
 [58] yes no  no  no  yes no  yes no  no  yes no  no  no  no  no  no  yes yes no 
 [77] no  yes no  no  no  no  no  no  no  no  no  no  yes yes yes no  no  no  no 
 [96] yes no  yes no  no 
Levels: no yes
```

可以通过如下方法查看测试集的预测成功程度，以评估模型性能

```r
> table(credit_prediction == credit_test$default)

FALSE  TRUE 
   25    75 

> prop.table(table(credit_prediction == credit_test$default))

FALSE  TRUE 
 0.25  0.75 
```
可见，正确率为75%。

如何提高模型性能呢？

## 提高C5.0算法的分类器性能

之前在创建分类器的时候有两个参数没有使用，分别是trials和costs参数。

对C5.0算法性能的提升可以通过**自适应增强(adaptive boosting)算法**，具体做法是在决策树的构建过程中，这些决策树通过投票表决的方法为每个案例选择最佳的分类。C5.0算法通过trials参数来引入该算法(boosting算法), 该参数的值表示在模型增强团队中使用到的独立决策树的数量，参数trials设置了一个上限，如果该算法识别出额外的实验似乎并没有提高模型的准确性，那么它将停止添加决策树。一般设置为10

```r
> credit_model <- C5.0(credit_train[-17], credit_train$default, trials = 10)
> credit_prediction <- predict(credit_model, credit_test)
> prop.table(table(credit_prediction == credit_test$default))

FALSE  TRUE 
 0.21  0.79 
```

可以看到明显提升了模型精度。

还有一个办法是通过costs参数，该参数是一个代价矩阵，用于指定每种错误相对于其它任何错误有多少倍的严重性。假如评估模型精度的双向交叉表为

```r
> library(gmodels)
Warning message:
程辑包‘gmodels’是用R版本3.5.3 来建造的 
> CrossTable(credit_test$default, credit_prediction, prop.chisq = FALSE, prop.c = FALSE, prop.r = FALSE, dnn = c("actual default", "predicted default"))

 
   Cell Contents
|-------------------------|
|                       N |
|         N / Table Total |
|-------------------------|

 
Total Observations in Table:  100 

 
               | predicted default 
actual default |        no |       yes | Row Total | 
---------------|-----------|-----------|-----------|
            no |        63 |         5 |        68 | 
               |     0.630 |     0.050 |           | 
---------------|-----------|-----------|-----------|
           yes |        16 |        16 |        32 | 
               |     0.160 |     0.160 |           | 
---------------|-----------|-----------|-----------|
  Column Total |        79 |        21 |       100 | 
---------------|-----------|-----------|-----------|

```

那么我们定义如下代价矩阵

```r
> error_cost <- matrix(c(0, 4, 1, 0), nrow = 2)
> error_cost
     [,1] [,2]
[1,]    0    1
[2,]    4    0
```

该代价矩阵用来说明双向交叉表中落入对应[2, 1]的元素的错误更加严重，那么分类器就会极力避免这种情况发生，再创建一个分类器试试

```r
> credit_model <- C5.0(credit_train[-17], credit_train$default, costs = error_cost)
Warning message:
no dimnames were given for the cost matrix; the factor levels will be used 
> credit_prediction <- predict(credit_model, credit_test)
> prop.table(table(credit_prediction == credit_test$default))

FALSE  TRUE 
 0.32  0.68 
> CrossTable(credit_test$default, credit_prediction, prop.chisq = FALSE, prop.c = FALSE, prop.r = FALSE, dnn = c("actual default", "predicted default"))

 
   Cell Contents
|-------------------------|
|                       N |
|         N / Table Total |
|-------------------------|

 
Total Observations in Table:  100 

 
                    | credit_prediction 
credit_test$default |        no | Row Total | 
--------------------|-----------|-----------|
                 no |        68 |        68 | 
                    |     0.680 |           | 
--------------------|-----------|-----------|
                yes |        32 |        32 | 
                    |     0.320 |           | 
--------------------|-----------|-----------|
       Column Total |       100 |       100 | 
--------------------|-----------|-----------|
```
可见，与之前的模型相比，该模型的精度更低。但是这不失为一种调整模型性能的方法，需要根据实际情况来调整。