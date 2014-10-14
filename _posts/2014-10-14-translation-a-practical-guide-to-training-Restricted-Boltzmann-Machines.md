---
title: "一篇关于训练限制波兹曼机(RBM)的实践指导"
layout: post
comments: yes
---

* 原文:[A Practical Guide to Training Restricted Boltzmann Machines](http://www.cs.toronto.edu/~hinton/absps/guideTR.pdf)
* 原作者个人网页:[http://www.cs.toronto.edu/~hinton/](http://www.cs.toronto.edu/~hinton/)

#####目录
1. 简介
2. 限制波兹曼机和Contrastive Divergence综述
3. 使用Contrastive Divergence时,如何收集统计信息
    * 更新隐层状态
    * 更新视觉层状态
    * 收集用于学习的统计信息
    * 一种获取学习信号用于CD1的诀窍
4. mini-batch的大小
    * 将训练集划分为mini-batch的诀窍
5. 监控学习的过程
    * 使用重构误差的诀窍
6. 监控过拟合
    * 监控过拟合的诀窍
7. 学习率
    * 设置权值和偏置学习率的诀窍
8. 权置和偏置初始化
    + 设置权执和偏置初始值的诀窍
9. 动量
    - 使用动量的诀窍
10. 权值腐蚀
    * 使用权值腐蚀的诀窍
11. 刺激稀疏的隐层响应
    * 稀疏性的诀窍
12. 隐层结点个数
    * 选择隐层结点个数的诀窍
13. 结点的不同类型
    * Softmax和多项式的结点
    * 高斯可视的结点
    * 高斯可视的和隐层的结点
    * 二项式结点
    * 改进的线性结点
14. contrastive divergence的变化
15. 显示学习中的变化过程
16. RBM用于分类
    * 计算一个可视层响量的能量
17. 处理缺省值

#####1. 简介

