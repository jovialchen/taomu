---
layout: post
date: 2026-02-26
title: Deep Agents 鏋舵瀯鍏ㄦ櫙锛氫粠 Backend 鍒?Middleware 鐨勫畬鏁磋В璇?categories: tech_coding
tags:
  - machine_learning
  - LLM
  - AIAgent
  - DeepAgents
---

## Deep Agents 鏋舵瀯鍏ㄦ櫙

## LangGraph 鍥剧粨鏋?
Deep Agents 鐨勬牳蹇冩槸閫氳繃 `create_deep_agent()` 鍑芥暟鏋勫缓涓€涓?LangGraph `CompiledStateGraph`銆傚簳灞傝皟鐢?`langchain.agents.create_agent()` 鍒涘缓涓€涓?tool-calling agent loop銆?
<pre class="mermaid">
graph TD
    subgraph "create_deep_agent()"
        A[鐢ㄦ埛杈撳叆 / HumanMessage] --> B[System Prompt 缁勮]
        B --> C[LLM 鎺ㄧ悊]
        C --> D{鏈?Tool Calls?}
        D -- 鏄?--> E[Tool Node 鎵ц]
        E --> F{HITL 涓柇?}
        F -- 鍚?--> C
        F -- 鏄?--> G[绛夊緟浜哄伐瀹℃壒]
        G --> E
        D -- 鍚?--> H[杩斿洖鏈€缁堝搷搴擼
    end

    subgraph "Middleware 鏍?(鎸夐『搴?"
        M1[TodoListMiddleware] --> M2[MemoryMiddleware]
        M2 --> M3[SkillsMiddleware]
        M3 --> M4[FilesystemMiddleware]
        M4 --> M5[SubAgentMiddleware]
        M5 --> M6[SummarizationMiddleware]
        M6 --> M7[AnthropicPromptCachingMiddleware]
        M7 --> M8[PatchToolCallsMiddleware]
        M8 --> M9[鐢ㄦ埛鑷畾涔?Middleware]
        M9 --> M10[HumanInTheLoopMiddleware]
    end

    subgraph "鍐呯疆 Tools"
        T1[ls]
        T2[read_file]
        T3[write_file]
        T4[edit_file]
        T5[glob]
        T6[grep]
        T7[execute]
        T8[write_todos]
        T9[task - 瀛愪唬鐞哴
    end

    C -.-> M1
    E -.-> T1 & T2 & T3 & T4 & T5 & T6 & T7 & T8 & T9
</pre>

## 涓讳唬鐞?+ 瀛愪唬鐞嗘灦鏋?
<pre class="mermaid">
graph TB
    subgraph "Main Agent (Orchestrator)"
        MA[涓讳唬鐞?LLM]
        MA_TOOLS[鍐呯疆 Tools + 鐢ㄦ埛 Tools]
        MA_TASK[task tool]
    end

    subgraph "General-Purpose SubAgent"
        GP[閫氱敤瀛愪唬鐞?LLM]
        GP_TOOLS[缁ф壙涓讳唬鐞嗗叏閮?Tools]
        GP_MW[鐙珛 Middleware 鏍圿
    end

    subgraph "Custom SubAgent N"
        CS[鑷畾涔夊瓙浠ｇ悊 LLM]
        CS_TOOLS[鑷畾涔?Tools]
        CS_MW[鐙珛 Middleware 鏍圿
    end

    MA --> MA_TASK
    MA_TASK -- "subagent_type=general-purpose" --> GP
    MA_TASK -- "subagent_type=custom" --> CS
    GP -- "ToolMessage (鏈€缁堢粨鏋?" --> MA
    CS -- "ToolMessage (鏈€缁堢粨鏋?" --> MA
</pre>

---

## Backends (鍚庣瀛樺偍灞?

Backend 鏄?Deep Agents 鐨勬枃浠跺瓨鍌ㄦ娊璞″眰銆傛墍鏈夊悗绔疄鐜?`BackendProtocol` 鎺ュ彛锛屾彁渚涚粺涓€鐨勬枃浠舵搷浣?API锛屼娇涓婂眰 Middleware 鍜?Tools 鏃犻渶鍏冲績搴曞眰瀛樺偍缁嗚妭銆?
| Backend | 绫诲悕 | 瀛樺偍浣嶇疆 | 鎸佷箙鎬?| 鎵ц鑳藉姏 | 鍏稿瀷鍦烘櫙 |
| --- | --- | --- | --- | --- | --- |
| State | `StateBackend` | LangGraph agent state | 浼氳瘽鍐呬复鏃?| 鉂?| 榛樿鍚庣锛岃交閲忎复鏃舵枃浠?|
| Filesystem | `FilesystemBackend` | 鏈湴纾佺洏 | 姘镐箙 | 鉂?| 鏈湴寮€鍙?CLI |
| LocalShell | `LocalShellBackend` | 鏈湴纾佺洏 + Shell | 姘镐箙 | 鉁?`execute()` | 鏈湴寮€鍙?+ 鍛戒护鎵ц |
| Store | `StoreBackend` | LangGraph BaseStore | 璺ㄤ細璇濇寔涔?| 鉂?| 鎸佷箙鍖栬蹇?鏂囦欢 |
| Sandbox | `BaseSandbox` (鎶借薄) | 杩滅▼娌欑鐜 | 鍙栧喅浜庡疄鐜?| 鉁?`execute()` | Docker/VM 闅旂鎵ц |
| Composite | `CompositeBackend` | 璺敱鍒板涓悗绔?| 鍙栧喅浜庡瓙鍚庣 | 鍙栧喅浜?default | 娣峰悎瀛樺偍绛栫暐 |

### 绫荤户鎵垮叧绯?
<pre class="mermaid">
classDiagram
    class BackendProtocol {
        <<abstract>>
        +ls_info(path) list~FileInfo~
        +read(file_path, offset, limit) str
        +write(file_path, content) WriteResult
        +edit(file_path, old, new, replace_all) EditResult
        +grep_raw(pattern, path, glob) list~GrepMatch~ | str
        +glob_info(pattern, path) list~FileInfo~
        +upload_files(files) list~FileUploadResponse~
        +download_files(paths) list~FileDownloadResponse~
    }

    class SandboxBackendProtocol {
        <<abstract>>
        +id str
        +execute(command, timeout) ExecuteResponse
        +aexecute(command, timeout) ExecuteResponse
    }

    class StateBackend {
        +runtime: ToolRuntime
        -璇诲啓 runtime.state 涓殑 files 瀛楀吀
        -write/edit 杩斿洖 files_update
    }

    class FilesystemBackend {
        +cwd: Path
        +virtual_mode: bool
        +max_file_size_bytes: int
        -_resolve_path(key) Path
        -_to_virtual_path(path) str
        -_ripgrep_search() dict
        -_python_search() dict
    }

    class LocalShellBackend {
        +_default_timeout: int
        +_max_output_bytes: int
        +_env: dict
        +_sandbox_id: str
        +execute(command, timeout) ExecuteResponse
    }

    class StoreBackend {
        +runtime: ToolRuntime
        -_namespace: NamespaceFactory
        -_get_store() BaseStore
        -_get_namespace() tuple
        -_search_store_paginated() list~Item~
    }

    class BaseSandbox {
        <<abstract>>
        +execute(command, timeout) ExecuteResponse*
        +id str*
        +upload_files()*
        +download_files()*
        -鎵€鏈夋枃浠舵搷浣滈€氳繃 execute() 濮旀墭
    }

    class CompositeBackend {
        +default: BackendProtocol
        +routes: dict
        +sorted_routes: list
        -_get_backend_and_key(key) tuple
    }

    BackendProtocol <|-- SandboxBackendProtocol
    BackendProtocol <|-- StateBackend
    BackendProtocol <|-- FilesystemBackend
    BackendProtocol <|-- StoreBackend
    BackendProtocol <|-- CompositeBackend
    SandboxBackendProtocol <|-- BaseSandbox
    FilesystemBackend <|-- LocalShellBackend
    SandboxBackendProtocol <|-- LocalShellBackend
</pre>

娉ㄦ剰 `LocalShellBackend` 鏄弻閲嶇户鎵匡細瀹冨悓鏃剁户鎵?`FilesystemBackend`锛堟枃浠舵搷浣滐級鍜?`SandboxBackendProtocol`锛圫hell 鎵ц鑳藉姏锛夈€?
### BackendProtocol 鏍稿績鎺ュ彛

`BackendProtocol` 鏄墍鏈夊悗绔殑鍩虹被 (ABC)锛屽畾涔変簡缁熶竴鐨勬枃浠舵搷浣滄帴鍙ｃ€傛瘡涓柟娉曢兘鏈夊搴旂殑 async 鐗堟湰 (`als_info`, `aread`, `awrite` 绛?锛岄粯璁ら€氳繃 `asyncio.to_thread()` 濮旀墭鍒板悓姝ュ疄鐜般€?
```python
## 鏍稿績鏂囦欢鎿嶄綔鎺ュ彛
ls_info(path: str) -> list[FileInfo]                          # 鍒楀嚭鐩綍 (闈為€掑綊)
read(file_path: str, offset=0, limit=2000) -> str             # 璇诲彇鏂囦欢 (甯﹁鍙? cat -n 鏍煎紡)
write(file_path: str, content: str) -> WriteResult            # 鍒涘缓鏂版枃浠?(宸插瓨鍦ㄥ垯鎶ラ敊)
edit(file_path: str, old: str, new: str, replace_all=False) -> EditResult  # 绮剧‘瀛楃涓叉浛鎹?grep_raw(pattern: str, path=None, glob=None) -> list[GrepMatch] | str     # 鏂囨湰鎼滅储 (literal, 闈?regex)
glob_info(pattern: str, path="/") -> list[FileInfo]           # Glob 妯″紡鏂囦欢鎼滅储
upload_files(files: list[tuple[str, bytes]]) -> list[FileUploadResponse]   # 鎵归噺涓婁紶
download_files(paths: list[str]) -> list[FileDownloadResponse]             # 鎵归噺涓嬭浇
```

---

## Middleware (涓棿浠跺眰)

涓棿浠跺湪 agent loop 鐨勪笉鍚岄樁娈垫敞鍏ラ€昏緫锛屾寜鏍堥『搴忔墽琛屻€?
| Middleware | 绫诲悕 | 鍔熻兘 | 娉ㄥ叆鐨?Tools | 淇敼 System Prompt |
|-----------|------|------|-------------|-------------------|
| TodoList | `TodoListMiddleware` | 浠诲姟瑙勫垝涓庤窡韪?| `write_todos` | 鉁?|
| Memory | `MemoryMiddleware` | 鍔犺浇 AGENTS.md 璁板繂鏂囦欢 | 鏃?(澶嶇敤 edit_file) | 鉁?娉ㄥ叆璁板繂鍐呭 |
| Skills | `SkillsMiddleware` | 鍔犺浇 SKILL.md 鎶€鑳藉簱 | 鏃?(澶嶇敤 read_file) | 鉁?娉ㄥ叆鎶€鑳藉垪琛?|
| Filesystem | `FilesystemMiddleware` | 鏂囦欢绯荤粺鎿嶄綔 | `ls` `read_file` `write_file` `edit_file` `glob` `grep` `execute` | 鉁?|
| SubAgent | `SubAgentMiddleware` | 瀛愪唬鐞嗚皟搴?| `task` | 鉁?|
| Summarization | `SummarizationMiddleware` | 瀵硅瘽鍘嗗彶鎽樿 + 鍗歌浇 | 鏃?| 鉁?(鎽樿娉ㄥ叆) |
| AnthropicPromptCaching | `AnthropicPromptCachingMiddleware` | Anthropic 妯″瀷 prompt 缂撳瓨浼樺寲 | 鏃?| 鍚?|
| PatchToolCalls | `PatchToolCallsMiddleware` | 淇ˉ鎮┖鐨?tool calls | 鏃?| 鍚?|
| HumanInTheLoop | `HumanInTheLoopMiddleware` | 浜哄伐瀹℃壒涓柇 | 鏃?| 鍚?|

---

## Tools 鎬昏

| Tool | 鏉ユ簮 | 鎻忚堪 |
|------|------|------|
| `ls` | FilesystemMiddleware | 鍒楀嚭鐩綍鍐呭 |
| `read_file` | FilesystemMiddleware | 璇诲彇鏂囦欢 (鏀寔鍒嗛〉銆佸浘鐗? |
| `write_file` | FilesystemMiddleware | 鍒涘缓鏂版枃浠?|
| `edit_file` | FilesystemMiddleware | 绮剧‘瀛楃涓叉浛鎹㈢紪杈?|
| `glob` | FilesystemMiddleware | Glob 妯″紡鏂囦欢鎼滅储 |
| `grep` | FilesystemMiddleware | 鏂囨湰鍐呭鎼滅储 (literal, 闈?regex) |
| `execute` | FilesystemMiddleware | Shell 鍛戒护鎵ц (闇€ SandboxBackend) |
| `write_todos` | TodoListMiddleware | 绠＄悊浠诲姟鍒楄〃 |
| `task` | SubAgentMiddleware | 鍚姩瀛愪唬鐞嗘墽琛岄殧绂讳换鍔?|

---

## 瀹屾暣鏁版嵁娴?
<pre class="mermaid">
graph TB
    subgraph "杈撳叆灞?
        USER[鐢ㄦ埛] --> |"invoke(messages, files)"| AGENT
    end

    subgraph "Agent 鏍稿績"
        AGENT[create_deep_agent] --> |"model + middleware + tools"| GRAPH[CompiledStateGraph]
        GRAPH --> LOOP{Agent Loop}
    end

    subgraph "Middleware 澶勭悊"
        LOOP --> MW_BEFORE[before_agent<br/>PatchToolCalls / Memory / Skills 鍔犺浇]
        MW_BEFORE --> MW_WRAP[wrap_model_call<br/>System Prompt 娉ㄥ叆]
        MW_WRAP --> LLM[LLM 鎺ㄧ悊<br/>Claude / GPT / etc.]
        LLM --> MW_AFTER[after_model_call<br/>Summarization 妫€鏌
    end

    subgraph "宸ュ叿鎵ц"
        LLM --> |tool_calls| TOOL_NODE[Tool Node]
        TOOL_NODE --> FS_TOOLS[鏂囦欢绯荤粺宸ュ叿<br/>ls/read/write/edit/glob/grep]
        TOOL_NODE --> EXEC[execute<br/>Shell 鍛戒护]
        TOOL_NODE --> TASK[task<br/>瀛愪唬鐞哴
        TOOL_NODE --> TODO[write_todos<br/>浠诲姟绠＄悊]
        TOOL_NODE --> CUSTOM[鐢ㄦ埛鑷畾涔?Tools]
    end

    subgraph "鍚庣瀛樺偍"
        FS_TOOLS & EXEC --> BACKEND{Backend}
        BACKEND --> B_STATE[StateBackend<br/>涓存椂 State]
        BACKEND --> B_FS[FilesystemBackend<br/>鏈湴纾佺洏]
        BACKEND --> B_SHELL[LocalShellBackend<br/>纾佺洏 + Shell]
        BACKEND --> B_STORE[StoreBackend<br/>鎸佷箙 Store]
        BACKEND --> B_COMP[CompositeBackend<br/>璺敱缁勫悎]
    end

    subgraph "瀛愪唬鐞?
        TASK --> SUB_GP[General-Purpose<br/>閫氱敤瀛愪唬鐞哴
        TASK --> SUB_CUSTOM[鑷畾涔夊瓙浠ｇ悊]
        SUB_GP --> |ToolMessage| LOOP
        SUB_CUSTOM --> |ToolMessage| LOOP
    end

    MW_AFTER --> |缁х画寰幆| LOOP
    LOOP --> |瀹屾垚| RESULT[鏈€缁堝搷搴擼
    RESULT --> USER
</pre>

---

**鏈郴鍒楁枃绔狅細**
- [Deep Agents 鏋舵瀯鍏ㄦ櫙](/blog/2026/02/26/deepagents-architecture/)锛堟湰鏂囷級
- [Deep Agents Human-in-the-Loop 鏈哄埗璇﹁В](/blog/2026/02/26/deepagents-hitl/)
- [Deep Agents Memory 鏈哄埗璇﹁В](/blog/2026/02/26/deepagents-memory/)
- [Deep Agents General-Purpose SubAgent 璇﹁В](/blog/2026/02/26/deepagents-subagent/)
