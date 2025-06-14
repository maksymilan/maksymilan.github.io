---
title: "跨领域分类：X-Cross 整合器的 LoRA 增强"
description: "本文介绍了 X-Cross 整合器的 LoRA 增强方法，旨在通过动态缩放和互动项提炼源领域模型的表示，提升跨领域分类任务的性能。"
publishDate: "29 May 2024"
updatedDate: "29 May 2024"
tags: ["X-Cross", "LoRA", "跨领域分类", "深度学习"]
---

### 符号定义 🔣

* $l$: 网络层索引 ($1 \le l \le L$)。
* $m$: 源领域索引 ($1 \le m \le n$)。
* $W_m^{(l)}$: 第 $m$ 个源模型在第 $l$ 层的**冻结**预训练权重矩阵。
* $A_m^{(l)}, B_m^{(l)}$: 第 $m$ 个源模型在第 $l$ 层的**冻结** LoRA 适配器矩阵。
* $x_m^{(l)}$: 第 $m$ 个源模型在第 $l$ 层的输入。对于第一层，$x_m^{(1)}$ 是提示（Prompt）的嵌入；对于 $l>1$，$x_m^{(l)} = \tilde{h}_m^{(l-1)}$。
* $h_m^{(l)}$: 第 $m$ 个源模型在第 $l$ 层的 LoRA 增强输出。
* $h_{concat}^{(l)}$: 第 $l$ 层所有源模型输出的连接表示。
* $W_{concat}^{(l)}$: 第 $l$ 层 X-Cross 整合器的**可训练**权重矩阵。
* $z^{(l)}$: 第 $l$ 层的动态缩放因子和互动项权重。
* $z_{[m']}^{(l,m)}, z_{[m,m']}^{(l,m)}$: 从 $z^{(l)}$ 派生出的，用于第 $m$ 个领域提炼的具体权重。
* $\tilde{h}_m^{(l)}$: 第 $m$ 个源模型在第 $l$ 层的**提炼后**表示。
* $\beta, \gamma$: 控制提炼过程中直接影响和互动项强度的超参数。
* $w_m$: 第 $m$ 个源模型在最终聚合时的**可学习**权重。
* $h^{final}$: 最终的整合表示。
* $h_{pooled}$: 池化后的表示。
* $W_p, b_p$: 池化层的**可学习**参数。
* $V_c, b_c$: 评分头的**可学习**参数。
* $S_u$: 用户 $u$ 的历史记录。
* $i$: 候选项目。
* $\text{score}(S_u, i)$: 候选项目的评分。

---

### 1. 逐层处理 ($l = 1, \dots, L$) ⚙️

#### a. LoRA 增强输出

对于每个源领域 $m$，计算其在第 $l$ 层的 LoRA 增强输出：
$$
h_{m}^{(l)} = (W_{m}^{(l)} + A_{m}^{(l)}B_{m}^{(l)}) \cdot x_{m}^{(l)}
$$
其中 $x_{m}^{(l)}$ 是第 $l$ 层的输入。

#### b. 连接源领域表示 (Stage 1)

将所有源领域的输出连接起来：
$$
h_{concat}^{(l)} = \text{Concat}(h_{1}^{(l)}, h_{2}^{(l)}, \dots, h_{n}^{(l)})
$$
这里 $h_{concat}^{(l)} \in \mathbb{R}^{n \cdot d}$。

#### c. 动态缩放 (Stage 2)

计算动态权重 $z^{(l)}$：
$$
z^{(l)} = W_{concat}^{(l)} \cdot h_{concat}^{(l)}
$$
这里 $z^{(l)} \in \mathbb{R}^{2n(n-1)}$。

#### d. 表示提炼与整合 (Stage 3)

为每个源领域 $m$ 计算提炼后的表示 $\tilde{h}_{m}^{(l)}$：
$$
\tilde{h}_{m}^{(l)} = h_{m}^{(l)} + \sum_{m' \ne m} \left( \beta \cdot z_{[m']}^{(l,m)} \cdot h_{m'}^{(l)} + \gamma \cdot z_{[m,m']}^{(l,m)} \cdot (h_{m}^{(l)} - h_{m'}^{(l)}) \right)
$$
这个 $\tilde{h}_{m}^{(l)}$ 将作为下一层 ($l+1$) 的输入。

---

### 2. 最终聚合与评分 🏆

#### a. 最终加权求和 (Stage 4)

在处理完所有 $L$ 层后，进行最终的加权聚合：
$$
h^{final} = \sum_{m=1}^{n} w_{m} \cdot \tilde{h}_{m}^{(L)}
$$

#### b. 候选项目评分

首先进行池化操作（以 [CLS] token 为例）：
$$
h_{pooled} = \text{GELU}(W_{p} \cdot h_{[CLS]}^{final} + b_{p})
$$
然后计算最终得分：
$$
\text{score}(S_{u}, i) = V_{c}^{T} \cdot h_{pooled} + b_{c}
$$

---

### 3. 训练损失 (Training Loss) 📉

模型训练使用负对数似然损失：
$$
L_i = -\log \frac{\exp(\text{score}(S_u, i))}{\sum_{i' \in \{i\} \cup I_{neg}} \exp(\text{score}(S_u, i'))}
$$
其中 $i$ 是真实的正样本，$I_{neg}$ 是负样本集合。

---