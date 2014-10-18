---
title: "Contrastive Divergence"
layout: post
comments: yes
---

[Contrastive Divergence](http://www.robots.ox.ac.uk/~ojw/files/NotesOnCD.pdf)

[good blog about RBM](http://blog.csdn.net/mytestmy/article/details/9150213)

这个笔记描述了对比分歧(CD)算法,这是个逼近最大似然函数的学习算法,由[Hinton](http://www.cs.toronto.edu/~hinton/)提出.

####CD是什么,我们为什么需要它?

试想我们建立一个点数据的概率模型,函数模型为f(x; θ), θ表示模型参数向量.对于x的概率,p(x;θ)在所有x的累加和为1, 表示为: 
<img src="http://www.forkosh.com/mathtex.cgi? \Large p(x; \Theta)=\frac{1}{Z(\Theta)}f(x; \Theta)">
其中Z(θ)是配分函数,定义为:
<img src="http://www.forkosh.com/mathtex.cgi? \Large Z(\Theta)=\int\limits f(x; \Theta)dx">
我们以最大化训练数据概率的方式对模型的参数进行学习.训练数据表示为:
<img src="http://www.forkosh.com/mathtex.cgi? \Large \mathbf{X}=x_{1,...,K}">
概率表示为:
<img src="http://www.forkosh.com/mathtex.cgi? \Large p(\mathbf{X};\Theta)=\prod_{k=1}^K\frac{1}{Z(\Theta)}f(x;\Theta)">
或等价于,对p(X;θ)取对数的负值再最小化,表示为E(X;θ),即能量公式:
<img src="http://www.forkosh.com/mathtex.cgi? \Large E(\mathbf{X};\Theta)=logZ(\Theta) - \frac{1}{K}\sum_{k=1}{K}logf(x_k;\Theta)">








