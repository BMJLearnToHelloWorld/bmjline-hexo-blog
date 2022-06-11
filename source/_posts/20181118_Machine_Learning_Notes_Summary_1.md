---
title: Machine Learning Notes & Summary _1 Introduction
description: 这本书非常适合我这样的人（目前还没有什么算法能力，对数学，线性代数，概率论及统计学没有什么学习研究）阅读，通过文字和举例子来解释不同的分类算法，语言处理，搜索排序等模型。还有流行，经典的算法模型。
date: 2018-11-18 19:09:29
tags:
  - MachineLearning
  - notes
  - summary
categories:
  - Technology
  - machinelearning
---


## 前言

![PYTHON-NNP](https://storage.bmjline.com/blog/2018/Machine_Learning_Notes_Summary_1/python_nnp.png)

大概从两个多月前开始，从工作中的师父那里看了一本**《Python神经网络编程》**的书，书中讲到了机器是怎么通过像人类神经那样的传输判断方式进行处理的。于是，作者就主要举了一个识别手写输入数字的例子。

其实最令我吃惊地是原来大学中学习的各种理论知识是真的可以运用上的。。。

首先便是**线性代数**，矩阵的概念（矩阵的乘法，转置等）都能够运用在神经网络的输入层和输入层到隐藏层之间的权重关系，隐藏层和隐藏层到输出层之间的权重关系等。只需使用一个矩阵于转置矩阵的相乘便可以同时输出同一层中每一个神经元节点的值。

可能有的人会认为这非常简单，我看完那一章节介绍的时候也觉得不难，但是我的惊讶在于：从来没有想到过矩阵还可以这么运用！

之后，我便开始准备从**机器学习**开始学习。人工智能的领域非常广泛，这里就引用网上的一张图片吧：

![AI-RELATIONSHIP](https://storage.bmjline.com/blog/2018/Machine_Learning_Notes_Summary_1/ai_relationship.jpg)

<br/>

可以看出，**AI**的领域最大，包含的内容也就最广泛。相比AI，进一步便是**Machine Learning机器学习**。再深层的便是**Deep Learning深度学习**等。

如果你问我，学习这些有什么用，我只能回答 **无可奉告！我啊，还需要提高自己的知识水平！**就像在学校里学着数学，我问老师：学好数学只是为了将来买菜的时候不会算错钱被别人坑吗？

之前一段时间，停下了机器学习的学习，搭建了这个博客，想要在以后的学习中，总结自己的笔记，然后通过自己的语言表现出来。这也是希望自己不要因为没有什么非常明确的目标而放弃学习，可能目标会在学习中越来越明显吧。

<br/>

----------

## 机器学习

机器学习分为两大概念：有监督学习，无监督学习。

>**有监督学习**：根据经过标签分类的训练数据，训练出能够分类现实内容的模型。
>
>**无监督学习**：根据没有经过标签分类的数据，让模型根据数据的不同特征自己进行分类。

何谓机器学习，我的理解就是两个字，**基础**。是学好任何人工智能领域的必经之路。机器学习，包括**Linear Regression（线性回归）**，**Logstic Regression（逻辑回归）**，如果用通俗点的话来理解，就是通过不同的算法和训练数据，能够使模型做出较为合理准确的判断和预测。并非像普通的应用判断一样使用 **`if`** **`else`** 语句。

----------

目前，我正在 [Coursera](https://www.coursera.org/) 看**Andrew Ng** 的Machine Learning视频。

>从**Linear Regression（线性回归）**开始，了解**Cost Function（损失函数）**，**Gradient Descent（梯度下降）**。
>
>再到**Logistic Regression（逻辑回归）**中的**Sigmoid（激活函数）**。
>
>然后理解**神经网络**和**SVM（Support Vector Machine）支持向量机**。
>
>最后能够实现一个**OCR（Optical Character Recognition）文字识别模型**。

学完这些，可能机器学习也就算是入门了吧。

<br/>

----------

## CS229

这个也是**Andrew Ng**老师的课程，算是进阶版。更加偏向于算法的讲解。我会在学习完**Coursera**上的**Machine Learning**后开始学习。

<br/>

----------

## 数学之美

![数学之美](https://storage.bmjline.com/blog/2018/Machine_Learning_Notes_Summary_1/Beauty_of_Mathematics.png)

这本书我是利用上下班坐地铁的时间看的，作者吴军是原Google的搜索反作弊领域的开创者。书中有大量的讲到自然语言的处理方式，这同样也是在人工智能中的一个领域，**NLP（Natural Language Processing）自然语言处理**。

这本书非常适合我这样的人（目前还没有什么算法能力，对数学，线性代数，概率论及统计学没有什么学习研究）阅读，通过文字和举例子来解释不同的分类算法，语言处理，搜索排序等模型。还有流行，经典的算法模型。

除了**NLP**，吴军老师还会教给读者各种问题的解决办法和解决思路。

所以，是一本不错的拓展思维和兴趣入门之书。

## 学习之路

我一直告诉自己，搭建完博客，就可以开始总结和继续学习了。。。

以上，也是我需要慢慢去走的路吧。这篇博客也算是开坑了。。。

在这里，我想我不得不感谢我的师父。他不仅是我工作上的师父，也算是我这条学习之路的启蒙老师吧。

<br/>

----------

## 参考

课程：

[Coursera - Machine Learning by Stanford University](https://www.coursera.org/learn/machine-learning)

[CS229 - Machine Learning](https://see.stanford.edu/Course/CS229)

书籍：

《数学之美（第二版）》 吴军 著

《Python神经网络编程》 [英] 塔里克·拉希德（Tariq Rashid） 著

<br/>

----------

感谢你的阅读，如果笔者有什么写错的或者不足之处，烦请读者指出。


![TO BE CONTINUED...](https://storage.bmjline.com/blog/tobecontinuedjojo.png)

<br/>
