---
title: "循环神经网络"
description: "这篇文章介绍了循环神经网络（RNN）的基本原理、模型架构、训练方法以及在自然语言处理中的应用。"
publishDate: "5 Jan 2025"
updatedDate: "5 Jan 2025"
tags: ["RNN", "deep learning", "neural networks", "NLP"]
---

# 序列模型

在时间$t$观察到$x_t$,那么得到$T$个不独立的随机变量$(x_1,x_2,...,x_T)\sim p(x)$ 

使用条件概率展开
$$
p(a,b)=p(a)p(b|a)
$$
可以得到

正向序列概率求得


$$
p(\mathcal{x})=p(x_1)*p(x_2|x_1)*p(x_3|x_2x_1)*...*p(x_T|x_1x_2...x_{T-1})
$$
反向序列概率求得
$$
p(x)=p(x_t)*p(x_{T-1}|x_T)*p(x_{T-2}|x_{T-1}x_T)*...*p(x_1|x_2x_3x_4...x_T)
$$
马尔科夫假设：假设当前数据之和$\tau$个过去的数据点相关
$$
p(x_t|x_1x_2...x_{t-1})=p(x_t|x_{t-\tau},...x_{t-1})=p(x_t|f(x_{t-\tau,...,x_{t-1}}))
$$
潜变量模型

引入潜变量$h_t$来表示过去信息$h_t=f(x_1,...,x_{t-1})$
$$
x_t=p(x_t|h_t)
$$
时序模型中，当前数据和之前观察的数据相关，自回归模型用自身的过去数据来预测未来。

自回归模型使用自身过去数据预测未来

马尔可夫假设模型当前之和最近最少数数据有关，从而简化模型

潜变量使用潜变量来概括历史信息

# 循环神经网络

RNN的模型是由输入$x_{t-1}$和隐变量 $h_{t-1}$计算得到$h_t$，然后由$h_t$进行计算得到 $o_t$，实现对输入$x_t$的下一个元素的预测，$\phi$是激活函数

更新隐藏状态的方程
$$
h_t=\phi(W_{hh}h_{t-1}+W_{xh}x_{t-1}+b_h)
$$
得到输出
$$
o_t=W_{ho}h_t =b_o
$$
$h_t$处于隐藏层，也叫做hidden state，作用是可以储存历史信息，从而实现对时间序列数据的建模，上面这个例子里表示的是单层的循环神经网络架构。

具体来说网络的工作流程如下：

```makefile
输入:     我       喜欢       学习
          ↓        ↓         ↓
隐藏层:   h_1  →   h_2   →   h_3
          ↓        ↓         ↓
输出:    喜欢      学习       <预测下一个单词>
```

## 困惑度

利用平均交叉熵来对我们当前的模型的好坏进行评价
$$
\pi=\frac{1}{n}\sum_{i=1}^n-\log{p(x_t|x_{t-1},x_{t-2},...)}
$$
$p$是语言模型的预测概率，$x_t$是输入的真实词

由于历史原因，NLP使用困惑度($\exp(\pi)$)来衡量模型好坏，代表了每一次预测时预测的结果的个数，1代表下一个预测结果只有一种可能，无穷大代表有无数种可能

## 梯度剪裁

如果输入长度为$T$，那么在每一个隐藏层的迭代过程中就要计算$T$个时间步上的梯度，在反向传播过程中产生长度为 $O(T)$的矩阵乘法链，导致数值不稳定。梯度剪裁能有效预防梯度爆炸（梯度的长度变得很大，导致训练不稳定或参数更新过大），下面这个表达式就表示了梯度裁剪的计算过程，$\theta$是设定的超参数，是我们设定的允许的最大梯度，$g$是当前的梯度向量，$||g||$代表向量的模长，当梯度超过了阈值的时候，就会自动将梯度进行拉回
$$
g\leftarrow\min(1,\frac{\theta}{||g||})g
$$

## RNN的应用

下面的输入输出的数量是以一个token为一个输入来计量的

- 由一个输入生成一个输出（MLP）
- 由一个输入生成多个输出（文本生成）
- 由多个输入生成一个输出（对文本内容进行分类）
- 由多个输入生成多个输出（问答或机器翻译）

# 代码实现

## 数据处理

**对输入数据进行独热编码**

在训练过程中，我们假设有batch_size个文本，每个文本的长度为t，同时vocab中所有的词数为vocab_size，我们要先将文本转换为可以计算的向量，这个过程是独热编码（one-hot）,由 $\text{batch_size*t}$ 的文本输入可以得到$\text{batch_size*t*vocab_size}$的向量，这是因为每一个长度为t的文本的每一个词都是一个长度为$\text{vocab_size}$的向量，为了在后续的遍历每一个时间步的计算过程中更方便，我们先对$\text{batch_size*t}$进行转置，然后进行独热编码（这样得到的就是 $\text{t*batch_size*vocal_size}$能直接进行运算）。

```python
X.shape()
#[batch_size,t]
torch.nn.functional.one_hot(X.T,vocal_size)
```

**对计算参数的解析**

```python
#初始化模型参数
def get_params(vocab_size,num_hiddens,device):
    num_inputs = num_outputs = vocab_size

    def normal(shape):
        return torch.randn(size=shape,device=device)*0.01
    
    W_xh = normal((num_inputs,num_hiddens))
    W_hh = normal((num_hiddens,num_hiddens))
    b_h = torch.zeros(num_hiddens,device=device)
    W_hq = normal((num_hiddens,num_outputs))
    b_q = torch.zeros(num_outputs,device=device)
    params = [W_xh,W_hh,b_h,W_hq,b_q]
    for param in params:
        param.requires_grad_(True)
    return params
```

```python
#RNN模型
def rnn(inputs,state,params):
    W_xh,W_hh,b_h,W_hq,b_q = params
    H, = state
    outputs = []
    for X in inputs:#X的形状是(batch_size,vocab_size)，按照第一维（也就是时间步）进行遍历
        H = torch.tanh(torch.mm(X,W_xh)+torch.mm(H,W_hh)+b_h)
        Y = torch.mm(H,W_hq)+b_q
        outputs.append(Y)
    return torch.cat(outputs,dim=0),(H,)
```

在`RNN`计算过程中，每一个`X`代表的是按照时间步从0开始的元素，形状为$\text{batch_size*input_size}$,表示有$\text{batch_size}$个元素参与运算，每个元素的特征个数为$\text{vocab_size}$，所以由输入层到隐藏层的参数的设置为$\text{input_size*hidden_size}$，其中$\text{hidden_size}$表示的是隐藏层的特征维度个数，在隐藏层之间的参数的形状就是$\text{hidden_size*hidden_size}$，从隐藏层到输出层的参数设置为$\text{hidden_size*output_size}$，其中$\text{output_size}$是输出元素的特征维度。在我们的这个模型中，我们是想用一本书来训练一个模型，让这个模型预测书中的内容，所以有$\text{input_size = out_put_size = vocab_size}$，因为输入输出的词都是在我们定义的$\text{vocab}$当中。

`H`是隐变量，特征维数为$\text{hidden_size}$，同时由于每一个`X`的字都会有一个对应的隐变量，所以在矩阵运算中`H`的形状为$\text{batch_size*hidden_size}$，在隐藏层进行运算的时候不会改变隐变量的形状

## 模型的训练函数

```python
#一个迭代周期内的训练函数
def train_epoch_ch8(net,train_iter,loss,updater,device,use_random_iter):
    state,timer = None,d2l.Timer()
    metric = d2l.Accumulator(2)
    for X,Y in train_iter:
        if state is None or use_random_iter:
            state = net.begin_state(batch_size=X.shape[0],device=device)
        else: 
            if isinstance(net,RNNModelScratch) and not use_random_iter:
                for s in state:
                    s.detach_()
            else:
                state.detach_()
        y = Y.T.reshape(-1)
        X,Y = X.to(device),Y.to(device)
        y = y.to(device)
        y_hat,state = net(X,state)
        l = loss(y_hat,y).mean()
        if isinstance(updater,torch.optim.Optimizer):
            updater.zero_grad()
            l.backward()
            grad_clipping(net,1)
            updater.step()
        else:
            l.backward()
            grad_clipping(net,1)
            updater(batch_size=1)
        metric.add(l*y.numel(),y.numel())
    return math.exp(metric[0]/metric[1]),metric[1]/timer.stop()
```

对于这个训练函数，流程如下

1. 首先进行累加器的初始化，用于计算损失
2. 第一个`for`循环是对`state`进行初始化
   - 如果是在`state`没有初始化或者每一次的批量都是随机从样本中抽取的，我们就将state初始化为形状为 $\text{batch_size*hidden_size}$ ，$0$填充的张量。如果每一个批次都是随机的，那么每一次的state都应该和上一个批次的state无关，所以初始化为0。
   - 如果是在`state`已经初始化了，并且是采用的顺序的迭代器，每一个批次在原文本中都是紧挨着上一个批次，那么我们就需要对`state`进行保留，但是需要对其进行`detach`操作，这样是为了避免长序列导致的梯度爆炸问题。
     - `detach`：我们举例说明detach的作用，令$x=2$,$y=x^2$,那么对$y$求梯度就等于$4$,我们令,$\text{y_detach=y.detach()}$$f=y*x$,$\text{g = y_detach*x}$,分别对$f$和$g$进行求梯度，结果分别是$\text{grad_f = 12,grad_g=4}$。从这个例子我们可以得到， $\text{detach}$操作实际上就是断开两个函数间的梯度关系。
3. 初始化完成后就是计算过程，`Y`是返回的标签序列，形状为$\text{batch_size*t}$,`X`的形状是一样的，将`Y`转置后按序列长度拼接为一个列向量。这么做是为了与`X`计算得到的$\hat{Y}$的形状对应，然后进行平均损失计算。
4. 计算完成后就是进行参数更新，如果是调用的torch库函数的优化器的话，我们就直接先清空梯度，然后进行`backward`运算，进行梯度裁剪，最后是更新参数。如果是自定义的优化器，就调用对应的函数。
5. 最后是返回困惑值

# GRU 门控循环单元