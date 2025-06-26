---
title: "研究了一下向量数据库: FAISS和LanceDB"
layout: post
date: 2025-06-26
categories: tech_coding
tags:
    - RAG
---


为了比较lancedb和FAISS在使用方法行区别, 这里使用 **FAISS** 和 **LanceDB** 分别实现：

* 将 10 条文本数据生成 embedding
* 添加元数据（例如 ID、来源等）
* 存储到本地
* 查询用户问题，匹配出最相关的 1-2 条文本和元数据
* 发给大语言模型（如 OpenAI GPT）

---

## ✅ 一、FAISS 实现：带元数据本地向量检索

### 💾 本地保存结构

* `index.faiss`：FAISS 向量索引
* `metadata.json`：文本 + 元数据列表，按索引对齐

### 📦 依赖

```bash
pip install faiss-cpu sentence-transformers openai
```

### 🧩 1. 构建并保存索引和元数据

```python
import faiss
import json
import numpy as np
from sentence_transformers import SentenceTransformer
import os

# 示例文本与元数据
documents = [
    {"text": "猫是哺乳动物。", "id": "doc1", "source": "百科"},
    {"text": "狗喜欢追球。", "id": "doc2", "source": "宠物指南"},
    {"text": "鲸鱼生活在海里。", "id": "doc3", "source": "海洋资料"},
    {"text": "鸟类会飞。", "id": "doc4", "source": "动物分类"},
    {"text": "鱼在水中游。", "id": "doc5", "source": "水生动物"},
    {"text": "蛇是冷血动物。", "id": "doc6", "source": "爬行动物"},
    {"text": "大象是最大的陆地动物。", "id": "doc7", "source": "哺乳动物资料"},
    {"text": "老虎是猫科动物。", "id": "doc8", "source": "动物百科"},
    {"text": "企鹅不会飞但会游泳。", "id": "doc9", "source": "极地动物"},
    {"text": "袋鼠生活在澳洲。", "id": "doc10", "source": "澳洲自然"}
]

# 建立向量模型
model = SentenceTransformer("all-MiniLM-L6-v2")
texts = [doc["text"] for doc in documents]
embeddings = model.encode(texts).astype("float32")

# 保存 FAISS 索引
index = faiss.IndexFlatL2(embeddings.shape[1])
index.add(embeddings)
os.makedirs("faiss_db", exist_ok=True)
faiss.write_index(index, "faiss_db/index.faiss")

# 保存元数据
with open("faiss_db/metadata.json", "w", encoding="utf-8") as f:
    json.dump(documents, f, ensure_ascii=False, indent=2)
```

---

### 🧩 2. 加载并匹配查询（返回文本 + 元数据）

```python
import openai

openai.api_key = "your-api-key"

def query_faiss(query, top_k=2):
    index = faiss.read_index("faiss_db/index.faiss")
    with open("faiss_db/metadata.json", encoding="utf-8") as f:
        metadata = json.load(f)

    model = SentenceTransformer("all-MiniLM-L6-v2")
    query_vec = model.encode([query]).astype("float32")
    D, I = index.search(query_vec, top_k)

    matched_docs = [metadata[i] for i in I[0]]
    context = "\n".join([f"{doc['text']}（来源：{doc['source']}）" for doc in matched_docs])

    prompt = f"以下是相关信息：\n{context}\n\n请回答用户的问题：{query}"

    response = openai.ChatCompletion.create(
        model="gpt-3.5-turbo",
        messages=[{"role": "user", "content": prompt}]
    )

    return response["choices"][0]["message"]["content"]

# 示例
print(query_faiss("企鹅怎么移动？"))
```

---

## ✅ 二、LanceDB 实现：embedding + 元数据本地管理

### 📦 安装

```bash
pip install lancedb sentence-transformers openai
```

### 🧩 1. 存储数据和元信息

```python
import lancedb
from sentence_transformers import SentenceTransformer

# 同样的文档数据
docs = [
    {"text": "猫是哺乳动物。", "id": "doc1", "source": "百科"},
    {"text": "狗喜欢追球。", "id": "doc2", "source": "宠物指南"},
    {"text": "鲸鱼生活在海里。", "id": "doc3", "source": "海洋资料"},
    {"text": "鸟类会飞。", "id": "doc4", "source": "动物分类"},
    {"text": "鱼在水中游。", "id": "doc5", "source": "水生动物"},
    {"text": "蛇是冷血动物。", "id": "doc6", "source": "爬行动物"},
    {"text": "大象是最大的陆地动物。", "id": "doc7", "source": "哺乳动物资料"},
    {"text": "老虎是猫科动物。", "id": "doc8", "source": "动物百科"},
    {"text": "企鹅不会飞但会游泳。", "id": "doc9", "source": "极地动物"},
    {"text": "袋鼠生活在澳洲。", "id": "doc10", "source": "澳洲自然"}
]

model = SentenceTransformer("all-MiniLM-L6-v2")
for doc in docs:
    doc["vector"] = model.encode(doc["text"]).tolist()

# 建立本地 LanceDB
db = lancedb.connect("lancedb_dir")
db.create_table("animals", data=docs, mode="overwrite")
```

---

### 🧩 2. 查询并构造 prompt 给大模型

```python
def query_lancedb(query, top_k=2):
    table = lancedb.connect("lancedb_dir").open_table("animals")
    vec = model.encode(query).tolist()
    result = table.search(vec).limit(top_k).to_df()

    context = "\n".join(
        f"{row['text']}（来源：{row['source']}）"
        for _, row in result.iterrows()
    )

    prompt = f"以下是相关信息：\n{context}\n\n请回答用户的问题：{query}"

    response = openai.ChatCompletion.create(
        model="gpt-3.5-turbo",
        messages=[{"role": "user", "content": prompt}]
    )

    return response['choices'][0]['message']['content']

# 示例
print(query_lancedb("企鹅怎么移动？"))
```

---

## ✅ 总结：FAISS vs LanceDB

| 功能点   | FAISS              | LanceDB        |
| ----- | ------------------ | -------------- |
| 向量存储  | 支持，高性能             | 支持，嵌套向量字段      |
| 元数据支持 | 需手动并行存储            | 原生支持（字段化）      |
| 本地结构  | `.faiss` + `.json` | 自动生成 DB 文件夹    |
| 查询接口  | 数组索引 -> 查 JSON     | 类似 SQL，支持多字段查询 |
| 推荐用途  | 海量向量数据             | 中小规模+结构化检索     |

---