---
layout: post
date: 2026-02-27
title: "Deep Agents 涓婁笅鏂囬暱搴﹀鐞嗘満鍒?
categories: tech_coding
tags:
  - DeepAgents
  - AIAgent
  - LLM
  - ContextWindow
  - Summarization
---
## Deep Agents 涓婁笅鏂囬暱搴﹀鐞嗘満鍒?
## 姒傝堪

Deep Agents 閫氳繃澶氬眰绛栫暐鏉ュ鐞嗕笂涓嬫枃闀垮害瓒呴檺闂锛屾牳蹇冩満鍒跺寘鎷細

1. **瀵硅瘽鎽樿锛圫ummarization锛?* 鈥?褰撴秷鎭巻鍙叉帴杩戞ā鍨嬩笂涓嬫枃绐楀彛涓婇檺鏃讹紝鑷姩灏嗘棫娑堟伅鍘嬬缉涓烘憳瑕?2. **宸ュ叿鍙傛暟鎴柇锛圱ool Argument Truncation锛?* 鈥?鎴柇鍘嗗彶娑堟伅涓繃澶х殑宸ュ叿璋冪敤鍙傛暟锛堝 `write_file`銆乣edit_file`锛?3. **澶у瀷宸ュ叿缁撴灉椹遍€愶紙Large Tool Result Eviction锛?* 鈥?灏嗚秴澶х殑宸ュ叿杩斿洖缁撴灉鍐欏叆鏂囦欢绯荤粺锛岀敤寮曠敤鏇挎崲鍘熷鍐呭
4. **ContextOverflowError 鍏滃簳** 鈥?褰撴ā鍨?API 杩斿洖涓婁笅鏂囨孩鍑洪敊璇椂锛岃嚜鍔ㄨЕ鍙戞憳瑕佷綔涓洪檷绾х瓥鐣?
杩欎簺鏈哄埗鐢?`SummarizationMiddleware` 鍜?`FilesystemMiddleware` 鍗忓悓瀹屾垚锛屽湪 `create_deep_agent()` 涓粯璁ゅ惎鐢ㄣ€?
---

## 1. 瀵硅瘽鎽樿锛圫ummarizationMiddleware锛?
### 宸ヤ綔鍘熺悊

`SummarizationMiddleware` 鍦ㄦ瘡娆℃ā鍨嬭皟鐢ㄥ墠妫€鏌ュ綋鍓嶆秷鎭垪琛ㄧ殑 token 鏁伴噺銆傚綋杈惧埌瑙﹀彂闃堝€兼椂锛?
1. 鏍规嵁 `keep` 绛栫暐纭畾淇濈暀澶氬皯鏈€杩戠殑娑堟伅
2. 灏嗚绉婚櫎鐨勬棫娑堟伅鎸佷箙鍖栧埌鍚庣瀛樺偍锛圡arkdown 鏂囦欢锛?3. 鐢ㄤ竴涓緝灏忕殑 LLM 璋冪敤鐢熸垚鏃ф秷鎭殑鎽樿
4. 鐢ㄦ憳瑕佹秷鎭?+ 淇濈暀鐨勬渶杩戞秷鎭浛鎹㈠師濮嬫秷鎭垪琛?
```text
[绯荤粺娑堟伅] [鏃ф秷鎭?..N] [鏂版秷鎭?..M]
                鈫?鎽樿瑙﹀彂
[绯荤粺娑堟伅] [鎽樿HumanMessage] [鏂版秷鎭?..M]
```

### 瑙﹀彂闃堝€硷紙trigger锛?
`trigger` 鍙傛暟鍐冲畾浣曟椂鍚姩鎽樿锛屾敮鎸佷笁绉嶆ā寮忥細

| 妯″紡           | 鏍煎紡              | 绀轰緥                  | 璇存槑                                              |
| -------------- | ----------------- | --------------------- | ------------------------------------------------- |
| token 鏁?      | `("tokens", N)`   | `("tokens", 170000)` | 娑堟伅鎬?token 鏁拌揪鍒?N 鏃惰Е鍙?                     |
| 娑堟伅鏁?        | `("messages", N)` | `("messages", 50)`   | 娑堟伅鏉℃暟杈惧埌 N 鏃惰Е鍙?                            |
| 涓婁笅鏂囩獥鍙ｆ瘮渚?| `("fraction", F)` | `("fraction", 0.85)` | 鍗犳ā鍨?`max_input_tokens` 鐨勬瘮渚嬭揪鍒?F 鏃惰Е鍙?    |

### 淇濈暀绛栫暐锛坘eep锛?
`keep` 鍙傛暟鍐冲畾鎽樿鍚庝繚鐣欏灏戞渶杩戠殑娑堟伅锛?
| 妯″紡           | 鏍煎紡              | 绀轰緥                  | 璇存槑                        |
| -------------- | ----------------- | --------------------- | --------------------------- |
| 娑堟伅鏁?        | `("messages", N)` | `("messages", 6)`     | 淇濈暀鏈€杩?N 鏉℃秷鎭?          |
| token 鏁?      | `("tokens", N)`   | `("tokens", 5000)`   | 淇濈暀鏈€杩?N 涓?token 鐨勬秷鎭? |
| 涓婁笅鏂囩獥鍙ｆ瘮渚?| `("fraction", F)` | `("fraction", 0.10)` | 淇濈暀鍗犵獥鍙?F 姣斾緥鐨勬渶杩戞秷鎭?|

### 榛樿鍊肩殑鑷姩璁＄畻

`create_deep_agent()` 浼氭牴鎹ā鍨嬬殑 profile锛堟槸鍚︽湁 `max_input_tokens`锛夎嚜鍔ㄩ€夋嫨鍚堥€傜殑榛樿鍊硷細

**鎯呭喌 1锛氭ā鍨嬫湁 `max_input_tokens` profile锛圕laude銆丟PT-4 绛変富娴佹ā鍨嬶級**

| 鍙傛暟                              | 榛樿鍊?                | 鍚箟                              |
| --------------------------------- | ---------------------- | --------------------------------- |
| `trigger`                         | `("fraction", 0.85)`  | 涓婁笅鏂囩獥鍙ｇ敤鍒?85% 鏃惰Е鍙戞憳瑕?    |
| `keep`                            | `("fraction", 0.10)`  | 鎽樿鍚庝繚鐣欐渶杩?10% 鐨勬秷鎭?        |
| `truncate_args_settings.trigger`  | `("fraction", 0.85)`  | 85% 鏃跺紑濮嬫埅鏂棫宸ュ叿鍙傛暟          |
| `truncate_args_settings.keep`     | `("fraction", 0.10)`  | 鏈€杩?10% 鐨勬秷鎭笉鎴柇             |

**鎯呭喌 2锛氭ā鍨嬫病鏈?profile 淇℃伅**

| 鍙傛暟                              | 榛樿鍊?                | 鍚箟                              |
| --------------------------------- | ---------------------- | --------------------------------- |
| `trigger`                         | `("tokens", 170000)`  | 170k tokens 鏃惰Е鍙戞憳瑕?           |
| `keep`                            | `("messages", 6)`     | 鎽樿鍚庝繚鐣欐渶杩?6 鏉℃秷鎭?          |
| `truncate_args_settings.trigger`  | `("messages", 20)`    | 20 鏉℃秷鎭悗寮€濮嬫埅鏂棫宸ュ叿鍙傛暟     |
| `truncate_args_settings.keep`     | `("messages", 20)`    | 鏈€杩?20 鏉℃秷鎭笉鎴柇              |

**宸ュ叿鍙傛暟鎴柇鐨勫浐瀹氶粯璁ゅ€硷紙涓ょ鎯呭喌閫氱敤锛夛細**

| 鍙傛暟               | 榛樿鍊?                      | 鍚箟                                  |
| ------------------ | ---------------------------- | ------------------------------------- |
| `max_length`       | `2000`                       | 宸ュ叿鍙傛暟瀛楃涓茶秴杩?2000 瀛楃鏃舵埅鏂?   |
| `truncation_text`  | `"...(argument truncated)"`  | 鎴柇鍚庣殑鏇挎崲鏂囨湰                      |

> 娉ㄦ剰锛歚create_deep_agent()` 鍐呴儴灏?`trim_tokens_to_summarize` 璁句负 `None`锛堜笉闄愬埗鍙戦€佺粰鎽樿 LLM 鐨?token 鏁帮級锛岃€?`SummarizationMiddleware` 鑷韩鐨勯粯璁ゅ€兼槸 4000銆?
### 甯歌妯″瀷鐨勫疄闄呴粯璁ゅ€?
鐢变簬 `fraction` 妯″紡涓嬬殑瀹為檯 token 闃堝€煎彇鍐充簬妯″瀷鐨?`max_input_tokens`锛屼笉鍚屾ā鍨嬬殑瑙﹀彂鐐瑰樊寮傚緢澶с€備互涓嬫槸鍑犱釜甯歌妯″瀷鐨勫疄闄呰绠楃粨鏋滐細

| 妯″瀷                        | 涓婁笅鏂囩獥鍙ｏ紙鏍囩О锛?| 瀹為檯杈撳叆涓婇檺 | 鎽樿瑙﹀彂 (85%)          | 淇濈暀娑堟伅 (10%)      |
| --------------------------- | ------------------ | ------------ | ----------------------- | ------------------- |
| GPT-5.2 / GPT-5.2-thinking | 400K               | ~272K        | ~340,000 tokens         | ~40,000 tokens      |
| GPT-5                       | 400K               | ~272K        | ~340,000 tokens         | ~40,000 tokens      |
| Claude Sonnet 4.5           | 200K               | ~200K        | ~170,000 tokens         | ~20,000 tokens      |
| GPT-4o                      | 128K               | ~128K        | ~108,800 tokens         | ~12,800 tokens      |
| 鏃?profile 鐨勬ā鍨?          | 鈥?                 | 鈥?           | 170,000 tokens (鍥哄畾)   | 6 鏉℃秷鎭?(鍥哄畾)     |

> 鈿狅笍 GPT-5/5.2 绯诲垪鐨?400K 鏄緭鍏?杈撳嚭鐨勬€荤獥鍙ｏ紝瀹為檯鏈€澶ц緭鍏ョ害 272K tokens锛?00K - 128K max output锛夈€備絾 langchain profile 涓?`max_input_tokens` 璁板綍鐨勬槸 400K锛屽鑷?85% 瑙﹀彂闃堝€硷紙~340K锛夎繙瓒呯湡瀹炶緭鍏ヤ笂闄愩€傝瑙佷笅鏂?GPT-5/5.2 鐨?400K 绐楀彛闄烽槺"銆?
#### GPT-5.2 浣跨敤绀轰緥

GPT-5.2 浜?2025 骞?12 鏈?11 鏃ュ彂甯冿紝鎻愪緵涓変釜鍙樹綋锛圼鏉ユ簮](https://llm-stats.com/blog/research/gpt-5-2-launch)锛夛細

- **Instant** 鈥?浣庡欢杩燂紝閫傚悎楂樺悶鍚愰噺浠诲姟
- **Thinking** 鈥?鍙皟鎺ㄧ悊娣卞害锛圠ight/Medium/Heavy锛夛紝閫傚悎澶у鏁扮敓浜у満鏅?- **Pro** 鈥?鏈€澶ф帹鐞嗙簿搴︼紝閫傚悎绉戠爺鍜屽鏉傜紪鐮?
涓変釜鍙樹綋鍏变韩 400K 涓婁笅鏂囩獥鍙ｅ拰 128K 鏈€澶ц緭鍑恒€傚湪 Deep Agents 涓娇鐢細

```python
from deepagents import create_deep_agent

## 榛樿閰嶇疆浼氳嚜鍔ㄩ€傞厤 400K 绐楀彛锛屾棤闇€鎵嬪姩璁剧疆 trigger/keep
agent = create_deep_agent(model="openai:gpt-5.2")

## 浣跨敤 Thinking 鍙樹綋锛堟帹鑽愮敤浜?agent 鍦烘櫙锛?agent = create_deep_agent(model="openai:gpt-5.2-thinking")

## 浣跨敤 Pro 鍙樹綋锛堝鏉傛帹鐞嗕换鍔★級
agent = create_deep_agent(model="openai:gpt-5.2-pro")
```

GPT-5.2 鐨勫畾浠蜂负杈撳叆 $1.75/1M tokens銆佽緭鍑?$14.00/1M tokens銆傜敱浜庢憳瑕佷細浜х敓棰濆鐨?LLM 璋冪敤锛堥粯璁や娇鐢ㄥ悓涓€妯″瀷锛夛紝400K 绐楀彛涓嬫憳瑕佽Е鍙戦鐜囨洿浣庯紝闂存帴闄嶄綆浜嗘憳瑕佹垚鏈€傚鏋滃笇鏈涜繘涓€姝ヨ妭鐪佹憳瑕佸紑閿€锛屽彲浠ヨ嚜瀹氫箟涓€涓娇鐢ㄦ洿渚垮疁妯″瀷鐨?`SummarizationMiddleware`銆?
> 鈿狅笍 GPT-5.2 鐨勯粯璁?fraction-based 瑙﹀彂闃堝€硷紙340K锛夎秴杩囦簡瀹為檯杈撳叆涓婇檺锛?72K锛夛紝鍙兘瀵艰嚧涓婁笅鏂囨孩鍑恒€傚缓璁墜鍔ㄨ缃?`trigger=("tokens", 200000)`锛岃瑙佷笅鏂?GPT-5/5.2 鐨?400K 绐楀彛闄烽槺"銆?
### 妯″瀷鍚嶇О蹇呴』绮剧‘鍖归厤

langchain 鐨?profile 鏌ユ壘鏄簿纭尮閰嶏紙exact match锛夛紝涓嶅瓨鍦ㄦā绯婂尮閰嶆垨鍓嶇紑鍖归厤銆?
姣忎釜 langchain provider 鍖咃紙濡?`langchain-openai`锛夊唴閮ㄧ淮鎶や竴涓?`_PROFILES` 瀛楀吀锛宬ey 鏄ā鍨嬬殑绮剧‘鍚嶇О锛堟暟鎹潵婧愪簬 [models.dev](https://models.dev)锛夈€俙model.profile` 鐨勮В鏋愯繃绋嬪氨鏄竴娆℃櫘閫氱殑瀛楀吀鏌ユ壘锛屾壘涓嶅埌灏辫繑鍥?`None`銆?
杩欐剰鍛崇潃妯″瀷鍚嶇О涓殑姣忎釜瀛楃閮藉緢閲嶈銆備互 GPT-5.2 涓轰緥锛?
```python
## 姝ｇ‘ 鈥?绮剧‘鍖归厤 _PROFILES 涓殑 "gpt-5.2"锛岃幏寰?400K 绐楀彛鐨?fraction-based 閰嶇疆
agent = create_deep_agent(model="openai:gpt-5.2")

## 閿欒 鈥?"gpt-5-2" 鍦?_PROFILES 涓笉瀛樺湪锛宲rofile 涓?None
agent = create_deep_agent(model="openai:gpt-5-2")
```

褰?`model.profile` 涓?`None` 鏃讹紝`_compute_summarization_defaults()` 涓殑 `has_profile` 妫€鏌ヤ細澶辫触锛?
```python
has_profile = (
    model.profile is not None          # 鈫?None锛岀洿鎺?False
    and isinstance(model.profile, dict)
    and "max_input_tokens" in model.profile
    and isinstance(model.profile["max_input_tokens"], int)
)
```

缁撴灉鏄郴缁熶細闈欓粯鍥為€€鍒?鏃?profile 妯″瀷"鐨勫浐瀹氶粯璁ゅ€硷紝鑰屼笉鏄綘鏈熸湜鐨?fraction-based 閰嶇疆锛?
| 閰嶇疆椤? | 鏈熸湜鍊硷紙`gpt-5.2`锛?               | 瀹為檯鍊硷紙`gpt-5-2`锛?           |
| ------- | ----------------------------------- | ------------------------------- |
| trigger | `("fraction", 0.85)` 鈫?~340K tokens | `("tokens", 170000)` 鈥?鍥哄畾鍊?|
| keep    | `("fraction", 0.10)` 鈫?~40K tokens  | `("messages", 6)` 鈥?鍥哄畾鍊?   |

> 杩欎釜闂涓嶄細鎶ラ敊锛屽彧浼氶潤榛橀檷绾с€傚鏋滀綘鍙戠幇 agent 鍦ㄨ繙鏈揪鍒版ā鍨嬩笂涓嬫枃绐楀彛涓婇檺鏃跺氨棰戠箒瑙﹀彂鎽樿锛岄鍏堟鏌ユā鍨嬪悕绉版槸鍚︿笌 OpenAI API 鏂囨。涓殑鍚嶇О瀹屽叏涓€鑷淬€?
### GPT-5/5.2 鐨?400K 绐楀彛闄烽槺

GPT-5/5.2 绯诲垪瀛樺湪涓€涓鏄撹蹇界暐鐨勫叧閿棶棰橈細400K 涓婁笅鏂囩獥鍙ｆ槸杈撳叆+杈撳嚭鐨勬€婚噺锛岃€岄潪绾緭鍏ヤ笂闄愩€侽penAI 涓烘渶澶ц緭鍑洪鐣欎簡 128K tokens锛屽洜姝ゅ疄闄呮渶澶ц緭鍏ョ害涓?272K tokens锛圼鏉ユ簮锛歄penAI 绀惧尯璁ㄨ](https://community.openai.com/t/huge-gpt-5-documentation-gap-flaw-input-limit-is-not-the-advertised-context-window/1344734)锛夈€?
褰撹緭鍏ヨ秴杩?~272K tokens 鏃讹紝OpenAI API 浼氳繑鍥炵‖閿欒锛?
```text
Input tokens exceed the configured limit of 272,000 tokens.
Your messages resulted in XXX tokens.
```

杩欎笌鍏朵粬妯″瀷鐨勮涓轰笉鍚屻€備緥濡?Claude Sonnet 4.5 鐨?200K 绐楀彛灏辨槸绾緭鍏ヤ笂闄愶紝GPT-4o 鐨?128K 涔熸槸銆傚彧鏈?GPT-5 绯诲垪閲囩敤浜嗚緭鍏?杈撳嚭鍏变韩绐楀彛鐨勮璁°€?
#### 涓轰粈涔堥粯璁ら厤缃棤娉曚繚鎶や綘

鍗充娇妯″瀷鍚嶇О姝ｇ‘鍖归厤鍒?`gpt-5.2`锛坧rofile 涓?`max_input_tokens: 400000`锛夛紝榛樿鐨?fraction-based 閰嶇疆涔熷瓨鍦ㄩ棶棰橈細

| 閰嶇疆椤? | 榛樿鍊?                             | 瀹為檯鏁堟灉                                |
| ------- | ----------------------------------- | --------------------------------------- |
| trigger | `("fraction", 0.85)` 鈫?~340K tokens | 杩滆秴 272K 鐪熷疄杈撳叆涓婇檺锛屾憳瑕佹案杩滀笉浼氳Е鍙?|
| keep    | `("fraction", 0.10)` 鈫?~40K tokens  | 姝ｅ父                                    |

鎽樿瑙﹀彂闃堝€?340K > 瀹為檯杈撳叆涓婇檺 272K锛屾剰鍛崇潃鍦ㄦ甯告儏鍐典笅鎽樿鏈哄埗鏍规湰鏉ヤ笉鍙婅Е鍙戯紝绯荤粺浼氱洿鎺ユ挒涓?API 鐨勭‖闄愬埗銆傝櫧鐒?`ContextOverflowError` 鍏滃簳鏈哄埗浼氬皾璇曢檷绾ф憳瑕侊紝浣嗘鏃跺凡缁忓彂鐢熶簡涓€娆″け璐ョ殑 API 璋冪敤銆?
#### 濡傛灉妯″瀷鍚嶇О杩樺啓閿欎簡

鎯呭喌浼氭洿澶嶆潅銆備互 `"gpt-5-2"`锛堣繛瀛楃锛変负渚嬶細

1. langchain profile 鏌ユ壘澶辫触锛宍model.profile` 涓?`None`
2. 鎽樿 trigger 鍥為€€鍒?`("tokens", 170000)` 鈥?鍥哄畾 170K
3. 浣?`count_tokens_approximately` 浣跨敤 4 瀛楃 鈮?1 token 鐨勭矖鐣ヤ及绠?4. 濡傛灉瀵硅瘽鍖呭惈澶ч噺浠ｇ爜銆丣SON銆佸伐鍏疯皟鐢ㄧ粨鏋滐紙瀛楃/token 姣旂巼鍙兘鍙湁 1.5~2:1锛夛紝瀹為檯 token 鏁颁細杩滈珮浜庝及绠楀€?5. 浼扮畻鍊煎埌 170K 鏃讹紝瀹為檯 token 鏁板彲鑳藉凡缁忔帴杩戞垨瓒呰繃 272K

杩欏氨鏄负浠€涔堜綘鍙兘鍦?~270K 鏃堕亣鍒板穿婧冣€斺€旇繎浼艰鏁板櫒涓ラ噸浣庝及浜嗗疄闄?token 鏁帮紝鎽樿铏界劧"瑙﹀彂"浜嗭紝浣嗕负鏃跺凡鏅氥€?
#### 鎺ㄨ崘閰嶇疆

瀵逛簬 GPT-5/5.2 绯诲垪锛屽缓璁墜鍔ㄨ缃竴涓畨鍏ㄧ殑瑙﹀彂闃堝€硷紝鑰屼笉鏄緷璧栭粯璁ょ殑 fraction-based 閰嶇疆锛?
```python
from deepagents import create_deep_agent
from deepagents.middleware.summarization import SummarizationMiddleware
from deepagents.backends import StateBackend

## 鏂瑰紡 1锛氫娇鐢ㄥ浐瀹?token 鏁拌Е鍙戯紝鐣欏嚭瀹夊叏浣欓噺
custom_summarization = SummarizationMiddleware(
    model="openai:gpt-5.2",
    backend=StateBackend,
    trigger=("tokens", 200000),   # 200K锛岃繙浣庝簬 272K 纭檺鍒?    keep=("tokens", 30000),       # 淇濈暀 30K tokens 鐨勬渶杩戞秷鎭?)

agent = create_deep_agent(
    model="openai:gpt-5.2",
    middleware=[custom_summarization],
)
```

閫夋嫨 200K 浣滀负瑙﹀彂鐐圭殑鐞嗙敱锛?
- 272K 鏄‖闄愬埗锛岄渶瑕佺暀鍑轰綑閲忕粰绯荤粺娑堟伅銆佸伐鍏峰畾涔夌瓑
- `count_tokens_approximately` 鐨勮宸彲鑳介珮杈?30-50%锛堝挨鍏舵槸浠ｇ爜瀵嗛泦鐨勫璇濓級
- 200K 鍦ㄦ渶鍧忔儏鍐典笅锛?:1 瀛楃/token 姣旂巼锛夊搴旂害 260K 瀹為檯 tokens锛屼粛鍦ㄥ畨鍏ㄨ寖鍥村唴
- 鍗充娇浼扮畻涓嶅噯锛宍ContextOverflowError` 鍏滃簳鏈哄埗涔熻兘鍦?272K 澶勬崟鑾峰苟闄嶇骇

### 鍘嗗彶娑堟伅鎸佷箙鍖?
琚憳瑕佺殑娑堟伅涓嶄細涓㈠け銆傚畠浠細琚啓鍏ュ悗绔瓨鍌ㄧ殑 Markdown 鏂囦欢涓細

- 璺緞鏍煎紡锛歚/conversation_history/{thread_id}.md`
- 姣忔鎽樿杩藉姞涓€涓甫鏃堕棿鎴崇殑鏂版钀?- 鎽樿娑堟伅涓寘鍚枃浠惰矾寰勫紩鐢紝agent 鍙互鍦ㄩ渶瑕佹椂鍥炴函鏌ョ湅

```text
You are in the middle of a conversation that has been summarized.

The full conversation history has been saved to /conversation_history/abc123.md
should you need to refer back to it for details.

A condensed summary follows:

<summary>
...鎽樿鍐呭...
</summary>
```

### 浣跨敤绀轰緥

```python
from deepagents import create_deep_agent
from deepagents.middleware.summarization import SummarizationMiddleware
from deepagents.backends import FilesystemBackend

## 鏂瑰紡 1锛氫娇鐢?create_deep_agent 鐨勯粯璁ら厤缃紙鎺ㄨ崘锛?agent = create_deep_agent(model="anthropic:claude-sonnet-4-5-20250929")

## 鏂瑰紡 2锛氳嚜瀹氫箟鎽樿鍙傛暟
middleware = SummarizationMiddleware(
    model="openai:gpt-4o-mini",  # 鐢ㄤ簬鐢熸垚鎽樿鐨勬ā鍨嬶紙鍙互鐢ㄤ究瀹滅殑妯″瀷锛?    backend=FilesystemBackend(root_dir="/data"),
    trigger=("fraction", 0.75),  # 75% 鏃惰Е鍙?    keep=("fraction", 0.15),     # 淇濈暀 15%
)

agent = create_deep_agent(
    model="anthropic:claude-sonnet-4-5-20250929",
    middleware=[middleware],
)
```

---

## 2. 宸ュ叿鍙傛暟鎴柇锛圱runcate Args锛?
### 宸ヤ綔鍘熺悊

闀挎椂闂磋繍琛岀殑 agent 浼氳瘽涓紝鍘嗗彶娑堟伅閲岀殑 `write_file` 鍜?`edit_file` 宸ュ叿璋冪敤鍙兘鍖呭惈澶ч噺鏂囦欢鍐呭浣滀负鍙傛暟銆傝繖浜涘弬鏁板湪鍚庣画瀵硅瘽涓凡缁忔病鏈変环鍊硷紝浣嗕粛鍗犵敤涓婁笅鏂囩┖闂淬€?
宸ュ叿鍙傛暟鎴柇鏈哄埗浼氾細

1. 鍦ㄦ憳瑕佹鏌ヤ箣鍓嶏紝鍏堟壂鎻忓巻鍙叉秷鎭腑鐨?`AIMessage.tool_calls`
2. 瀵?`write_file` 鍜?`edit_file` 鐨勫弬鏁帮紝濡傛灉瀛楃涓查暱搴﹁秴杩?`max_length`锛堥粯璁?2000锛夛紝鍒欐埅鏂?3. 鍙埅鏂?鏃?娑堟伅锛堢敱 `keep` 绛栫暐淇濇姢鐨勬渶杩戞秷鎭笉鍙楀奖鍝嶏級

```python
## 鎴柇鍓?{"name": "write_file", "args": {"file_path": "/app.py", "content": "...10000瀛楃..."}}

## 鎴柇鍚?{"name": "write_file", "args": {"file_path": "/app.py", "content": "...鍓?0瀛楃...(argument truncated)"}}
```

### 閰嶇疆

閫氳繃 `truncate_args_settings` 鍙傛暟閰嶇疆锛?
```python
SummarizationMiddleware(
    model="gpt-4o-mini",
    backend=backend,
    truncate_args_settings={
        "trigger": ("messages", 50),     # 50 鏉℃秷鎭悗寮€濮嬫埅鏂?        "keep": ("messages", 20),        # 鏈€杩?20 鏉′笉鎴柇
        "max_length": 2000,              # 鍙傛暟瓒呰繃 2000 瀛楃鏃舵埅鏂?        "truncation_text": "...(argument truncated)",
    },
)
```

---

## 3. 澶у瀷宸ュ叿缁撴灉椹遍€愶紙FilesystemMiddleware锛?
### 宸ヤ綔鍘熺悊

褰撳伐鍏锋墽琛岃繑鍥炵殑缁撴灉杩囧ぇ鏃讹紙濡傝鍙栧ぇ鏂囦欢銆佹墽琛岃繑鍥炲ぇ閲忚緭鍑虹殑鍛戒护锛夛紝`FilesystemMiddleware` 浼氳嚜鍔ㄥ皢缁撴灉鍐欏叆鏂囦欢绯荤粺锛屽苟鐢ㄤ竴鏉＄畝鐭殑寮曠敤娑堟伅鏇挎崲鍘熷鍐呭銆?
鍒ゆ柇鏍囧噯锛歚len(content_str) > NUM_CHARS_PER_TOKEN * tool_token_limit_before_evict`

- `NUM_CHARS_PER_TOKEN = 4`锛堜繚瀹堜及璁★級
- `tool_token_limit_before_evict = 20000`锛堥粯璁ゅ€硷級
- 鍗冲唴瀹硅秴杩囩害 80,000 瀛楃鏃惰Е鍙戦┍閫?
椹遍€愬悗鐨勬秷鎭牸寮忥細

```text
Tool result too large to display inline.
The full result has been saved to /large_tool_results/{tool_call_id}.
Use read_file to view the content.
```

### 鎺掗櫎鐨勫伐鍏?
浠ヤ笅宸ュ叿涓嶄細瑙﹀彂椹遍€愶紙鍥犱负瀹冧滑鐨勮繑鍥炲€奸€氬父寰堝皬鎴栧凡缁忔湁鑷繁鐨勬埅鏂€昏緫锛夛細

- `ls`銆乣glob`銆乣grep` 鈥?鏂囦欢鍒楄〃/鎼滅储宸ュ叿
- `write_file`銆乣edit_file` 鈥?鍐欏叆纭娑堟伅
- `write_todos` 鈥?todo 鍒楄〃鎿嶄綔

### 閰嶇疆

```python
from deepagents.middleware.filesystem import FilesystemMiddleware

## 鑷畾涔夐┍閫愰槇鍊?middleware = FilesystemMiddleware(
    backend=backend,
    tool_token_limit_before_evict=10000,  # 鏇存縺杩涚殑椹遍€?)

## 绂佺敤椹遍€?middleware = FilesystemMiddleware(
    backend=backend,
    tool_token_limit_before_evict=None,  # 涓嶉┍閫?)
```

---

## 4. ContextOverflowError 鍏滃簳

鍗充娇鏈変富鍔ㄧ殑鎽樿瑙﹀彂鏈哄埗锛屾煇浜涙儏鍐典笅锛堝 token 璁℃暟涓嶇簿纭€佺郴缁熸秷鎭彉鍖栫瓑锛変粛鍙兘瑙﹀彂妯″瀷 API 鐨勪笂涓嬫枃婧㈠嚭閿欒銆?
`SummarizationMiddleware` 浼氭崟鑾?`ContextOverflowError`锛屽苟鑷姩鎵ц涓€娆℃憳瑕佷綔涓洪檷绾х瓥鐣ワ細

```python
## 绠€鍖栫殑鍐呴儴閫昏緫
try:
    return handler(request.override(messages=truncated_messages))
except ContextOverflowError:
    # 闄嶇骇锛氱珛鍗虫墽琛屾憳瑕?    cutoff_index = self._determine_cutoff_index(truncated_messages)
    messages_to_summarize, preserved = self._partition_messages(truncated_messages, cutoff_index)
    summary = self._create_summary(messages_to_summarize)
    # ... 鐢ㄦ憳瑕佸悗鐨勬秷鎭噸璇?```

---

## 寮€鍙戣€呮敞鎰忎簨椤?
### 浣跨敤 `create_deep_agent()` 鏃?
1. **榛樿宸插惎鐢ㄦ墍鏈夋満鍒?* 鈥?`create_deep_agent()` 鑷姩閰嶇疆浜?`SummarizationMiddleware` 鍜?`FilesystemMiddleware`锛屽寘鎷伐鍏峰弬鏁版埅鏂€傞€氬父涓嶉渶瑕侀澶栭厤缃€?
2. **`create_deep_agent()` 涓嶆毚闇?`trigger`/`keep` 鍙傛暟** 鈥?杩欎簺鍊肩敱 `_compute_summarization_defaults(model)` 鏍规嵁妯″瀷 profile 鑷姩璁＄畻锛屾病鏈夌洿鎺ョ殑瑕嗙洊鍏ュ彛銆傚鏋滈渶瑕佽嚜瀹氫箟锛屾湁涓ょ鏂瑰紡锛?
   **鏂瑰紡 A锛氶€氳繃 `middleware` 鍙傛暟杩藉姞锛堢畝鍗曚絾鏈夊壇浣滅敤锛?*

   ```python
   from deepagents import create_deep_agent
   from deepagents.middleware.summarization import SummarizationMiddleware
   from deepagents.backends import StateBackend

   custom_summarization = SummarizationMiddleware(
       model="openai:gpt-4o-mini",
       backend=StateBackend,
       trigger=("tokens", 100000),
       keep=("messages", 10),
   )

   agent = create_deep_agent(
       model="anthropic:claude-sonnet-4-5-20250929",
       middleware=[custom_summarization],
   )
   ```

   > 鈿狅笍 杩欐牱浼氬鑷村瓨鍦ㄤ袱涓?`SummarizationMiddleware`锛堝唴缃粯璁ょ殑 + 浣犺拷鍔犵殑锛夛紝涓よ€呴兘浼氭墽琛屻€傚浜庡ぇ澶氭暟鍦烘櫙杩欎笉浼氶€犳垚闂锛堝厛瑙﹀彂鐨勯偅涓細鍏堝帇缂╀笂涓嬫枃锛夛紝浣嗗苟涓嶇悊鎯炽€?
   **鏂瑰紡 B锛氱粫杩?`create_deep_agent()`锛岃嚜宸辩粍瑁?middleware 鏍?*

   鍙傝€?`libs/deepagents/deepagents/graph.py` 涓?`create_deep_agent()` 鐨勫疄鐜帮紝鐩存帴浣跨敤 `langchain.agents.create_agent()` 骞跺畬鍏ㄦ帶鍒?middleware 鍒楄〃銆傝繖鏍峰彲浠ョ簿纭浛鎹?`SummarizationMiddleware` 鐨勯厤缃€?
3. **鎽樿妯″瀷涓庝富妯″瀷鐩稿悓** 鈥?榛樿浣跨敤鍚屼竴涓ā鍨嬬敓鎴愭憳瑕併€傚鏋滄兂鑺傜渷鎴愭湰锛屽彲浠ュ湪鑷畾涔?`SummarizationMiddleware` 鏃舵寚瀹氫竴涓洿渚垮疁鐨勬ā鍨嬶紙濡?`gpt-4o-mini`锛夈€?
4. **Backend 璺敱** 鈥?CLI 妯″紡涓嬶紝澶у瀷宸ュ叿缁撴灉鍜屽璇濆巻鍙插垎鍒矾鐢卞埌鐙珛鐨勪复鏃剁洰褰曪紝閬垮厤姹℃煋宸ヤ綔鐩綍锛?
   ```python
   CompositeBackend(
       default=backend,
       routes={
           "/large_tool_results/": large_results_backend,
           "/conversation_history/": conversation_history_backend,
       },
   )
   ```

### 鑷畾涔?Middleware 鏃?
1. **State Schema 鍚堝苟** 鈥?`SummarizationMiddleware` 浣跨敤 `SummarizationState`锛屽叾涓寘鍚鏈夊瓧娈?`_summarization_event`銆傚鏋滀綘鐨勮嚜瀹氫箟 middleware 涔熷畾涔変簡 `state_schema`锛岀‘淇濅笉浼氫笌姝ゅ瓧娈靛啿绐併€?
2. **Middleware 椤哄簭寰堥噸瑕?* 鈥?鍦?`create_deep_agent()` 涓紝middleware 鐨勬墽琛岄『搴忔槸锛?
   ```text
   TodoListMiddleware 鈫?MemoryMiddleware 鈫?SkillsMiddleware 鈫?   FilesystemMiddleware 鈫?SubAgentMiddleware 鈫?SummarizationMiddleware 鈫?   AnthropicPromptCachingMiddleware 鈫?PatchToolCallsMiddleware 鈫?鐢ㄦ埛鑷畾涔?middleware
   ```

   `SummarizationMiddleware` 鍦?`FilesystemMiddleware` 涔嬪悗锛岃繖鎰忓懗鐫€瀹冨鐞嗙殑娑堟伅宸茬粡缁忚繃浜嗗伐鍏风粨鏋滈┍閫愩€?
3. **瀛?Agent 涔熸湁鐙珛鐨勬憳瑕?* 鈥?姣忎釜瀛?agent锛堝寘鎷?general-purpose subagent锛夐兘鏈夎嚜宸辩殑 `SummarizationMiddleware` 瀹炰緥锛岀嫭绔嬬鐞嗗悇鑷殑涓婁笅鏂囬暱搴︺€?
### 鎬ц兘涓庢垚鏈€冮噺

1. **鎽樿浼氫骇鐢熼澶栫殑 LLM 璋冪敤** 鈥?姣忔瑙﹀彂鎽樿鏃讹紝浼氶澶栬皟鐢ㄤ竴娆?LLM 鏉ョ敓鎴愭憳瑕佹枃鏈€俙trim_tokens_to_summarize` 鍙傛暟鎺у埗鍙戦€佺粰鎽樿 LLM 鐨勬渶澶?token 鏁帮紙榛樿 4000锛宍create_deep_agent` 涓涓?`None` 鍗充笉闄愬埗锛夈€?
2. **fraction 妯″紡渚濊禆妯″瀷 profile** 鈥?濡傛灉浣跨敤 `("fraction", 0.85)` 浣嗘ā鍨嬫病鏈?`max_input_tokens` profile锛宖raction 妯″紡浼氶潤榛樺け璐ワ紙涓嶈Е鍙戯級銆傜‘淇濅綘鐨勬ā鍨嬫湁姝ｇ‘鐨?profile锛屾垨浣跨敤 `("tokens", N)` 浣滀负澶囬€夈€?
3. **token 璁℃暟鏄繎浼肩殑** 鈥?榛樿浣跨敤 `count_tokens_approximately`锛岃繖鏄竴涓熀浜庡瓧绗︽暟鐨勭矖鐣ヤ及璁°€傚鏋滈渶瑕佹洿绮剧‘鐨勮鏁帮紝鍙互浼犲叆鑷畾涔夌殑 `token_counter` 鍑芥暟銆?
### 璋冭瘯涓庢帓鏌?
1. **鏃ュ織** 鈥?`SummarizationMiddleware` 浣跨敤 `logging` 妯″潡璁板綍鍏抽敭浜嬩欢銆傚惎鐢?`deepagents.middleware.summarization` logger 鐨?DEBUG 绾у埆鍙互鐪嬪埌锛?    - 娑堟伅鎸佷箙鍖栫殑璺緞鍜屾暟閲?    - 鎸佷箙鍖栧け璐ョ殑璀﹀憡

2. **CLI 涓殑鎽樿杩囨护** 鈥?CLI 鐨?Textual UI 浼氳繃婊ゆ帀鎽樿 LLM 鐨勬祦寮忚緭鍑猴紙閫氳繃 `_is_summarization_chunk` 妫€娴?`lc_source: "summarization"` 鍏冩暟鎹級锛岀敤鎴蜂笉浼氱湅鍒版憳瑕佺敓鎴愯繃绋嬨€?
3. **LocalContextMiddleware 鑱斿姩** 鈥?鍦?CLI 涓紝姣忔鎽樿浜嬩欢鍙戠敓鍚庯紝`LocalContextMiddleware` 浼氳嚜鍔ㄥ埛鏂版湰鍦颁笂涓嬫枃锛坓it 淇℃伅銆佺洰褰曟爲绛夛級锛岀‘淇濇憳瑕佸悗鐨勪笂涓嬫枃浠嶇劧鍖呭惈鏈€鏂扮殑鐜淇℃伅銆?
