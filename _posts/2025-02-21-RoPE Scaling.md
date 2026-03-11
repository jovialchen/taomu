---
title: "RoPE Scaling鏄粈涔?"
layout: post
date: 2025-02-21
categories: tech_coding
tags:
    - Transformers
---

鏈€杩戜竴鐩村湪瀛︿範鑷劧璇█澶勭悊鍜屽ぇ璇█妯″瀷鐨勪竴浜涘熀鏈蹇? 杩欎簺鍐呭鏄湪ChatGPT, Deepseek绛夊ぇ璇█妯″瀷鐨勫府鍔╀笅璁板綍鐨勩€?




### 馃攳 **浠€涔堟槸 RoPE锛圧otary Positional Embedding锛夛紵**

**RoPE** 鍏ㄧО鏄?**Rotary Positional Embedding**锛屾槸涓€绉嶇浉瀵逛綅缃紪鐮佹柟娉曪紝鐢ㄤ簬鍦?Transformer 鏋舵瀯涓负搴忓垪涓殑 token 鎻愪緵浣嶇疆淇℃伅銆傚畠棣栨鍑虹幇鍦?**Su et al., 2021 鐨勮鏂囥€奟oFormer: Enhanced Transformer with Rotary Position Embedding銆?* 涓€?

---

### 馃幆 **涓轰粈涔堥渶瑕?RoPE锛?*

浼犵粺鐨?Transformer 浣跨敤 **缁濆浣嶇疆缂栫爜锛圓bsolute Positional Encoding锛?*锛屾瘮濡傦細
- 浣跨敤姝ｅ鸡鍜屼綑寮﹀嚱鏁扮敓鎴愮殑鍥哄畾鍚戦噺锛堝鍦ㄥ師濮?Transformer 璁烘枃涓級銆?
- 鐩存帴瀛︿範鐨勪綅缃悜閲忥紙Learned Positional Embedding锛夈€?

杩欎簺鏂规硶鏈変袱涓富瑕侀棶棰橈細  
1. **鏃犳硶娉涘寲鍒版洿闀垮簭鍒?*锛氫竴鏃﹁缁冩椂鐨勪綅缃檺鍒惰鎵撶牬锛堜緥濡傝缁冩椂鍙湅鍒?512 tokens锛夛紝妯″瀷灏辨棤娉曞鐞嗘洿闀跨殑搴忓垪銆? 
2. **鏃犳硶鎹曟崏鐩稿浣嶇疆淇℃伅**锛氬湪寰堝鎯呭喌涓嬶紝**"鍓嶅悗璺濈"** 姣?*"缁濆浣嶇疆"**鏇撮噸瑕併€? 

---

### 鈿欙笍 **RoPE 鐨勬牳蹇冩€濇兂**

RoPE 鐨勫叧閿湪浜庡皢浣嶇疆淇℃伅閫氳繃**鏃嬭浆鐭╅樀**宓屽叆鍒?token 鐨勮〃绀轰腑銆? 
- 瀹冪敤涓€涓?*鏃嬭浆鍙樻崲**灏嗕綅缃俊鎭敞鍏?token 鐨勫祵鍏ュ悜閲忥紙embedding锛夐噷銆? 
- 杩欑鏃嬭浆鏄牴鎹?token 鐨勪綅缃姩鎬佸彉鍖栫殑锛屾墍浠?RoPE 鏈川涓婃槸涓€绉?**鐩稿浣嶇疆缂栫爜**锛岃€屼笉鏄紶缁熺殑缁濆浣嶇疆缂栫爜銆?

---

### 馃搻 **RoPE 鐨勬暟瀛﹀師鐞嗭紙绠€鍖栫増锛?*

1. **缁欏畾涓€涓?token 鐨勫祵鍏ュ悜閲?* \( x \in \mathbb{R}^d \)锛屽皢瀹冩媶鍒嗘垚涓や袱涓€缁勶紙鍗虫瘡涓や釜缁村害涓轰竴瀵癸級銆? 
2. 瀵逛簬姣忎竴瀵圭淮搴?\( (x_{2i}, x_{2i+1}) \)锛屽簲鐢ㄦ棆杞彉鎹細  

   \[
   \begin{bmatrix}
   x'_{2i} \\
   x'_{2i+1}
   \end{bmatrix}
   =
   \begin{bmatrix}
   \cos(\theta) & -\sin(\theta) \\
   \sin(\theta) & \cos(\theta)
   \end{bmatrix}
   \cdot
   \begin{bmatrix}
   x_{2i} \\
   x_{2i+1}
   \end{bmatrix}
   \]

3. **\(\theta\) 鏄熀浜?token 浣嶇疆鍜岀淮搴﹁瀹氱殑**锛岄€氬父鏄細  
   \[
   \theta = pos \cdot \frac{1}{10000^{2i/d}}
   \]
   杩欓噷锛孿( pos \) 鏄?token 鐨勪綅缃紝\( i \) 鏄淮搴︾储寮曪紝\( d \) 鏄祵鍏ョ淮搴︺€? 

杩欎釜鏃嬭浆鎿嶄綔鍙互鐪嬩綔鍦?*澶嶆暟骞抽潰涓婃棆杞悜閲?*锛屼粠鑰屽湪鍚戦噺绌洪棿涓敞鍏ヤ綅缃俊鎭€? 

---

### 馃殌 **RoPE 鐨勪紭鍔?*

1. 鉁?**鏀寔鏇撮暱鐨勫簭鍒?*锛氱浉瀵逛綅缃俊鎭彲浠ュ湪鏇撮暱鐨勬枃鏈腑娉涘寲锛岃秴瓒婁簡璁粌鏃剁殑鏈€澶?context length銆? 
2. 鉁?**鏇村皯鐨勫唴瀛樻秷鑰?*锛氱浉姣斾簬浼犵粺鐨勭粷瀵逛綅缃祵鍏ワ紝RoPE 涓嶉渶瑕佹樉寮忓瓨鍌ㄤ綅缃悜閲忋€? 
3. 鉁?**鏇村ソ鐨勬帹鐞嗘€ц兘**锛氱壒鍒€傚悎澶勭悊闀挎枃妗ｏ紝甯哥敤浜庡ぇ瑙勬ā璇█妯″瀷锛堝 LLaMA銆丆hatGLM 绛夛級銆? 

---

### 馃洜锔?**RoPE 鍦ㄤ唬鐮佷腑鐨勫疄鐜帮紙PyTorch 绀轰緥锛?*

```python
import torch
import math

def apply_rope(x, seq_len):
    d = x.shape[-1]
    half_d = d // 2
    theta = 10000 ** (-torch.arange(0, half_d, dtype=torch.float32) / half_d)
    pos = torch.arange(seq_len, dtype=torch.float32).unsqueeze(1)

    # 璁＄畻鏃嬭浆瑙掑害
    angles = pos * theta.unsqueeze(0)

    # 鏋勯€犳棆杞煩闃?
    cos = torch.cos(angles).repeat(1, 2)
    sin = torch.sin(angles).repeat(1, 2)

    x1 = x * cos + torch.cat([-x[..., half_d:], x[..., :half_d]], dim=-1) * sin
    return x1

## 鍋囪杈撳叆寮犻噺鏄?(batch_size, seq_len, embedding_dim)
x = torch.randn(1, 10, 64)  # 1 涓?batch, 10 涓?token, 姣忎釜宓屽叆鍚戦噺 64 缁?
output = apply_rope(x, seq_len=10)
```

---

### 馃挕 **RoPE 鍦ㄥ疄闄呬腑鐨勫簲鐢?*

- 鉁?GPT-3.5銆丩LaMA銆丆hatGLM 绛夊ぇ鍨嬭瑷€妯″瀷閮介噰鐢ㄤ簡 RoPE銆? 
- 鉁?瀵逛簬闇€瑕佸鐞嗛暱鏂囨湰鐨勪换鍔★紙渚嬪浣犵殑 RAG 椤圭洰锛夛紝浣跨敤 RoPE 鍙互澶у箙鎻愬崌涓婁笅鏂囧鐞嗚兘鍔涖€? 

---


