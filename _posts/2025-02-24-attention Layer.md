---
title: "关于Attention Layer的林林总总"
layout: post
date: 2025-02-24
categories: tech_coding
tags:
    - LLM
    - Transformers
---



最近学习NLP对于attention layer的来历十分震惊, 居然一开始的attention layer就是建立在LSTM上的.
同时对并行计算也有了更深刻的理解. 上学的时候学并行计算, 学的非常痛苦. 那时候完全不理解为什么要不停的去计算矩阵乘法, 用各种各样的方式在各种不同结构的计算机上进行计算.

下面一篇LLM帮我总结的关于attention layer 的内容. 又是一个有什么问题问本人系列.

---


在自然语言处理（NLP）和时间序列分析领域，序列建模一直是核心挑战。随着研究的深入，序列建模方法经历了从传统循环神经网络（RNN）到门控循环单元（GRU）、长短期记忆网络（LSTM），再到 Attention 机制和 Transformers 的深刻演进。本文将简要回顾这一过程，并重点探讨 Transformer 的 Attention 机制有何独特之处。

### **1. RNN：序列建模的起点**

循环神经网络（RNN）是处理序列数据的早期深度学习架构。它通过隐藏状态（hidden state）将过去的信息传递到当前时刻，从而形成对时间序列的记忆。

**RNN 的优点**：
- 能处理变长输入序列。
- 结构简单，容易理解和实现。

**RNN 的局限**：
- **梯度消失/爆炸**：随着序列长度增加，反向传播过程中梯度可能会消失或爆炸，导致模型难以学习长期依赖。
- **长期依赖问题**：RNN 很难捕捉长时间间隔的信息。

### **2. GRU 和 LSTM：为了解决长期依赖问题而生**

为了解决 RNN 在捕捉长期依赖上的局限，研究者提出了 **LSTM（Long Short-Term Memory）** 和 **GRU（Gated Recurrent Unit）**。

**LSTM 的核心** 是引入了三个门控机制：
- **遗忘门**（Forget Gate）：决定保留多少旧信息。
- **输入门**（Input Gate）：决定加入多少新信息。
- **输出门**（Output Gate）：决定传递多少信息到下一个时间步。

**GRU 的改进** 相对 LSTM 更加简洁，它合并了遗忘门和输入门，减少了计算量，同时保持了良好的性能。

**GRU/LSTM 的优势**：
- 有效缓解了梯度消失问题。
- 更擅长处理长时间依赖。

**局限**：
- 仍然是序列性的计算，难以并行加速。
- 对长序列处理时效率不高。

### **3. Attention 机制的出现：打破顺序限制**

**Attention 机制** 的核心思想是：在处理每个输入时，模型不再仅依赖前一时刻的隐藏状态，而是可以 "关注" 输入序列中的所有其他部分，并根据它们的重要性进行加权聚合。

**Attention 的优点**：
- 可以捕捉长距离依赖关系。
- 支持并行计算，加快训练速度。
- 提高模型对关键部分的聚焦能力。

最初的 Attention 机制在机器翻译中取得了巨大成功（例如 Bahdanau Attention），但它真正引发变革是在 Transformer 架构中。

### **4. Transformer 的 Self-Attention：Attention 机制的革新**

**Transformer** 完全摒弃了 RNN 结构，采用 **Self-Attention（自注意力）** 机制。其核心思想是：序列中的每个元素可以对整个序列进行关注，从而捕捉全局依赖。

**Self-Attention 的计算公式**：
\[
\text{Attention}(Q, K, V) = \text{softmax}\left( \frac{QK^T}{\sqrt{d_k}} \right) V
\]
其中：
- \( Q \)（Query）、\( K \)（Key）、\( V \)（Value） 是通过输入向量线性变换得到的矩阵。
- \( d_k \) 是 Key 的维度，用于缩放。

**Transformer Attention 的独特之处**：
- **全局依赖建模**：每个位置都能直接关注整个序列。
- **并行计算**：摒弃了序列化计算，显著提升训练效率。
- **多头注意力（Multi-Head Attention）**：通过多组注意力机制并行学习不同的特征子空间。

### **5. 为什么 Transformer 会成为主流？**

- 训练速度快，能够充分利用 GPU 并行计算能力。
- 能捕捉全局依赖，效果优于传统序列模型。
- 具有更强的可扩展性，适合大规模预训练（例如 BERT、GPT 系列）。
