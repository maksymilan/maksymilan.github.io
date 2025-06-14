---
title: Pytorch的小trick
date: 2024-12-10 17:43:05
updated: "10 Dec 2024"
publishDate: "10 Dec 2024"
description: "这篇文章介绍了Pytorch的安装、基本函数、矩阵乘法、数学定义和运算、建立神经网络前的知识等内容，帮助读者快速上手Pytorch。"
tags: ["Pytorch", "deep learning", "neural networks", "ai"]
---

## pytorch的安装

在已经安装的python环境的前提下，只需要在命令行输入

```bash
pip install torch torchvision torchaudio
```

即可。

### 预备知识

### 基本函数

1. torch.empty(int size,int size)

   创建一个指定大小的张量，张量中的值是未初始化的，也就是任意的，参数代表的是张量的维度

   ```python
   x = torch.empty(5, 3)
   ```

2. torch.zeros(int,int,dtype)

   创建指定大小的张量，所有元素初始化为0，第三个参数指定元素的类型

   ```python
   x = torch.zeros(5, 3, dtype=torch.long)
   ```

3. torch.rand(int,int)

   创建指定大小的随机张量，元素的值是随机从0到1的区间取的

   ```python
   x = torch.rand(5, 3)
   ```

4. torch.tensor()

   手动创建一个张量，自己给定数字进行创建

   ```python
   x = torch.tensor([5.5, 3])
   ```

5. torch.add(x,y)

   对两个张量进行逐元素相加的操作，x,y这两个张量的形状必须相同

   ```python
   torch.add(x, y)
   ```

### 矩阵乘法

1. **torch.matmul()**

   最通用的矩阵乘法，能处理高位数据

   ```python
   import torch
   
   # 创建两个2x3和3x2的矩阵
   a = torch.tensor([[1, 2, 3],
                     [4, 5, 6]])
   b = torch.tensor([[7, 8],
                     [9, 10],
                     [11, 12]])
   
   # 矩阵乘法
   c = torch.matmul(a, b)
   print(c)
   ```

2. **torch.mm()**

   专门用于二维矩阵的乘法。

   ```python
   import torch
   
   # 创建两个2x3和3x2的矩阵
   a = torch.tensor([[1, 2, 3],
                     [4, 5, 6]])
   b = torch.tensor([[7, 8],
                     [9, 10],
                     [11, 12]])
   
   # 矩阵乘法
   c = torch.mm(a, b)
   print(c)
   ```

3. **@运算符**

   功能和**torch.matmul()**一样。

   ```python
   import torch
   
   # 创建两个2x3和3x2的矩阵
   a = torch.tensor([[1, 2, 3],
                     [4, 5, 6]])
   b = torch.tensor([[7, 8],
                     [9, 10],
                     [11, 12]])
   
   # 矩阵乘法
   c = a @ b
   print(c)
   ```

### 数学定义和运算

**矩阵的点乘(哈达玛积)**
$$
z = y*y
$$
对应位置的元素进行相乘，不是矩阵乘法

**梯度**

- .backward()方法
- .grad()方法

**自动求导（Autigrad）**

在张量创建时设置，requires_grad = True,Pytorch就会开始对追踪对该张量的所有操作，完成计算后调用 .backward就会计算所有梯度并复制到.grad属性里面。

```python
# 创建一个张量并设置requires_grad=True用来追踪其计算历史
x = torch.ones(2, 2, requires_grad=True)

# 对张量进行操作
y = x + 2
z = y * y * 3
out = z.mean()

# 计算梯度
out.backward()

# 打印梯度 d(out)/dx
print(x.grad)
#答案是2*2的元素均4.5的矩阵
```

### 建立神经网络前应该知道的

**.liear()方法**

.liear(in_features,out_features)创建一个线性的映射关系，用线性函数来计算输出，这个线性函数可以表示为
$$
y=xA^T+b
$$
$x$是输入的向量，大小由in_features决定，$A$是权重矩阵，由电脑根据输入输出的特征值自动生成，一般使用均匀分布或正态分布，$b$是偏置向量(Bias),通常初始化为0，这是因为偏置的初值通常不会对网络初期的学习造成太大影响，而且从零开始可以保持初始化的简单性。（这里的转置计算是出于编程的便利考虑，无深意）

$x$可以是一个多维的向量，也就是矩阵，我们把这种情况叫做批处理

```python
import torch

# 假设每个样本有2个特征
features = torch.tensor([[1.0, 2.0], [1.5, 2.5], [2.0, 3.0], [2.5, 3.5]])
# 这里 features 的大小是 [4, 2]
# 4 是批处理大小，2 是特征数量

layer = torch.nn.Linear(2, 3)
output = layer(features)
print(output)
```

最后得到的是一个$[4\times 3]$的矩阵

**torch.nn**

torch.nn提供很多预定的层，比如全连接层(Linear),卷积层(Conv2d),池化层(MaxPool2d)等

**torch.nn.functional**

torch.nn.functional包含了torch.nn里面层的函数实现，每个操作可以通过它来作为为一个独立的函数使用，而不需要事先创建对象。

**激活函数**

引入非线性性质，使得神经网络可以学习和模拟复杂的函数，如分类边界和连续的函数映射。

常见的激活函数有

- **ReLU**:对输入执行非线性变换$f(x)=max(0,x)$
- **Sigmoid**:将输入压缩到0到1之间，常用于二分类问题中
- **Tanh**:将输入压缩到-1和1之间，形状和Sigmoid类似但是输出范围更广
- **Softmax**:常用于多酚类问题的输出层，会将输入转换为概率分布

**super函数继承父类**

super是python的内置函数，用于调用父类（超类）的方法。常用于构造函数中，确保父类被正确初始化，也可以用来调用其他继承自父类的方法。super内部的参数可以不写（python3）。

super函数的用法

```python
class Parent:
    def __init__(self):
        print("Parent __init__")

class Child(Parent):
    def __init__(self):
        super().__init__()
        print("Child __init__")

child = Child()
```

### 构建简单的神经网络

```python
import torch
import torch.nn as nn
import torch.nn.functional as F

class Net(nn.Module):

    def __init__(self):
        super(Net, self).__init__()
        # 定义网络层
        self.fc1 = nn.Linear(2, 3)  # 2输入到3输出
        self.fc2 = nn.Linear(3, 2)

    def forward(self, x):
        # 定义前向传播
        x = F.relu(self.fc1(x))
        x = self.fc2(x)
        return x

# 实例化网络
net = Net()
print(net)
```

## 数据处理

查看pandas读取的文件

- 使用 `print(df)` 输出整个 DataFrame。
- 使用 `df.head()` 查看前几行数据，默认显示前 5 行。
- 使用 `df.info()` 获取 DataFrame 的概要信息。
- 使用 `df.describe()` 获取数值型列的统计信息。

### Dropout

`torch.rand`:生成0，1之间的均匀分布的矩阵

`torch.rand` 的基本语法是：

```python
torch.rand(*size, out=None, dtype=None, layout=torch.strided, device=None, requires_grad=False)
```

- `*size`：指定张量的形状，可以是一个或多个维度的大小，例如 `(3, 4)`、`(2, 3, 4)` 等。
- `out`：输出张量（默认为 `None`），如果指定，生成的结果会存储在该张量中。
- `dtype`：生成张量的数据类型，默认为 `torch.float32`。
- `layout`：张量的布局方式（一般默认为 `torch.strided`）。
- `device`：张量所放置的设备（CPU 或 GPU），默认为 `None`，表示张量生成在当前设备上。
- `requires_grad`：是否需要计算梯度，默认为 `False`。

`torch.rand_like`生成和输入的向量形状一样的向量

**布尔掩码**

在实现DropOut的时候，如何对一个张量里的每一个元素按照给定的概率进行随机置零呢。我们可以按照给定的概率使用 `torch.rand_like` 生成0，1矩阵，这个矩阵也就是布尔掩码，然后用这个矩阵和张量进行逐元素相乘。当然，如果是在实现Dropout层的话，还要对得到的结果乘以$\frac{1}{1-p}$来得到最后的张量。

```python
mask = torch.rand_like(x) > p
```

### BatchNorm

实现归一化，首先有两个学习参数，一个是缩放参数$\gamma$，一个是偏移参数$\beta$,在学习过程中，由每一个样本的均值和方差，不断更新总的均值和方差，但是训练过程中的计算是用的该样本的均值和方差，最后计算出的总均值和方差是用于训练完成之后的计算使用。

`torch.nn.Parameter`

`torch.nn.Parameter` 是一个包装类，用于将一个张量标记为模型的参数，用`Parameter`初始化的参数都会在训练过程中自动更新（相当于创建向量时，requires_grad=True），在`nn.Module`定义的类当中使用Parameter，会将该张量添加到模型参数中进行自动更新

你可以使用 `torch.nn.init` 模块对 `Parameter` 进行初始化

```python
from torch.nn import init

# 初始化权重为正态分布
init.normal_(model.weight, mean=0.0, std=1.0)

# 初始化偏置为 0
init.zeros_(model.bias)
```

`torch.mean` 用于计算给定张量的均值（平均值）。均值是所有元素之和除以元素的数量。

```python
torch.mean(input, dim=None, keepdim=False, dtype=None)
```

默认情况下是对整个张量求均值，得到的是一个标量数字，如果指定了dim，那么就会按照指定的方向求均值，比如在二维张量中dim为0，那么就对对所有的第一维求均值（也就是对所有的行求均值）。

每沿着一个dim求均值，这个dim维度内得元素个数就会下降到1，比如对维度为[2,3,4,4]的张量关于dim=(0,2,3)求均值后，在keepdim=True的条件下就会变成[1，3，1，1]

`squeeze`方法就是去除维度为1的轴。

`torch.var` 用于计算给定张量的方差。

**广播机制**

对于二维的情况下，计算均值和方差的时候，我们利用广播机制进行计算，先将形状为[num_fetures]的参数进行x.view(1,-1,1,1)的操作，然后与输入张量进行运算，由于广播机制会自动通过复制已有值得方式补全维度不一样的部分，所以可以实现运算。

### 卷积层

#### `Conv2d` 的常见参数：

```python
torch.nn.Conv2d(in_channels, out_channels, kernel_size, stride=1, padding=0, dilation=1, groups=1, bias=True, padding_mode='zeros')
```

- **`in_channels`**：输入的通道数（例如，RGB 图像的通道数为 3）。
- **`out_channels`**：卷积层输出的通道数，即卷积核的数量。
- **`kernel_size`**：卷积核的大小，可以是一个整数（表示正方形卷积核）或一个元组 `(height, width)`。
- **`stride`**：步幅，默认是1，表示卷积核每次移动的像素步长。
- **`padding`**：填充大小，默认是0，用于在输入的边缘添加零填充。
- **`dilation`**：扩张卷积的步幅，默认为1。
- **`groups`**：分组卷积的数量，默认是1，表示标准卷积。
- **`bias`**：是否添加偏置项，默认是 `True`。
- **`padding_mode`**：填充的方式，默认为 `'zeros'`，也可以是 `'reflect'` 或 `'replicate'`。

### 池化层

池化层会将一个窗口替换为一个元素，一个4*4的张量，经过2 *2的池化后就会变成一个2 *2的张量

**最大池化&平均池化** 

```python
torch.nn.MaxPool2d(kernel_size, stride=None, padding=0, dilation=1, return_indices=False, ceil_mode=False)
torch.nn.AvgPool2d(kernel_size, stride=None, padding=0, dilation=1, count_include_pad=True, ceil_mode=False)
```

- **`kernel_size`**：池化窗口的大小。可以是一个整数（表示正方形的池化窗口）或一个元组 `(height, width)`（表示矩形池化窗口）。
- **`stride`**：池化操作的步幅。默认情况下，步幅等于 `kernel_size`，即池化窗口每次移动一个窗口的大小。如果步幅为 `None`，则步幅默认为 `kernel_size`。
- **`padding`**：在输入的边缘添加的零填充。默认值为 0。
- **`dilation`**：池化的膨胀系数，默认值为 1。通常用于扩张卷积，池化时不太常用。
- **`return_indices`**：对于 `MaxPool2d`，如果设置为 `True`，池化层会返回池化操作中最大值的索引（用于反向传播时的梯度计算）。默认值为 `False`。
- **`ceil_mode`**：如果为 `True`，则采用向上取整的方式来计算输出大小。默认值为 `False`。