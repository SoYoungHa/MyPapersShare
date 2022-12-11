# Universal Adversarial Perturbations

# 通用扰动UAP

CVPR 2017

## 问题

之前提出的算法都是根据图像“私人定制”的扰动，这种就需要每次生成扰动时重复计算，那么是否可以生成一种与图像无关的扰动，一个扰动就可以迁移到所有图像上面。

## 优缺点

优点：

1.提出的通用扰动是一种无关图像的，不需要去进行重复的计算，可以做到一劳永逸。

缺点：

1.一般的对抗样本的fool ratio可以达到99，UAP的效果还是要差上一点。

2.UAP的迁移性由于扰动是与图像无关的而且这种扰动其实是根据模型的特征生成的所以在其他模型上的泛化能力并不是很好。

## Abstract

找到了与图像无关(**image-agnostic**)的扰动，提出了一种计算普遍扰动的算法。

普遍扰动的泛化能力很好

攻击者可能利用这种安全漏洞来破坏大多数自然图像的分类器。 

## Conclusions

提出一种迭代算法来生成通用扰动

通用扰动在不同的分类模型里面泛化能力很好，导致双重通用扰动（图片、网络不可知）  

**（特征空间上的解释）**进一步解释了这种扰动与决策边界不同区域之间相关性的存在。这提供了对深度神经网络决策边界几何的见解，并有助于更好地理解此类系统。

## 一、Introduction

<img src="attachments/JS9FPV38.png" alt="image" style="zoom:80%;" />

-   提出算法：该算法寻找一组训练点的通用扰动，然后通过聚合原子的扰动向量继续进行，该原子扰动向量将连续的数据点发送到分类器的决策边界
-   普遍扰动有着显著的泛化特性。
-   通过检查决策边界不同部分之间的几何相关性，来解释和分析深度神经网络对普遍扰动的高度脆弱性

之前的对抗性扰动的基本属性是它们对数据点的内在依赖性：扰动是针对每个数据点专门设计的。

“针对新数据点的对抗性扰动的计算需要从头解决数据相关的优化问题，这需要使用分类模型的全部知识（权重、梯度）。这与本文所考虑的通用扰动不同，因为本文寻求一个使**大多数自然图像**上的网络fool的扰动向量，而且还不需要重新解决优化问题/梯度计算。“

## 二、Universal perturbations 通用扰动

μ：图像的数据分布

k^(x):分类结果，输出标签

目的：![image](attachments/DL3FY43Z.png)

s.t. 约束条件：

 ![image](attachments/VQMJXJM2.png)

<span style="background-color: rgb(255, 255, 255)">ξ</span> 代表了扰动向量的量级，

 <span style="background-color: rgb(255, 255, 255)">1-</span><span style="background-color: rgb(255, 255, 255)">δ</span> <span style="background-color: rgb(255, 255, 255)">定量了希望欺骗的比例</span>

***<span style="background-color: rgb(255, 255, 255)">需要找到一个对抗扰动v，这个扰动可以加到所有的样本点上，而且会以1−δ的概率让对抗样本被分类错误。</span>***

**Algorithm:**

$$X=\left\{x1,x2,…,xm\right\}$$  from $μ$,即从μ中采样的图片集合

![image](attachments/X6SNTWIB.png)

**每次迭代时，都计算逃离当前分类域的扰动**

在每次迭代中，计算将当前扰点$x_i+v$发送到分类器决策边界的最小扰动 <span style="background-color: rgb(255, 255, 255)">∆vi</span> <span style="background-color: rgb(255, 255, 255)">，并将其汇总到通用扰动的当前实例。</span>

如果当前的通用扰动v不能欺骗数据点 ，可以通过解决以下优化问题来寻求具有最小范数的额外扰动∆vi来欺骗数据点 xi：优化公式

求Δvi:

![image](attachments/ABQTU4XR.png)

更新v扰动：再把v限制在范数下以ε为半径的球上。

![image](attachments/QF7KB4Q3.png)

![image](attachments/7XCUT4PQ.png)

**Alogrithm1**的目的不是找到最小的通用扰动，而是找到一种范数足够小的扰动

<img src="attachments/HDGLIGQG.png" alt="image" style="zoom:80%;" />

## 三、Universal perturbations for deep nets 深度网络的通用扰动

**实验部分**

![image](attachments/56FYT568.png)

可见fool的效果都还不错

![image](attachments/MM8PZVZG.png)

可见人眼根本看不出来区别，但网络识别的结果天差地别.

![image](attachments/FEEU2CMY.png)

​																				**不同网络的扰动可视化**

不同大小的X在验证集上的fool ratio(ImageNet一共1000class):

![image](attachments/2ZDUXMK9.png)

跨模型通用性(Cross-model universality)：

![image](attachments/2PN2CTQ2.png)

## 四、Explaining the vulnerability to universal perturbations

这篇分析深度神经网络对扰动的脆弱性。

先做了一组扰动对比实验来体现通用扰动的特点：

![image](attachments/5BEPDP43.png)

在norm很小时通用扰动就已经达到了很高的fool ratio

提取n个样本处的这种法向量，并对它们进行单位化，形成正规矩阵N:</span>

![image](attachments/I68XPLRZ.png)

对N进行奇异值分解(SVG):

![image](attachments/RBQUXQVZ.png)

根据得到的对比结果，作者认为这表明在深度网络的决策边界中存在着较大的相关性与冗余性。更精准地来说，作者认为存在一个低维子空间S，在这个S中存在着大量包裹着自然图像的决策边界的法向量



作者假设存在使大多数自然图像蒙混的通用扰动的部分原因是由于存在这样一个低维子空间，该子空间捕获了决策边界不同区域之间的相关性

![image](attachments/BIQILJ7W.png)



## 引用格式

Moosavi-Dezfooli S M, Fawzi A, Fawzi O, et al. Universal adversarial perturbations[C]//Proceedings of the IEEE conference on computer vision and pattern recognition. 2017: 1765-1773.