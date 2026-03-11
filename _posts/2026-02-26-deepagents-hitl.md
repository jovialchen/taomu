---
layout: post
date: 2026-02-26
title: Deep Agents HITL 鏈哄埗锛氫汉宸ュ鎵逛腑鏂殑瀹屾暣瀹炵幇
categories: tech_coding
tags:
  - machine_learning
  - LLM
  - AIAgent
  - DeepAgents
---

## Deep Agents Human-in-the-Loop (HITL) 鏈哄埗璇﹁В

## 姒傝堪

Deep Agents 鐨?Human-in-the-Loop (HITL) 鏈哄埗鍏佽鍦?Agent 鎵ц宸ュ叿璋冪敤涔嬪墠鏆傚仠鎵ц锛岀瓑寰呬汉宸ュ鎵广€傝繖鏄竴涓叧閿殑瀹夊叏鏈哄埗锛岀壒鍒槸鍦ㄦ秹鍙婃枃浠跺啓鍏ャ€丼hell 鍛戒护鎵ц銆佺綉缁滆姹傜瓑鍏锋湁鍓綔鐢ㄧ殑鎿嶄綔鏃躲€?
HITL 鐨勬牳蹇冨疄鐜颁緷璧栦簬锛?1. **`HumanInTheLoopMiddleware`** 鈥?鏉ヨ嚜 `langchain.agents.middleware`锛屼綔涓?Middleware 鏍堢殑鏈€鍚庝竴灞?2. **LangGraph `interrupt` / `Command(resume=...)` 鏈哄埗** 鈥?搴曞眰鐨勬殏鍋?鎭㈠鍘熻
3. **`InterruptOnConfig`** 鈥?姣忎釜宸ュ叿鐨勪腑鏂厤缃紙鍏佽鐨勫喅绛栫被鍨嬨€佹弿杩扮瓑锛?
<pre class="mermaid">
graph LR
    A[LLM 鐢熸垚 Tool Call] --> B{interrupt_on 閰嶇疆?}
    B -- "True / InterruptOnConfig" --> C[HumanInTheLoopMiddleware]
    C --> D[LangGraph interrupt 鏆傚仠]
    D --> E[绛夊緟浜哄伐鍐崇瓥]
    E --> F{鍐崇瓥绫诲瀷}
    F -- approve --> G[鎵ц Tool Call]
    F -- reject --> H[杩斿洖鎷掔粷娑堟伅缁?LLM]
    F -- edit --> I[淇敼鍙傛暟鍚庢墽琛宂
    B -- "False / 鏈厤缃? --> G
</pre>

## 鏍稿績鏁版嵁妯″瀷

### `interrupt_on` 閰嶇疆

`interrupt_on` 鏄竴涓瓧鍏革紝鏄犲皠宸ュ叿鍚嶇О鍒颁腑鏂厤缃細

```python
interrupt_on: dict[str, bool | InterruptOnConfig] | None
```

閰嶇疆鏂瑰紡鏈変笁绉嶏細

| 閰嶇疆鍊?| 鍚箟 | 榛樿 `allowed_decisions` |
|--------|------|------------------------|
| `True` | 鍚敤涓柇锛屼娇鐢ㄩ粯璁ら厤缃?| `["approve", "edit", "reject"]` |
| `False` | 鏄惧紡绂佺敤璇ュ伐鍏风殑涓柇 | 鈥?|
| `InterruptOnConfig` | 绮剧粏鎺у埗 | 鑷畾涔?|

### `InterruptOnConfig`

```python
class InterruptOnConfig(TypedDict):
    allowed_decisions: list[str]  # "approve", "edit", "reject" 鐨勫瓙闆?    description: str | Callable   # 涓柇鏃舵樉绀虹殑鎻忚堪淇℃伅
```

### 閰嶇疆绀轰緥

```python
## 绀轰緥 1: 绠€鍗曞竷灏旈厤缃?interrupt_on = {
    "sample_tool": True,       # 鍚敤锛岄粯璁?approve/edit/reject
    "get_weather": False,      # 绂佺敤锛岃嚜鍔ㄦ墽琛?    "get_soccer_scores": {"allowed_decisions": ["approve", "reject"]},  # 鍙厑璁告壒鍑嗘垨鎷掔粷锛屼笉鍏佽缂栬緫
}
```

## Middleware 鏍堜腑鐨勪綅缃?
`HumanInTheLoopMiddleware` 濮嬬粓鏄?Middleware 鏍堢殑**鏈€鍚庝竴灞?*銆傝繖鎰忓懗鐫€瀹冨湪鎵€鏈夊叾浠?Middleware 澶勭悊瀹屾瘯鍚庢墠浠嬪叆锛岀‘淇濇嫤鎴殑鏄渶缁堣鎵ц鐨勫伐鍏疯皟鐢ㄣ€?
<pre class="mermaid">
graph TB
    subgraph "涓讳唬鐞?Middleware 鏍?
        M1[TodoListMiddleware] --> M2[MemoryMiddleware]
        M2 --> M3[SkillsMiddleware]
        M3 --> M4[FilesystemMiddleware]
        M4 --> M5[SubAgentMiddleware]
        M5 --> M6[SummarizationMiddleware]
        M6 --> M7[AnthropicPromptCachingMiddleware]
        M7 --> M8[PatchToolCallsMiddleware]
        M8 --> M9[鐢ㄦ埛鑷畾涔?Middleware]
        M9 --> M10["HumanInTheLoopMiddleware 鈫?鏈€鍚庝竴灞?]
    end

    style M10 fill:#ff6b6b,color:#fff
</pre>

### 涓轰粈涔堟槸鏈€鍚庝竴灞傦紵

鍦?`create_deep_agent()` 涓紝HITL Middleware 鐨勬坊鍔犻€昏緫濡備笅锛?
```python
## graph.py 涓殑鍏抽敭浠ｇ爜
deepagent_middleware: list[AgentMiddleware] = [
    TodoListMiddleware(),
    MemoryMiddleware(...),
    SkillsMiddleware(...),
    FilesystemMiddleware(backend=backend),
    SubAgentMiddleware(backend=backend, subagents=all_subagents),
    SummarizationMiddleware(...),
    AnthropicPromptCachingMiddleware(...),
    PatchToolCallsMiddleware(),
]

## 鐢ㄦ埛鑷畾涔?middleware 鍦?HITL 涔嬪墠
if middleware:
    deepagent_middleware.extend(middleware)

## HITL 濮嬬粓鏈€鍚庢坊鍔?if interrupt_on is not None:
    deepagent_middleware.append(HumanInTheLoopMiddleware(interrupt_on=interrupt_on))
```

杩欎釜椤哄簭淇濊瘉浜嗭細
1. 鎵€鏈夊伐鍏锋敞鍐岋紙FilesystemMiddleware銆丼ubAgentMiddleware 绛夛級宸插畬鎴?2. 鎵€鏈?Prompt 淇敼锛圡emoryMiddleware銆丼killsMiddleware 绛夛級宸插簲鐢?3. HITL 鎷︽埅鐨勬槸瀹屾暣鐨勩€佹渶缁堢殑宸ュ叿璋冪敤鍒楄〃

## 瀹屾暣鎵ц娴佺▼

### 鍩烘湰娴佺▼

<pre class="mermaid">
sequenceDiagram
    participant User as 鐢ㄦ埛
    participant Agent as Deep Agent
    participant LLM as LLM
    participant HITL as HumanInTheLoopMiddleware
    participant Tool as Tool Node

    User->>Agent: invoke({"messages": [HumanMessage]})
    Agent->>LLM: 鎺ㄧ悊璇锋眰
    LLM-->>Agent: AIMessage + ToolCalls

    Note over Agent,HITL: HITL 妫€鏌ユ瘡涓?ToolCall

    loop 瀵规瘡涓?ToolCall
        Agent->>HITL: 妫€鏌?interrupt_on 閰嶇疆
        alt 宸ュ叿鍦?interrupt_on 涓笖鍊间负 True/Config
            HITL->>HITL: 鏀堕泦闇€瑕佸鎵圭殑 ToolCalls
        else 宸ュ叿涓嶅湪 interrupt_on 涓垨鍊间负 False
            HITL->>Tool: 鐩存帴鎵ц
            Tool-->>Agent: ToolMessage 缁撴灉
        end
    end

    alt 鏈夐渶瑕佸鎵圭殑 ToolCalls
        HITL-->>User: interrupt (鏆傚仠鎵ц)
        Note over User: 鐢ㄦ埛鐪嬪埌瀹℃壒璇锋眰
        User->>Agent: Command(resume={"decisions": [...]})

        loop 瀵规瘡涓喅绛?            alt approve
                Agent->>Tool: 鎵ц鍘熷 ToolCall
                Tool-->>Agent: ToolMessage 缁撴灉
            else reject
                Agent->>LLM: 杩斿洖鎷掔粷娑堟伅
            else edit
                Agent->>Tool: 鎵ц淇敼鍚庣殑 ToolCall
                Tool-->>Agent: ToolMessage 缁撴灉
            end
        end
    end

    Agent->>LLM: 缁х画鎺ㄧ悊锛堝甫 ToolMessage 缁撴灉锛?    LLM-->>User: 鏈€缁堝搷搴?</pre>

### 骞惰宸ュ叿璋冪敤鐨勫鐞?
褰?LLM 鍦ㄤ竴娆″搷搴斾腑鍙戝嚭澶氫釜宸ュ叿璋冪敤鏃讹紝HITL 浼氬皢闇€瑕佸鎵圭殑璋冪敤**鎵归噺鏀堕泦**锛屼竴娆℃€у憟鐜扮粰鐢ㄦ埛锛?
<pre class="mermaid">
graph TD
    A[LLM 杩斿洖 3 涓?ToolCalls] --> B{閫愪釜妫€鏌?interrupt_on}

    B --> C1["sample_tool 鈫?True 鈫?闇€瑕佸鎵?]
    B --> C2["get_weather 鈫?False 鈫?鐩存帴鎵ц"]
    B --> C3["get_soccer_scores 鈫?Config 鈫?闇€瑕佸鎵?]

    C2 --> D[get_weather 绔嬪嵆鎵ц]

    C1 --> E["鎵归噺涓柇: action_requests = [sample_tool, get_soccer_scores]"]
    C3 --> E

    E --> F[鐢ㄦ埛涓€娆℃€у鎵规墍鏈夊緟瀹℃壒鐨勮皟鐢╙
    F --> G["Command(resume={decisions: [approve, approve]})"]
    G --> H[sample_tool 鎵ц]
    G --> I[get_soccer_scores 鎵ц]
</pre>

鍏抽敭鐐癸細
- **鏈厤缃腑鏂殑宸ュ叿**锛堝 `get_weather: False`锛変細**绔嬪嵆鎵ц**锛屼笉绛夊緟瀹℃壒
- **闇€瑕佸鎵圭殑宸ュ叿**浼氳**鎵归噺鏀堕泦**鍒颁竴涓?interrupt 涓?- 鐢ㄦ埛鐨勫喅绛栨暟閲忓繀椤讳笌 `action_requests` 鏁伴噺涓€鑷?
## 瀛愪唬鐞?(SubAgent) 涓殑 HITL

### 閰嶇疆缁ф壙

HITL 閰嶇疆鍦ㄥ瓙浠ｇ悊涓湁涓ょ妯″紡锛?
#### 1. 缁ф壙鐖朵唬鐞嗛厤缃?
褰撳瓙浠ｇ悊娌℃湁鎸囧畾鑷繁鐨?`interrupt_on` 鏃讹紝浣跨敤鐖朵唬鐞嗙殑閰嶇疆锛?
```python
agent = create_deep_agent(
    model=model,
    tools=[sample_tool, get_weather, get_soccer_scores],
    interrupt_on={
        "sample_tool": True,
        "get_weather": False,
        "get_soccer_scores": {"allowed_decisions": ["approve", "reject"]},
    },
    checkpointer=checkpointer,
)
## general-purpose 瀛愪唬鐞嗚嚜鍔ㄧ户鎵夸笂杩?interrupt_on 閰嶇疆
```

#### 2. 瀛愪唬鐞嗚嚜瀹氫箟閰嶇疆

瀛愪唬鐞嗗彲浠ヨ鐩栫埗浠ｇ悊鐨?HITL 閰嶇疆锛?
```python
agent = create_deep_agent(
    model=model,
    tools=[sample_tool, get_weather, get_soccer_scores],
    interrupt_on={
        "sample_tool": True,
        "get_weather": False,
        "get_soccer_scores": {"allowed_decisions": ["approve", "reject"]},
    },
    checkpointer=checkpointer,
    subagents=[
        {
            "name": "task_handler",
            "description": "A subagent that can handle all sorts of tasks",
            "system_prompt": "You are a task handler.",
            "tools": [sample_tool, get_weather, get_soccer_scores],
            # 瀛愪唬鐞嗚嚜瀹氫箟锛歴ample_tool 涓嶉渶瑕佸鎵癸紝get_weather 闇€瑕佸鎵?            "interrupt_on": {
                "sample_tool": False,
                "get_weather": True,
                "get_soccer_scores": True,
            },
        },
    ],
)
```

## CLI 涓殑 HITL 瀹炵幇

Deep Agents CLI (`deepagents-cli`) 鎻愪緵浜嗕袱绉?HITL 浜や簰妯″紡锛氫氦浜掑紡 (Textual TUI) 鍜岄潪浜や簰寮忋€?
### 浜や簰寮忔ā寮?(Textual TUI)

鍦ㄤ氦浜掑紡妯″紡涓嬶紝CLI 浣跨敤 Textual 妗嗘灦娓叉煋瀹℃壒瀵硅瘽妗嗭細

<pre class="mermaid">
graph TD
    subgraph "TextualUIAdapter 娴佸紡澶勭悊"
        A[agent.astream] --> B{stream chunk 绫诲瀷}
        B -- "updates + __interrupt__" --> C[瑙ｆ瀽 HITLRequest]
        B -- "messages" --> D[娓叉煋 AI/Tool 娑堟伅]

        C --> E{session_state.auto_approve?}
        E -- 鏄?--> F[鑷姩鎵瑰噯鎵€鏈塢
        E -- 鍚?--> G[鏄剧ず ApprovalMenu]

        G --> H{鐢ㄦ埛閫夋嫨}
        H -- "1/y: Approve" --> I["ApproveDecision"]
        H -- "2/n: Reject" --> J["RejectDecision"]
        H -- "3/a: Auto-approve all" --> K["鍚敤 auto_approve + ApproveDecision"]

        I --> L["Command(resume={decisions})"]
        J --> L
        K --> L
        F --> L
        L --> A
    end
</pre>

#### ApprovalMenu 缁勪欢

`ApprovalMenu` 鏄竴涓?Textual `Container` 缁勪欢锛屾彁渚涗互涓嬪姛鑳斤細

| 蹇嵎閿?| 鎿嶄綔 |
|--------|------|
| `1` / `y` / `Enter`(閫変腑 Approve) | 鎵瑰噯鎵€鏈夊緟瀹℃壒鐨勫伐鍏疯皟鐢?|
| `2` / `n` / `Enter`(閫変腑 Reject) | 鎷掔粷鎵€鏈夊緟瀹℃壒鐨勫伐鍏疯皟鐢?|
| `3` / `a` / `Enter`(閫変腑 Auto-approve) | 鍚敤鑷姩鎵瑰噯锛堟湰娆′細璇濆唴鎵€鏈夊悗缁皟鐢ㄨ嚜鍔ㄦ壒鍑嗭級 |
| `e` | 灞曞紑/鎶樺彔 Shell 鍛戒护璇︽儏 |
| `鈫慲 / `k` | 鍚戜笂绉诲姩閫夋嫨 |
| `鈫揱 / `j` | 鍚戜笅绉诲姩閫夋嫨 |

### 闈炰氦浜掑紡妯″紡

闈炰氦浜掑紡妯″紡 (`deepagents -n "task"`) 浣跨敤鍩轰簬瑙勫垯鐨勮嚜鍔ㄥ喅绛栵細

<pre class="mermaid">
graph TD
    A[鏀跺埌 HITL 涓柇] --> B{action_name 鏄?Shell 宸ュ叿?}

    B -- 鍚?--> C["鑷姩鎵瑰噯 鉁?]

    B -- 鏄?--> D{鏈?shell_allow_list?}
    D -- 鍚?--> E["鑷姩鎷掔粷 鉁?br/>Shell commands not permitted"]

    D -- 鏄?--> F{鍛戒护鍦?allow-list 涓?}
    F -- 鏄?--> C
    F -- 鍚?--> G["鎷掔粷 鉁?br/>Command not in allow-list"]
</pre>

#### 闈炰氦浜掑紡 HITL 鍐崇瓥閫昏緫

```python
def _make_hitl_decision(action_request, console):
    action_name = action_request.get("name", "")

    if action_name in SHELL_TOOL_NAMES:
        if not settings.shell_allow_list:
            return {"type": "reject", "message": "Shell commands not permitted..."}

        command = action_request.get("args", {}).get("command", "")
        if is_shell_command_allowed(command, settings.shell_allow_list):
            return {"type": "approve"}
        else:
            return {"type": "reject", "message": f"Command '{command}' not in allow-list..."}

    # 闈?Shell 宸ュ叿鑷姩鎵瑰噯
    return {"type": "approve"}
```

## CLI 涓鎷︽埅鐨勫伐鍏?
鍦?CLI 鐨勯粯璁ら厤缃腑锛坄auto_approve=False`锛夛紝浠ヤ笅宸ュ叿浼氳Е鍙?HITL 瀹℃壒锛?
| 宸ュ叿鍚嶇О | 璇存槑 | `allowed_decisions` |
|----------|------|-------------------|
| `execute` | Shell 鍛戒护鎵ц | `["approve", "reject"]` |
| `write_file` | 鍐欏叆鏂囦欢 | `["approve", "reject"]` |
| `edit_file` | 缂栬緫鏂囦欢 | `["approve", "reject"]` |
| `web_search` | 缃戠粶鎼滅储 | `["approve", "reject"]` |
| `fetch_url` | 鑾峰彇 URL 鍐呭 | `["approve", "reject"]` |
| `task` | 鍚姩瀛愪唬鐞?| `["approve", "reject"]` |

浠ヤ笅宸ュ叿**涓嶄細**瑙﹀彂 HITL锛堝彧璇绘搷浣滐級锛?- `ls` 鈥?鍒楀嚭鐩綍
- `read_file` 鈥?璇诲彇鏂囦欢
- `glob` 鈥?鏂囦欢妯″紡鍖归厤
- `grep` 鈥?鏂囨湰鍐呭鎼滅储
- `write_todos` 鈥?绠＄悊寰呭姙鍒楄〃

## Checkpointer 渚濊禆

HITL 鏈哄埗**蹇呴』**閰嶅悎 LangGraph Checkpointer 浣跨敤銆侰heckpointer 璐熻矗锛?
1. **鎸佷箙鍖栦腑鏂姸鎬?* 鈥?褰?`interrupt` 鍙戠敓鏃讹紝Agent 鐨勫畬鏁寸姸鎬侊紙娑堟伅鍘嗗彶銆佸緟鎵ц鐨勫伐鍏疯皟鐢ㄧ瓑锛夎淇濆瓨
2. **鎭㈠鎵ц** 鈥?褰?`Command(resume=...)` 鍒拌揪鏃讹紝浠庝繚瀛樼殑鐘舵€佹仮澶嶆墽琛?3. **绾跨▼闅旂** 鈥?閫氳繃 `thread_id` 纭繚涓嶅悓瀵硅瘽鐨勪腑鏂姸鎬佷簰涓嶅共鎵?
```python
from langgraph.checkpoint.memory import MemorySaver

## 蹇呴』鎻愪緵 checkpointer
agent = create_deep_agent(
    model="claude-sonnet-4-5-20250929",
    tools=[...],
    interrupt_on={"execute": True, "write_file": True},
    checkpointer=MemorySaver(),  # 蹇呴渶锛?)

## 浣跨敤 thread_id 鏍囪瘑瀵硅瘽
config = {"configurable": {"thread_id": "my-thread-123"}}

## 绗竴娆¤皟鐢?鈥?鍙兘瑙﹀彂涓柇
result = agent.invoke({"messages": [HumanMessage("鍐欎竴涓?hello.py")]}, config)

## 妫€鏌ヤ腑鏂姸鎬?state = agent.get_state(config)
if state.interrupts:
    # 鎭㈠鎵ц
    result = agent.invoke(
        Command(resume={"decisions": [{"type": "approve"}]}),
        config
    )
```

## 瀹夊叏寤鸿

### 浣曟椂鍚敤 HITL

| 鍦烘櫙 | 寤鸿 |
|------|------|
| 浣跨敤 `FilesystemBackend`锛堢洿鎺ヨ闂湰鍦版枃浠剁郴缁燂級 | **寮虹儓寤鸿**鍚敤 HITL |
| 浣跨敤 `LocalShellBackend`锛堟墽琛?Shell 鍛戒护锛?| **蹇呴』**鍚敤 HITL |
| 浣跨敤 `StateBackend`锛堝唴瀛樹腑鎿嶄綔锛?| 鍙€?|
| 浣跨敤 `BaseSandbox`锛堟矙绠辩幆澧冿級 | 鍙€夛紝浣嗗缓璁鏁忔劅鎿嶄綔鍚敤 |
| 鐢熶骇鐜 | 鏍规嵁淇′换绾у埆鍐冲畾 |

### 鏈€浣冲疄璺?
1. **鏈€灏忔潈闄愬師鍒?* 鈥?鍙闇€瑕佺殑宸ュ叿鍚敤涓柇锛岄伩鍏嶈繃搴﹀鎵瑰鑷寸敤鎴风柌鍔?2. **Shell 鍛戒护濮嬬粓瀹℃壒** 鈥?`execute` 宸ュ叿鍙互缁曡繃鏂囦欢绯荤粺闄愬埗锛屽簲濮嬬粓鍚敤 HITL
3. **闈炰氦浜掑紡浣跨敤鐧藉悕鍗?* 鈥?鍦?CI/CD 绛夐潪浜や簰寮忓満鏅腑锛屼娇鐢?`--shell-allow-list` 闄愬埗鍏佽鐨勫懡浠?4. **瀛愪唬鐞嗛厤缃?* 鈥?瀛愪唬鐞嗗彲浠ユ湁鏇村鏉炬垨鏇翠弗鏍肩殑 HITL 閰嶇疆锛屾牴鎹叾鑱岃矗璋冩暣

---

**鏈郴鍒楁枃绔狅細**
- [Deep Agents 鏋舵瀯鍏ㄦ櫙](/blog/2026/02/26/deepagents-architecture/)
- [Deep Agents Human-in-the-Loop 鏈哄埗璇﹁В](/blog/2026/02/26/deepagents-hitl/)锛堟湰鏂囷級
- [Deep Agents Memory 鏈哄埗璇﹁В](/blog/2026/02/26/deepagents-memory/)
- [Deep Agents General-Purpose SubAgent 璇﹁В](/blog/2026/02/26/deepagents-subagent/)
