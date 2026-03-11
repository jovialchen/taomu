---
layout: post
date: 2026-03-03
title: "寮€婧?AI Agent 涓?SDK 瀵规瘮鎸囧崡"
categories: tech_coding
tags:
  - AIAgent
  - OpenSource
  - SDK
  - DeepAgents
  - ClaudeCode
  - OpenAI
  - Google
---

## 寮€婧?AI Agent 涓?SDK 瀵规瘮鎸囧崡

## 1. Goose

- **Owner:** Block
- **Source Code:** https://github.com/block/goose
- **Documentation:** https://block.github.io/goose/docs/quickstart
- **Open Source:** Yes
- **Provide SDK:** No
- **Support Skills:** Yes
- **Programming Language:** Rust + TypeScript
- **Notes:** 寮€婧愩€佸彲鎵╁睍鐨?AI 浠ｇ悊锛岃秴瓒婁唬鐮佸缓璁紝鏀寔瀹夎銆佹墽琛屻€佺紪杈戝拰娴嬭瘯浠讳綍 LLM

---

## 2. Opencode

- **Owner:** SST
- **Source Code:** https://github.com/sst/opencode
- **Documentation:** https://opencode.ai/docs
- **Open Source:** Yes
- **Provide SDK:** Yes
- **Support Skills:** Yes
- **Programming Language:** TypeScript
- **Notes:** 
  - 缁堢 AI 缂栫爜浠ｇ悊
  - 鏀寔 75+ LLM 鎻愪緵鍟?  - 鍏锋湁鍘熺敓 LSP 鏀寔
  - 鏀寔澶氫細璇濆苟琛岃繍琛屽涓唬鐞?
### SDK 鍏蜂綋寮曠敤鍜屼娇鐢ㄦ柟寮?
**1. 瀹夎 SDK**

鍦ㄦ偍鐨勯」鐩洰褰曚腑锛岄€氳繃 npm 鎴?yarn 绛夊寘绠＄悊鍣ㄨ繘琛屽畨瑁咃細

```bash
npm install @opencode-ai/sdk
## 鎴?yarn add @opencode-ai/sdk
```

**2. 鍦ㄤ唬鐮佷腑瀵煎叆鍜屽垵濮嬪寲**

鎮ㄥ彲浠ュ湪浠ｇ爜鏂囦欢涓鍏?SDK 骞跺垱寤哄鎴风瀹炰緥锛?
```javascript
// 鏂瑰紡涓€锛氳 SDK 鑷姩鍒涘缓鎴栬繛鎺ュ埌鏈湴鐨?Opencode 瀹炰緥
import { createOpencode } from "@opencode-ai/sdk";
const { client } = await createOpencode();

// 鏂瑰紡浜岋細杩炴帴鍒版寚瀹氬湴鍧€鐨勫凡鏈?Opencode 瀹炰緥
import { createOpencodeClient } from "@opencode-ai/sdk";
const client = createOpencodeClient({ baseUrl: "http://localhost:4096" });
```

**3. 璋冪敤 SDK 鎻愪緵鐨勬柟娉?*

鑾峰緱 `client` 瀹炰緥鍚庯紝鍗冲彲璋冪敤鍏朵赴瀵岀殑 API 鏂规硶锛屼緥濡傦細

```javascript
// 鍒涘缓涓€涓柊鐨勪細璇?const session = await client.sessions.create();

// 鍦ㄤ細璇濅腑鎵ц鎸囦护
const response = await client.sessions.instruction(session.id, {
  instruction: "璇峰垎鏋愬綋鍓嶇洰褰曚笅鐨?package.json 鏂囦欢"
});

// 鎼滅储椤圭洰涓殑鏂囦欢
const files = await client.files.search({
  query: "function component"
});
```

**鎬荤粨**锛歄pencode SDK 鐨勮璁″垵琛峰氨鏄负浜嗚寮€鍙戣€?*鍦ㄤ唬鐮佷腑寮曠敤**锛屽畠灏?Opencode 鏅鸿兘浣撶殑鑳藉姏锛堝浼氳瘽绠＄悊銆佹枃浠舵搷浣溿€佷换鍔℃墽琛岀瓑锛夊皝瑁呮垚浜嗕竴绯诲垪缂栫▼鎺ュ彛锛屼娇鎮ㄥ彲浠ヨ交鏉惧湴灏?AI 浠ｇ悊鍔熻兘宓屽叆鍒拌嚜宸辩殑搴旂敤绋嬪簭銆佽嚜鍔ㄥ寲鑴氭湰鎴栧伐鍏烽摼涓€?
---

## 3. Claude Code

- **Owner:** Anthropic
- **Source Code:** 涓撴湁锛堥棴婧愶級
- **Documentation:** https://claude.ai/docs/code
- **Open Source:** No
- **Provide SDK:** [anthropics/claude-agent-sdk-python](https://github.com/anthropics/claude-agent-sdk-python)
- **Support Skills:** Yes
- **Programming Language:** TypeScript + Rust
- **Notes:** 
  - Anthropic 寮€鍙戠殑鍩轰簬缁堢鐨?AI 缂栫爜浠ｇ悊
  - 鍏锋湁瀹屾暣鐨勬枃浠剁郴缁熻闂潈闄?  - 鍙互璇诲彇銆佸啓鍏ュ拰缂栬緫椤圭洰鏂囦欢
  - 杩愯缁堢鍛戒护
  - 绠＄悊 Git 宸ヤ綔娴?
### SDK 鍏蜂綋寮曠敤鍜屼娇鐢ㄦ柟寮?
杩欐槸 Anthropic 瀹樻柟鎻愪緵鐨?**Claude Agent SDK 鐨?Python 鐗堟湰**銆?
鍏舵牳蹇冨姛鑳芥槸璁╁紑鍙戣€呰兘鍦ㄨ嚜宸辩殑 **Python 搴旂敤绋嬪簭涓洿鎺ラ泦鎴愬拰璋冪敤 Claude Code锛圕laude AI 浠ｇ悊锛夌殑鍏ㄩ儴鑳藉姏**锛岃€屼笉浠呴檺浜庨€氳繃鍛戒护琛屼氦浜掋€?
- **宓屽叆寮忛泦鎴?*锛歋DK 宸茶嚜鍔ㄦ崋缁?Claude Code CLI锛屾棤闇€鍗曠嫭瀹夎锛屽彲鐩存帴鍦ㄤ唬鐮佷腑璋冪敤銆? 
- **鍙屽悜瀵硅瘽**锛氭敮鎸佷笌 Claude Code 杩涜鍙屽悜銆佷氦浜掑紡鐨勭▼搴忓寲瀵硅瘽
- **鑷畾涔夊伐鍏?*锛氬厑璁镐綘灏?Python 鍑芥暟瀹氫箟涓?宸ュ叿"渚?Claude 璋冪敤锛岃繖浜涘伐鍏蜂互**杩涚▼鍐?MCP 鏈嶅姟鍣?*鐨勫舰寮忚繍琛岋紝鏃犻渶鐙珛杩涚▼銆?- **閽╁瓙 (Hooks)**锛氬厑璁镐綘鍦?Claude 浠ｇ悊鎵ц寰幆鐨勭壒瀹氳妭鐐规彃鍏ヨ嚜瀹氫箟鐨?Python 鍑芥暟锛屼互瀹炵幇纭畾鐨勯€昏緫澶勭悊鎴栬嚜鍔ㄥ弽棣堛€?
---

## 4. OpenClaw

- **Owner:** Peter Steinberger
- **Source Code:** https://github.com/openclaw/openclaw
- **Documentation:** [OpenClaw - OpenClaw](https://docs.openclaw.ai/)
- **Open Source:** Yes
- **Provide SDK:** No
- **Support Skills:** Yes
- **Support ACP:** Yes, https://docs.openclaw.ai/tools/acp-agents
- **Programming Language:** TypeScript + Node.js
- **Notes:** 
  - 寮€婧愯嚜涓?AI 浠ｇ悊妗嗘灦
  - 鍙湪鏈湴杩愯
  - 杩炴帴瓒呰繃 20 涓秷鎭钩鍙?  - 鍏锋湁蹇冭烦璋冨害鍣?  - 鏀寔娴忚鍣ㄨ嚜鍔ㄥ寲銆佹枃浠惰鍐欍€乻hell 鍛戒护绛?
OpenClaw 鐨?ACP Agents 鏄竴涓敤浜庤皟搴﹀閮?AI 缂栫爜宸ュ叿锛堝 Claude Code銆丆odex锛夌殑鍗忚绯荤粺銆傛垜璇曚簡涓€涓嬫病璇曞嚭鏉ャ€?
---

## 5. Agentic Seek

- **Owner:** 绀惧尯寮€婧?- **Source Code:** [Fosowl/agenticSeek: Fully Local Manus AI. No APIs, No $200 monthly bills. Enjoy an autonomous agent that thinks, browses the web, and code for the sole cost of electricity. 馃敂 Official updates only via twitter @Martin993886460 (Beware of fake account)](https://github.com/Fosowl/agenticSeek)
- **Documentation:** https://fosowl.github.io/agenticSeek.html
- **Open Source:** Yes
- **Provide SDK:** No
- **Support Skills:** Yes
- **Programming Language:** Python
- **Notes:** 
  - 瀹屽叏鍏嶈垂銆佸紑婧愮殑閫氱敤 AI 鍔╂墜
  - 鍏锋湁璇煶鍔熻兘
  - 鍙互鑷富娴忚缃戦〉
  - 缂栧啓浠ｇ爜鍜屾墽琛屽鏉備换鍔?  - 鏁版嵁鏈湴瀛樺偍

---

## 6. DeepAgents

- **Owner:** LangChain AI
- **Source Code:** https://github.com/langchain-ai/deepagents
- **Documentation:** https://docs.langchain.com/deepagents
- **Open Source:** Yes
- **Provide SDK:** Yes
- **Support Skills:** Yes
- **Programming Language:** Python
- **Notes:** 
  - 鍩轰簬 LangChain 鍜?LangGraph 鏋勫缓鐨勪唬鐞嗘鏋?  - 鍏锋湁瑙勫垝宸ュ叿
  - 鏂囦欢绯荤粺鍚庣
  - 鐢熸垚瀛愪唬鐞嗙殑鑳藉姏
  - 閫傜敤浜庡鏉傜殑浠ｇ悊浠诲姟
  - **鏈€鏂扮増鏈?** 0.4.4 (2026-02-26)

---

## 7. AgentZero

- **Owner:** 绀惧尯寮€婧?- **Source Code:** [agent0ai/agent-zero: Agent Zero AI framework](https://github.com/agent0ai/agent-zero)
- **Documentation:** https://agentzero.ai/docs
- **Open Source:** Yes
- **Provide SDK:** No
- **Support Skills:** Yes
- **Programming Language:** TypeScript + Python

---

## 8. OpenAI Agents SDK

- **Owner:** OpenAI
- **Source Code:** [openai/openai-agents-python: A lightweight, powerful framework for multi-agent workflows](https://github.com/openai/openai-agents-python)
- **Documentation:** [OpenAI Agents SDK](https://openai.github.io/openai-agents-python/)
- **Open Source:** Yes
- **Provide SDK:** Yes 
- **Support Skills:** Yes [Tools - OpenAI Agents SDK](https://openai.github.io/openai-agents-python/tools/#hosted-container-shell-skills)
- **Programming Language:** Python
- **Notes:** 

```python
from agents import Agent, Runner

agent = Agent(name="Assistant", instructions="You are a helpful assistant")

result = Runner.run_sync(agent, "Write a haiku about recursion in programming.")
print(result.final_output)

## Code within the code,
## Functions calling themselves,
## Infinite loop's dance.
```

---

## 9. Google ADK (Agent Development Kit)

- **Owner:** Google
- **Source Code:** [google/adk-python: An open-source, code-first Python toolkit for building, evaluating, and deploying sophisticated AI agents with flexibility and control.](https://github.com/google/adk-python)
- **Documentation:** [Index - Agent Development Kit (ADK)](https://google.github.io/adk-docs/)
- **Open Source:** Yes
- **Provide SDK:** Yes
- **Support Skills:** Yes
- **Programming Language:** TypeScript + Python + Java + Go
- **Notes:** 
  - Google 鐨勪唬鐞嗗紑鍙戝伐鍏峰寘
  - 寮€婧愭鏋?  - 甯姪寮€鍙戣€呮瀯寤恒€佹祴璇曘€佽瘎浼板拰閮ㄧ讲鑷富 AI 浠ｇ悊鍜屽浠ｇ悊绯荤粺
  - 娣卞害闆嗘垚 Google 鐢熸€佺郴缁?
```python
from google.adk.agents.llm_agent import Agent

## Mock tool implementation
def get_current_time(city: str) -> dict:
    """Returns the current time in a specified city."""
    return {"status": "success", "city": city, "time": "10:30 AM"}

root_agent = Agent(
    model='gemini-3-flash-preview',
    name='root_agent',
    description="Tells the current time in a specified city.",
    instruction="You are a helpful assistant that tells the current time in cities. Use the 'get_current_time' tool for this purpose.",
    tools=[get_current_time],
)
```

---

## 鎬荤粨瀵规瘮琛?
| 椤圭洰 | 寮€婧?| SDK | 璇█ | 鐗圭偣 |
|------|------|-----|------|------|
| Goose | 鉁?| 鉂?| Rust+TS | 鍙墿灞曚唬鐞?|
| Opencode | 鉁?| 鉁?| TS | 75+ LLM 鏀寔 |
| Claude Code | 鉂?| 鉁?| TS+Rust | 缁堢缂栫爜浠ｇ悊 |
| OpenClaw | 鉁?| 鉂?| TS+Node | 澶氬钩鍙拌繛鎺?|
| Agentic Seek | 鉁?| 鉂?| Python | 瀹屽叏鏈湴杩愯 |
| DeepAgents | 鉁?| 鉁?| Python | LangChain 鐢熸€?|
| AgentZero | 鉁?| 鉂?| TS+Python | 绀惧尯椹卞姩 |
| OpenAI Agents | 鉁?| 鉁?| Python | 瀹樻柟 SDK |
| Google ADK | 鉁?| 鉁?| 澶氳瑷€ | Google 鐢熸€?|

---

*Published on 2026-03-03 by Joyce*
