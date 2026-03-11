---
title: "鐮旂┒浜嗕竴涓嬪悜閲忔暟鎹簱: FAISS鍜孡anceDB"
layout: post
date: 2025-06-26
categories: tech_coding
tags:
    - RAG
---


涓轰簡姣旇緝lancedb鍜孎AISS鍦ㄤ娇鐢ㄦ柟娉曡鍖哄埆, 杩欓噷浣跨敤 **FAISS** 鍜?**LanceDB** 鍒嗗埆瀹炵幇锛?

* 灏?10 鏉℃枃鏈暟鎹敓鎴?embedding
* 娣诲姞鍏冩暟鎹紙渚嬪 ID銆佹潵婧愮瓑锛?
* 瀛樺偍鍒版湰鍦?
* 鏌ヨ鐢ㄦ埛闂锛屽尮閰嶅嚭鏈€鐩稿叧鐨?1-2 鏉℃枃鏈拰鍏冩暟鎹?
* 鍙戠粰澶ц瑷€妯″瀷锛堝 OpenAI GPT锛?

---

## 鉁?涓€銆丗AISS 瀹炵幇锛氬甫鍏冩暟鎹湰鍦板悜閲忔绱?

### 馃捑 鏈湴淇濆瓨缁撴瀯

* `index.faiss`锛欶AISS 鍚戦噺绱㈠紩
* `metadata.json`锛氭枃鏈?+ 鍏冩暟鎹垪琛紝鎸夌储寮曞榻?

### 馃摝 渚濊禆

```bash
pip install faiss-cpu sentence-transformers openai
```

### 馃З 1. 鏋勫缓骞朵繚瀛樼储寮曞拰鍏冩暟鎹?

```python
import faiss
import json
import numpy as np
from sentence_transformers import SentenceTransformer
import os

## 绀轰緥鏂囨湰涓庡厓鏁版嵁
documents = [
    {"text": "鐚槸鍝轰钩鍔ㄧ墿銆?, "id": "doc1", "source": "鐧剧"},
    {"text": "鐙楀枩娆㈣拷鐞冦€?, "id": "doc2", "source": "瀹犵墿鎸囧崡"},
    {"text": "椴搁奔鐢熸椿鍦ㄦ捣閲屻€?, "id": "doc3", "source": "娴锋磱璧勬枡"},
    {"text": "楦熺被浼氶銆?, "id": "doc4", "source": "鍔ㄧ墿鍒嗙被"},
    {"text": "楸煎湪姘翠腑娓搞€?, "id": "doc5", "source": "姘寸敓鍔ㄧ墿"},
    {"text": "铔囨槸鍐疯鍔ㄧ墿銆?, "id": "doc6", "source": "鐖鍔ㄧ墿"},
    {"text": "澶ц薄鏄渶澶х殑闄嗗湴鍔ㄧ墿銆?, "id": "doc7", "source": "鍝轰钩鍔ㄧ墿璧勬枡"},
    {"text": "鑰佽檸鏄尗绉戝姩鐗┿€?, "id": "doc8", "source": "鍔ㄧ墿鐧剧"},
    {"text": "浼侀箙涓嶄細椋炰絾浼氭父娉炽€?, "id": "doc9", "source": "鏋佸湴鍔ㄧ墿"},
    {"text": "琚嬮紶鐢熸椿鍦ㄦ境娲层€?, "id": "doc10", "source": "婢虫床鑷劧"}
]

## 寤虹珛鍚戦噺妯″瀷
model = SentenceTransformer("all-MiniLM-L6-v2")
texts = [doc["text"] for doc in documents]
embeddings = model.encode(texts).astype("float32")

## 淇濆瓨 FAISS 绱㈠紩
index = faiss.IndexFlatL2(embeddings.shape[1])
index.add(embeddings)
os.makedirs("faiss_db", exist_ok=True)
faiss.write_index(index, "faiss_db/index.faiss")

## 淇濆瓨鍏冩暟鎹?
with open("faiss_db/metadata.json", "w", encoding="utf-8") as f:
    json.dump(documents, f, ensure_ascii=False, indent=2)
```

---

### 馃З 2. 鍔犺浇骞跺尮閰嶆煡璇紙杩斿洖鏂囨湰 + 鍏冩暟鎹級

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
    context = "\n".join([f"{doc['text']}锛堟潵婧愶細{doc['source']}锛? for doc in matched_docs])

    prompt = f"浠ヤ笅鏄浉鍏充俊鎭細\n{context}\n\n璇峰洖绛旂敤鎴风殑闂锛歿query}"

    response = openai.ChatCompletion.create(
        model="gpt-3.5-turbo",
        messages=[{"role": "user", "content": prompt}]
    )

    return response["choices"][0]["message"]["content"]

## 绀轰緥
print(query_faiss("浼侀箙鎬庝箞绉诲姩锛?))
```

---

## 鉁?浜屻€丩anceDB 瀹炵幇锛歟mbedding + 鍏冩暟鎹湰鍦扮鐞?

### 馃摝 瀹夎

```bash
pip install lancedb sentence-transformers openai
```

### 馃З 1. 瀛樺偍鏁版嵁鍜屽厓淇℃伅

```python
import lancedb
from sentence_transformers import SentenceTransformer

## 鍚屾牱鐨勬枃妗ｆ暟鎹?
docs = [
    {"text": "鐚槸鍝轰钩鍔ㄧ墿銆?, "id": "doc1", "source": "鐧剧"},
    {"text": "鐙楀枩娆㈣拷鐞冦€?, "id": "doc2", "source": "瀹犵墿鎸囧崡"},
    {"text": "椴搁奔鐢熸椿鍦ㄦ捣閲屻€?, "id": "doc3", "source": "娴锋磱璧勬枡"},
    {"text": "楦熺被浼氶銆?, "id": "doc4", "source": "鍔ㄧ墿鍒嗙被"},
    {"text": "楸煎湪姘翠腑娓搞€?, "id": "doc5", "source": "姘寸敓鍔ㄧ墿"},
    {"text": "铔囨槸鍐疯鍔ㄧ墿銆?, "id": "doc6", "source": "鐖鍔ㄧ墿"},
    {"text": "澶ц薄鏄渶澶х殑闄嗗湴鍔ㄧ墿銆?, "id": "doc7", "source": "鍝轰钩鍔ㄧ墿璧勬枡"},
    {"text": "鑰佽檸鏄尗绉戝姩鐗┿€?, "id": "doc8", "source": "鍔ㄧ墿鐧剧"},
    {"text": "浼侀箙涓嶄細椋炰絾浼氭父娉炽€?, "id": "doc9", "source": "鏋佸湴鍔ㄧ墿"},
    {"text": "琚嬮紶鐢熸椿鍦ㄦ境娲层€?, "id": "doc10", "source": "婢虫床鑷劧"}
]

model = SentenceTransformer("all-MiniLM-L6-v2")
for doc in docs:
    doc["vector"] = model.encode(doc["text"]).tolist()

## 寤虹珛鏈湴 LanceDB
db = lancedb.connect("lancedb_dir")
db.create_table("animals", data=docs, mode="overwrite")
```

---

### 馃З 2. 鏌ヨ骞舵瀯閫?prompt 缁欏ぇ妯″瀷

```python
def query_lancedb(query, top_k=2):
    table = lancedb.connect("lancedb_dir").open_table("animals")
    vec = model.encode(query).tolist()
    result = table.search(vec).limit(top_k).to_df()

    context = "\n".join(
        f"{row['text']}锛堟潵婧愶細{row['source']}锛?
        for _, row in result.iterrows()
    )

    prompt = f"浠ヤ笅鏄浉鍏充俊鎭細\n{context}\n\n璇峰洖绛旂敤鎴风殑闂锛歿query}"

    response = openai.ChatCompletion.create(
        model="gpt-3.5-turbo",
        messages=[{"role": "user", "content": prompt}]
    )

    return response['choices'][0]['message']['content']

## 绀轰緥
print(query_lancedb("浼侀箙鎬庝箞绉诲姩锛?))
```

---

## 鉁?鎬荤粨锛欶AISS vs LanceDB

| 鍔熻兘鐐?  | FAISS              | LanceDB        |
| ----- | ------------------ | -------------- |
| 鍚戦噺瀛樺偍  | 鏀寔锛岄珮鎬ц兘             | 鏀寔锛屽祵濂楀悜閲忓瓧娈?     |
| 鍏冩暟鎹敮鎸?| 闇€鎵嬪姩骞惰瀛樺偍            | 鍘熺敓鏀寔锛堝瓧娈靛寲锛?     |
| 鏈湴缁撴瀯  | `.faiss` + `.json` | 鑷姩鐢熸垚 DB 鏂囦欢澶?   |
| 鏌ヨ鎺ュ彛  | 鏁扮粍绱㈠紩 -> 鏌?JSON     | 绫讳技 SQL锛屾敮鎸佸瀛楁鏌ヨ |
| 鎺ㄨ崘鐢ㄩ€? | 娴烽噺鍚戦噺鏁版嵁             | 涓皬瑙勬ā+缁撴瀯鍖栨绱?    |

---
