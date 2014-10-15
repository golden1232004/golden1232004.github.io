---
title: "Restriced Boltzmann Machines(RBM)解释和代码分析"
layout: post
comments: yes
tags: programming
category: languages
---

本文的RBM代码来自于[DeepLearnToolbox](https://github.com/rasmusbergpalm/DeepLearnToolbox), 使用Matlab语言

输入参数的结构如下:

* rbm = struct('W', W, 'vW', vW, 'b', vb, 'c',vc);
* x = rand(N, D); %N:样本数量; D:样本的维度
* opts = struct('numepochs', value1, 'batchsize', value2, 'momentum', value3, 'alpha', value4);

<pre><code>function rbm = rbmtrain(rbm, x, opts)
    assert(isfloat(x), 'x must be a float');
    assert(all(x(:)>=0) && all(x(:)<=1), 'all data in x must be in [0:1]');
    m = size(x, 1); %样本数量
    numbatches = m / opts.batchsize;
    
    assert(rem(numbatches, 1) == 0, 'numbatches not integer');

    for i = 1 : opts.numepochs
        kk = randperm(m);  %生成m个整数随机数
        err = 0;
        for l = 1 : numbatches
            batch = x(kk((l - 1) * opts.batchsize + 1 : l * opts.batchsize), :); 
            v1 = batch;
            h1 = sigmrnd(repmat(rbm.c', opts.batchsize, 1) + v1 * rbm.W');
            v2 = sigmrnd(repmat(rbm.b', opts.batchsize, 1) + h1 * rbm.W);
            h2 = sigm(repmat(rbm.c', opts.batchsize, 1) + v2 * rbm.W');

            c1 = h1' * v1;
            c2 = h2' * v2;

            rbm.vW = rbm.momentum * rbm.vW + rbm.alpha * (c1 - c2)     / opts.batchsize;
            rbm.vb = rbm.momentum * rbm.vb + rbm.alpha * sum(v1 - v2)' / opts.batchsize;
            rbm.vc = rbm.momentum * rbm.vc + rbm.alpha * sum(h1 - h2)' / opts.batchsize;

            rbm.W = rbm.W + rbm.vW;
            rbm.b = rbm.b + rbm.vb;
            rbm.c = rbm.c + rbm.vc;

            err = err + sum(sum((v1 - v2) .^ 2)) / opts.batchsize;
        end
        
        disp(['epoch ' num2str(i) '/' num2str(opts.numepochs)  '. Average reconstruction error is: ' num2str(err / numbatches)]);
        
    end
end
</code></pre>

###RBM理解
> RBM[参考教程](http://deeplearning.net/tutorial/rbm.html)

> 本人认为RBM难以理解的地方在于理论推倒. 比如说能量函数的定义是这样的,而不是那样的.关于能量函数难倒了一大片人, 而这就是大牛厉害的地方. 观察RBM能量函数的形式发现其与Hopfield神经网络的Lyapunov函数类似. 在<<神经网络设计>>一书中18.2.2节中有一句话解释了这个函数的来源"Hopfield选择这个特殊的Lyapunov的函数也是他的主要贡献之一".

关于RBM的介绍和简单分析参考[维基百科](http://zh.wikipedia.org/wiki/%E5%8F%97%E9%99%90%E7%8E%BB%E5%B0%94%E5%85%B9%E6%9B%BC%E6%9C%BA).部分内容如下:

受限玻尔兹曼机（英语：restricted Boltzmann machine, RBM）是一种可通过输入数据集学习概率分布的随机生成神经网络。RBM最初由发明者保罗·斯模棱斯基（Paul Smolensky）于1986年命名为簧风琴（Harmonium）[1]，但直到杰弗里·辛顿及其合作者在2000年代中叶发明快速学习算法后，受限玻尔兹曼机才变得知名。受限玻尔兹曼机在降维[2] 、分类[3]、协同过滤、特征学习[4]和主题建模[5]中得到了应用。根据任务的不同，受限玻尔兹曼机可以使用监督学习或无监督学习的方法进行训练。

正如名字所提示的那样，受限玻尔兹曼机是一种玻尔兹曼机的变体，但限定模型必须为二分图。模型中包含对应输入参数的输入（可见）单元和对应训练结果的隐单元，图中的每条边必须连接一个可见单元和一个隐单元。（与此相对，“无限制”玻尔兹曼机包含隐单元间的边，使之成为递归神经网络。）这一限定使得相比一般玻尔兹曼机更高效的训练算法成为可能，特别是基于梯度的对比分歧（contrastive divergence）算法[6]。

受限玻尔兹曼机也可被用于深度学习网络。具体地，深度信念网络可使用多个RBM堆叠而成，并可使用梯度下降法和反向传播算法进行调优[7]。

RBM的网络结构如下图:
![](/res/rbm.png)

> 这个网络结构来自于网络, 网上大部分的RBM网络结构都类似上图.单看这个网络结构并不能完全RBM的网络含义,详细解释参考[8]中的第二部分(2. THE BOLTZMANN MACHINE)

从上图看,RBM分为2层,第一层是可视层,表示为V; 第二层是隐藏层,表示为H. 层内无连接,层间全连接.

####参考资料
1. Smolensky, Paulgengxin. Chapter 6: [Information Processing in Dynamical Systems: Foundations of Harmony Theory.](http://www-psych.stanford.edu/~jlm/papers/PDP/Volume%201/Chap6_PDP86.pdf) Rumelhart, David E.; McLelland, James L. Parallel Distributed Processing: Explorations in the Microstructure of Cognition, Volume 1: Foundations. MIT Press. 1986: 194–281. ISBN 0-262-68053-X.
2. Hinton, G. E.; Salakhutdinov, R. R. Reducing the Dimensionality of Data with Neural Networks. Science. 2006, 313 (5786): 504–507. doi:10.1126/science.1127647. PMID 16873662.
3. Larochelle, H.; Bengio, Y. Classification using discriminative restricted Boltzmann machines. Proceedings of the 25th international conference on Machine learning - ICML '08. 536. 2008. doi:10.1145/1390156.1390224. ISBN 9781605582054.
4. Coates, Adam; Lee, Honglak; Ng, Andrew Y.. An analysis of single-layer networks in unsupervised feature learning, International Conference on Artificial Intelligence and Statistics (AISTATS). 2011.
5. Ruslan Salakhutdinov and Geoffrey Hinton (2010). Replicated softmax: an undirected topic model. Neural Information Processing Systems 23.
6. Miguel Á. Carreira-Perpiñán and Geoffrey Hinton (2005). On contrastive divergence learning. Artificial Intelligence and Statistics.
7. Hinton, G. Deep belief networks. Scholarpedia. 2009, 4 (5): 5947. doi:10.4249/scholarpedia.5947.
8. Ackley, D. Hinton, G. Sejnowski, T. A learning algorithm for boltzmann machines. 1985