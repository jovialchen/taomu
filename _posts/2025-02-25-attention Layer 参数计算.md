---
title: "Attention Layer的参数计算"
layout: post
date: 2025-02-25
categories: tech_coding
tags:
    - LLM
---

一些关于transfomers的思考. 谢谢LLM解答.


---

### 1. 注意力公式

Transformer 的核心注意力计算公式为：
$$
\text{Attention}(Q, K, V) = \text{softmax}\Big(\frac{QK^T}{\sqrt{d_k}}\Big)V
$$

其中：
- $$Q$$、$$K$$、$$V$$ 分别是查询（Query）、键（Key）和值（Value）的矩阵；
- $$d_k$$ 是每个查询和键的向量维度；
- $$\sqrt{d_k}$$ 是缩放因子，用于控制点积值的尺度（下面详细解释原因）。

---

### 2. 注意力层的参数计算

#### **(1) 线性变换**

- 输入为 $$X \in \mathbb{R}^{N \times d_{\text{model}}}$$，其中 $$N$$ 是序列长度，$$d_{\text{model}}$$是模型的隐藏维度。
- 通过三个不同的线性变换得到 $$Q$$、$$K$$ 和 $$V$$：
 $$ 
  Q = XW^Q,\quad K = XW^K,\quad V = XW^V
 $$
- 其中权重矩阵 $$W^Q, W^K, W^V \in \mathbb{R}^{d_{\text{model}} \times d_k}$$。在多头注意力中，通常设 $$d_k = d_{\text{model}}/h$$（$$h$$ 为头数）。

#### **(2) 多头注意力**

- **每个头：** 都使用各自独立的 $$W^Q, W^K, W^V$$ 计算出 $$Q, K, V$$ 后独立计算注意力；
- **参数数量：** 每个头的参数量为 $$3 \times (d_{\text{model}} \times d_k)$$，共 $$h$$ 个头，所以总共为：
  $$
  3 \times h \times (d_{\text{model}} \times d_k)
  $$
- **拼接与输出：** 各个头的输出拼接成一个矩阵（尺寸为 $$N \times (h \times d_k) = N \times d_{\text{model}}$$），然后经过一个线性变换 $$W^O \in \mathbb{R}^{d_{\text{model}} \times d_{\text{model}}}$$ 得到最终输出。
  
- **总体参数量（忽略偏置）：**
  $$
  3 \cdot d_{\text{model}}^2 + d_{\text{model}}^2 = 4d_{\text{model}}^2\quad (\text{因为 } h \times d_k = d_{\text{model}})
  $$


---



---

### 4. 图示说明

下面是一个简单的示意图，展示了注意力层的计算流程：

```
             输入 X
               │
       ┌─────────────┐
       │  线性变换   │
       │ (W^Q, W^K, W^V)  ──────────────► 生成 Q, K, V
       └─────────────┘
               │
               ▼
        ┌─────────────┐
        │   Q, K, V   │
        └─────────────┘
               │
               ▼
        计算 QK^T 点积
               │
               ▼
         除以 √d_k 进行缩放
               │
               ▼
            softmax
               │
               ▼
       加权求和值（乘以 V）
               │
               ▼
        ┌─────────────┐
        │ 得到输出：  │
        │  Attention  │
        └─────────────┘
               │
       （多头时：各头输出拼接后再线性变换）
               │
               ▼
          最终输出结果
```

---

### 总结

- **注意力公式：**
  $$
  \text{Attention}(Q, K, V) = \text{softmax}\Big(\frac{QK^T}{\sqrt{d_k}}\Big)V
  $$
- **参数计算：** 通过对输入进行三个线性变换得到 Q, K, V，每个变换的参数为 $$d_{\text{model}} \times d_k$$；在多头注意力中，总参数量大约为 $$4d_{\text{model}}^2$$（不计偏置）。


