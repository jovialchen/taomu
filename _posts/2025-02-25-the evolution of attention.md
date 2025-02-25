---
title: "Attention Layer的演进"
layout: post
date: 2025-02-25
categories: tech_coding
tags:
    - LLM
---


**Attention 机制的演变：从 RNN 到 Transformer**

---

### **1. Attention 机制的基本概念**

#### **动机**

在传统的 Encoder-Decoder 模型中，编码器需要将整个输入序列压缩为一个固定长度的向量。这种方式对于长序列来说往往会导致信息丢失。Attention 机制通过让模型在生成每个输出时“聚焦”于输入序列中最相关的部分，从而缓解这一问题。

#### **核心思想**

Attention 模拟了人类在处理信息时聚焦于局部重点的能力。它通过计算注意力权重，衡量输入中各个部分对当前生成任务的重要性，然后利用这些权重加权求和，得到一个上下文向量，用以辅助生成当前输出。

---

### **2. Attention 在 RNN（LSTM）中的使用**

在基于 RNN（比如 LSTM）的 Encoder-Decoder 结构中，attention 机制主要用于解决固定向量瓶颈问题。

#### **工作流程**

<pre class="mermaid">
flowchart TD

    subgraph Encoder
        A1[x₁]
        A2[x₂]
        A3[x₃]
        A4[...]
        A5[x_T]
        H1[ h₁ ]
        H2[ h₂ ]
        H3[ h₃ ]
        H4[ ... ]
        H5[ h_T ]
    end

    A1 --> H1
    A2 --> H2
    A3 --> H3
    A4 --> H4
    A5 --> H5

    subgraph Decoder
        D1[sₜ]
    end

    H1 -- α₁ --> C[ ]
    H2 -- α₂ --> C
    H3 -- α₃ --> C
    H4 -- ...  --> C
    H5 -- α_T --> C

    C[上下文向量 cₜ] --> D1
    D1 --> |结合 cₜ 和 sₜ| Output[生成输出]
</pre>

#### **计算步骤**
1. **编码器输出隐藏状态**：将输入序列编码为隐藏状态序列 $$h_1, h_2, \dots, h_T$$。

2. **计算注意力权重**：在每个解码时间步 $$t$$，根据解码器当前状态 $$s_t$$ 和编码器的每个隐藏状态 $$h_i$$ 计算相似度（score）：
   - 加性注意力（Bahdanau Attention）：
     $$
     \text{score}(s_t, h_i) = \text{v}^T \tanh(W_1 s_t + W_2 h_i)
     $$
   - 点积注意力（Luong Attention）：
     $$
     \text{score}(s_t, h_i) = s_t^T h_i
     $$

3. **归一化**：将分数通过 softmax 转换为权重：
   $$
   \alpha_{t,i} = \frac{\exp(\text{score}(s_t, h_i))}{\sum_{j=1}^{T} \exp(\text{score}(s_t, h_j))}
   $$

4. **生成上下文向量**：根据注意力权重加权求和得到上下文向量：
   $$
   c_t = \sum_{i=1}^{T} \alpha_{t,i} h_i
   $$

5. **生成输出**：结合上下文向量 $$c_t$$ 和当前解码器状态 $$s_t$$ 生成输出。

---

### **3. 从 RNN Attention 到 Transformer Attention 的演变**

#### **3.1 注意力机制的升级**

最初，Attention 是作为 RNN 的辅助模块出现，用于弥补长序列信息传递不足的问题。但随着研究的深入，注意力被发现不仅能聚焦局部信息，还能捕捉全局关系，且具备更高的并行计算能力。

#### **3.2 Transformer 的创新**

Transformer 完全摆脱了 RNN 结构，依赖于自注意力（Self-Attention）机制来处理序列。每个输入位置都可以直接与序列中其他位置交互，从而捕捉长距离依赖。



#### **自注意力计算步骤**

1. **生成 Q、K、V**：
   $$
   Q = XW^Q, \quad K = XW^K, \quad V = XW^V
   $$

2. **缩放点积注意力**：
   $$
   \text{Attention}(Q, K, V) = \text{softmax}\left(\frac{QK^\top}{\sqrt{d_k}}\right) V
   $$

3. **多头注意力**：将 Q、K、V 分为多个头并行计算，然后将各头输出拼接起来，得到更丰富的语义表示。

#### **3.3 RNN 与 Transformer 的关键区别**

| 特性          | RNN Attention                    | Transformer Attention             |
|---------------|----------------------------------|-----------------------------------|
| 并行计算      | 否                               | 是                                |
| 长距离依赖    | 难以捕捉                          | 易于捕捉                           |
| 架构复杂度    | 高                               | 低（去掉了循环结构）              |
| 主要机制      | 依赖时间步递归                   | 依赖注意力机制进行全局信息聚合      |

---

### **4. 总结**

- **在 RNN（LSTM）中**，attention 作为辅助机制，帮助模型动态关注输入序列中最重要的信息，解决固定向量瓶颈问题。
- **在 Transformer 中**，注意力机制被全面推广，成为核心计算单元。通过自注意力和多头注意力，Transformer 能够捕捉全局依赖关系，实现更高效的并行计算。

这种演变体现了深度学习技术的发展趋势：从局部辅助机制到核心架构的转变，是模型在追求更高效、更准确表达能力过程中的关键突破。




