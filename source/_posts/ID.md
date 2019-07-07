title: ID3 Decision Tree Algorithm
author: Vaskka
tags:
  - ML
  - 决策树
categories:
  - 机器学习
date: 2019-07-07 17:43:00
---
> 介绍一下ID3 决策树算法

# 变量介绍

对于一个多分类问题我们通常将数据集$D$分为训练集$D_train$和测试集$D_test$。对于数据集$D$上每个数据$d$都有$k$个属性${a_1,a_2,...,a_k}$，每个属性都有一个明确的输出$output$，全部分类数的个数计做$y$。

# 生成决策树

想要生成决策树，我们必须知道每个节点都是怎样量化的。这里引入**信息增益**来对每个节点的位置进行评估。信息增益表示得知属性$a$的信息而使得样本集合不确定度减少的程度[^1]。其公式如下：

$Gain(D,a)=Ent(D)-\sum^V_{v=1} \frac{|D^v|}{|D|}Ent(D^v)$

其中，Ent(D)表示对于数据集D的**信息熵**，信息熵表示度量样本集合不确定度的最常用的指标。其公式如下：

$Ent(D)=-\sum^{|y|}_{k=1}p_klog_2p_k$

在对每个属性求得当前数据集上这个属性的信息增益的值后，我们便可以将此数值最大的属性作为当前研究的节点属性。接着再将数据集变换为当前节点下的数据集继续计算信息增益以确定节点属性。

# 使用决策树

当决策树构建好后将每个测试数据带入决策树即可。

# 示例

训练集:

| attr1 | attr2 | output |
| ----- | ----- | ------ |
| Married | Female | High | 
| Single | Female | Low |
| Devorced | Female | High |
| Single | Male | Low |
| Single | Female | Low|
| Married | Male | Low |
| Divorced | Male | High |
| Single | Male | Low |

测试集:

| attr1 | attr2 | output |
| ----- | ----- | ------ |
| Married | Male | High | 
| Single | Male | Low |
| Devorced | Female | High |

## 构建决策树示例

对于第一个节点的属性选取，我们需要对两个属性分别计算信息增益。

对于属性attr1，此时的$D$为全体训练集，有3种取值，即 Married、Single、Divorced

先计算当前数据集$D$（即全体训练集）的信息熵$Ent(D)$，带入公式

$Ent(D)=-\sum^{|y|}_{k=1}p_klog_2p_k$

$=-\(\frac{3}{8}log_2\frac{3}{8} + \frac{5}{8}log_2\frac{5}{8})$

$=0.95443$

再分别计算每个attr1的取值的条件熵：

对于Married来说符合的有1号和6号，其中High占$\frac{1}{2}$，Low占$\frac{1}{2}$

则

$Ent(D_{Married})=-\(\frac{1}{2}log_2\frac{1}{2}+\frac{1}{2}log_2\frac{1}{2})=1$

$\frac {D^v} {D}=\frac {2} {8}$

对于Single来说符合的有1、4、5、8号，其中High占0，Low占1

则

$Ent(D_{Single})=-\(lim_{m \to 0}m \times log_{2}m+1 \times  log_{2}1)=0$

$\frac {D^v} {D}=\frac {4} {8}$

对于Disvorced来说符合的有3、7号，其中High占1，Low占0

则

$Ent(D_{Disvorced})=-\(1 \times log_{2}1 + lim_{m \to 0}m \times log_{2}m)=0$

$\frac {D^v} {D}=\frac {2} {8}$

所以，

$Gain(D,attr1)=Ent(D) - \(\frac{2}{8} \times 1 + \frac{4}{8} \times 0 + \frac{2}{8} \times 0 \)$

$=Ent(D) - 0.25$

$=0.95443 - 0.25$

$=0.70443$

对于属性attr2，此时的$D$为全体训练集，有2种取值，即 Female、Male

同样方法计算，这里简略。

Female集合为$\{1,2,3,5\}$，其中High占$\frac {1} {2}$，Low占$\frac {1} {2}$

Male集合为$\{4,6,7,8\}$，其中High占$\frac {1} {4}$，Low占$\frac {3} {4}$

$Ent(D_{Female}) = 1$

$Ent(D_{Male}) = 0.81228$

最后的信息增益结果为0.04879

由于attr1的信息增益大于attr2，所以我们选择attr1作为第一个选择。

在根据第一个节点的三个属性Married、Single、Disvorced分别重新生成数据集D并重复上述步骤，最后构建的决策树如下：

![决策树](https://raw.githubusercontent.com/Vaskka/GitLearn/master/ID3-DT/id3-dt-real.jpg)

# 参考

[^1] web 知乎 深入浅出理解决策树算法（二）[https://zhuanlan.zhihu.com/p/26760551](https://zhuanlan.zhihu.com/p/26760551) 2019-7-7