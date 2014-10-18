---
title: "关于训练限制波兹曼机(RBM)的实践指导"
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

####1 简介


####2 RBM和CD概述

> 如果你已经了解RBM相关内容,跳过这一小节

给定一个二值向量表示的训练集,为了方便解释,我们假设二值向量是二值图像.这个训练集能够被一个叫作限制波尔兹曼机(RBM)的两层网络模拟出来(Smolensky,1986; Freund and Haussler, 1992; Hinto,2002),在这个RBM网络中,随机的二值像素通过对称的权值连接到随机的二值特征检测器.像素对应RBM的"可视"单元,因为它们的状态是可观查的;特征检测器对应"隐藏"单元.一个可视单元和隐藏单元(v,h)的联合配置(joint configuration)有一个能量(Hopfield,1982),可表示为:

<img src="http://www.forkosh.com/mathtex.cgi? \Large E(\mathbf{v};\mathbf{h})=\sum_{i\in visible}a_iv_i-\sum_{j\in hidden}b_jh_j-\sum_{i,j}v_ih_jw_{ij}">

其中

<img src="http://www.forkosh.com/mathtex.cgi? \Large v_i, h_j, a_i, b_j, w_{ij}">

对应第i个可视单元的二值状态, 第j个隐藏单元的二值状态, 第i个可视单元的偏置, 第j个隐藏单元的二值偏置, 可视单元和隐藏单元之间的权值.网络通过能量函数给每一个可能连接的可视向量和隐藏向量分配一个概率,表示为:
<img src="http://www.forkosh.com/mathtex.cgi? \Large p(\mathbf{v},\mathbf{h})=\frac{1}{Z}e^{-E(\mathbf{v},\mathbf{h})}">
其中"分配函数"Z表示所有可能的可视向量和隐藏向量对的累加和
<img src="http://www.forkosh.com/mathtex.cgi? \Large Z=\sum_{\mathbf{v},\mathbf{h}}e^{-E(\mathbf{v},\mathbf{h})}">
网络分配给可视层向量v的概率表示所有隐藏层向量的累加和:
<img src="http://www.forkosh.com/mathtex.cgi? \Large p(\mathbf{v})=\frac{1}{Z}\sum_{\mathbf{h}}e^{-E(\mathbf{v},\mathbf{h})}">
网络分配给训练图像的概率可以通过调整权值和偏置使图片的能量最低的方式生成并生成其它图像的能量,特别是这些具有低能量的图像对配分函数有很大的贡献.训练向量的概率的对数关于权值的导数是相当地简单:
<img src="http://www.forkosh.com/mathtex.cgi? \Large \frac{\partial \log p(\mathbf{v})}{\partial w_{ij}}=\langle v_ih_j\rangle_{data} - \langle v_ih_j\rangle_{model}">
其中三角括号表示在下标指定的分布的期望.这样就生成了一个非常简单的学习规则用于训练数据的概率对数计算随机最速上升:
<img src="http://www.forkosh.com/mathtex.cgi? \Large \Delta w_{ij}=\epsilon (\langle v_ih_j\rangle_{data} - \langle v_ih_j\rangle_{model})">
其中ε(epsilon)表示学习率.

> 上述等号左边的第一项是数据期望项,第二项是模型期望项

因为在一个RBM网络的隐藏单元之间不存在直接的连接,非常容易得到数据期望项的无偏采样.给定一张随机选择的训练图像v,根据概率公式将每个隐藏单元h的状态设为1.
<img src="http://www.forkosh.com/mathtex.cgi? \Large p(h_j=1|\mathbf{v})=\sigma (b_j+\sum_{i}v_iw_{ij})">
(7)

σ(x)表示逻辑sigmoid函数1/(1+exp(-x))

因为在一个RBM网络的可视单元之间不存在直接的连接,在已知隐藏层向量的情况下,非常容易的到可视单元状态的无偏采样.
<img src="http://www.forkosh.com/mathtex.cgi? \Large p(v_i=1|\mathbf{h})=\sigma (a_i+\sum_{j}h_jw_{ij})">
(8)

然而,获得模型期望项的无偏采样无疑更加困难.可以这样做,从可视单元的随机状态开始,在一个非常长的时间里交叉地进行Gibbs采样.在一次迭代里,交叉Gibbs采样由根据公式(7)并行地更新所有隐藏单员的状态,紧接着根据公式(8)并行地更新所有可视单员的状态组成.

> 在训练过程中，首先将可视向量值映射给隐藏单元；然后可视单元由隐藏单元重建；这些新可视单元再次映射给隐藏单元，这样就获取新的隐藏单元。执行这种反复步骤叫做吉布斯采样

一个更快的训练过程由Hintion(2002)提出.从把可视单元设置成一个训练向量开始,根据公式(7)并行地计算隐藏单元的二值状态.一旦选定隐藏单元的二值状态,通过根据公式(8)得出的概率设每个可视单元为1的方式,重构就完成了.
<img src="http://www.forkosh.com/mathtex.cgi? \Large \Delta w_{ij}=\epsilon (\langle v_ih_j\rangle_{data} - \langle v_ih_j\rangle_{recon})">

各层单元的偏置的学习规择就更加简单.
> <img src="http://www.forkosh.com/mathtex.cgi? \Large \Delta a_{i}=\epsilon (\langle v_i\rangle_{data} - \langle v_i\rangle_{recon})">
> <img src="http://www.forkosh.com/mathtex.cgi? \Large \Delta b_{j}=\epsilon (\langle h_j\rangle_{data} - \langle h_j\rangle_{recon})">


这个学习的效果非常地好,尽管只是粗糙地逼近训练数据概率对数的梯度(Hinton,2002).这个学习规则非常逼近另一个叫对比分歧的对象函数的梯度.CD不同于Kullback-Liebler分歧,但是它忽略了对象函数中的一个技巧项,所以它不同于梯度.确实,Sutskever和Tieleman已经证明CD不同于任何函数的梯度(Sutskever,Tieleman,2010).然而CD可以工作地很好以致于能够成功地使用于许多重大的应用.

如果在为学习规则中第二项收集统计数据(叫作负统计)之前,多次进行交叉的Gibbs采样,RBM可以学出更好地模型.CDn表示学习中使用了n步交叉Gibbs采样.

> 概率图模型的能量和概率之间的关系可以参阅[gibbs measure](http://en.wikipedia.org/wiki/Gibbs_measure)和[Hammersley-Clifford theorem](http://en.wikipedia.org/wiki/Hammersley%E2%80%93Clifford_theorem)

> [Gibbs free energy](http://en.wikipedia.org/wiki/Gibbs_free_energy)


####3 当使用CD时,如何收集统计数据

首先,我们假设所有的可视单元和隐藏单元都是二值的.其它类型的单元将在13节讨论.我们同样可以假设学习的目的是产生一个很好的训练向量集的生成模型.当使用RBM去学习深度信念网络(),生成模型不是最后的目标,而且可能通过低拟和的方式节省时间,但这里不考虑这个.

#####3.1 更新隐藏层状态

假设隐藏单元是二值的,同时你正在使用CD1,当隐藏单元被数据向量驱动时,它们应该是随机二值状态.激活一个隐藏单元j的概率是"总输入"的逻辑函输值σ(x)=1/(1+exp(-x)):
<img src="http://www.forkosh.com/mathtex.cgi? \Large p(h_j=1)=\sigma (b_j+\sum_{i}v_iw_{ij})">
如果概率大于均匀分布在[0,1]之间的一个随机值,则隐藏单元激活.

隐藏单元使用二值状态而不是用概率用本身,这一点非常重要. 如果使用概率,在重构中每一个隐藏单元能够联系一个实数值. 这严重违背了信息瓶颈,它基于这样一个事实:一个隐藏单元最多用1位(平均情况下)来表示. 这个信息瓶颈起到了很强的约束.(不是很懂)

对于最后更新的隐藏单元,因为不取绝于选择哪个状态,就使用随机地二值状态. 所以使用概率本身可以避免不必要的采样噪声. 当使用CDn,只有最后更新的隐藏单元使用概率.(这里感觉有点矛盾)

#####3.2 更新可视层状态



