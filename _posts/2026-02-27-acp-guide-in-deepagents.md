---
layout: post
date: 2026-02-27
title: "Agent Client Protocol (ACP) 鍦?Deep Agents 涓殑浣跨敤鎸囧崡"
categories: tech_coding
tags:
  - DeepAgents
  - AIAgent
  - ACP
  - Zed
  - LangGraph
---

## Agent Client Protocol (ACP) 鍦?Deep Agents 涓殑浣跨敤鎸囧崡

## 浠€涔堟槸 ACP锛?
Agent Client Protocol (ACP) 鏄敱 [Zed Industries](https://zed.dev/) 鍙戣捣鐨勫紑鏀炬爣鍑嗗崗璁紝鐢ㄤ簬鏍囧噯鍖栦唬鐮佺紪杈戝櫒/IDE 涓?AI 缂栫爜 Agent 涔嬮棿鐨勯€氫俊銆傚畠鐨勫畾浣嶇被浼间簬 Language Server Protocol (LSP) 涔嬩簬璇█鏈嶅姟鍣ㄢ€斺€擜CP 灏?Agent 涓庣紪杈戝櫒瑙ｈ€︼紝浣垮緱浠讳綍 ACP 鍏煎鐨?Agent 鍙互鍦ㄤ换浣曟敮鎸?ACP 鐨勭紪杈戝櫒涓繍琛屻€?
鍗忚瀹樻柟缃戠珯锛歔agentclientprotocol.com](https://agentclientprotocol.com/)

### 鏍稿績浠峰€?
- 娑堥櫎闆嗘垚寮€閿€锛氫笉鍐嶉渶瑕佷负姣忎釜 Agent-缂栬緫鍣ㄧ粍鍚堝仛瀹氬埗寮€鍙?- 骞挎硾鍏煎锛欰gent 鍙渶瀹炵幇涓€娆?ACP锛屽嵆鍙湪鎵€鏈夊吋瀹圭紪杈戝櫒涓娇鐢?- 寮€鍙戣€呰嚜鐢憋細鐢ㄦ埛鍙互鑷敱閫夋嫨 Agent 鍜岀紪杈戝櫒鐨勭粍鍚?
### 閫氫俊妯″瀷

ACP 鍩轰簬 JSON-RPC 2.0 瑙勮寖锛屾敮鎸佷袱绉嶆秷鎭被鍨嬶細

- Methods锛堟柟娉曪級锛氳姹?鍝嶅簲瀵癸紝鏈熸湜杩斿洖缁撴灉鎴栭敊璇?- Notifications锛堥€氱煡锛夛細鍗曞悜娑堟伅锛屼笉鏈熸湜鍝嶅簲

鏀寔鏈湴锛坰tdio锛夊拰杩滅▼锛圚TTP/WebSocket锛変袱绉嶉儴缃叉ā寮忋€?
## ACP 鍗忚鐢熷懡鍛ㄦ湡

涓€涓吀鍨嬬殑 ACP 浼氳瘽娴佺▼濡備笅锛?
<pre class="mermaid">
sequenceDiagram
    participant C as Client (缂栬緫鍣?
    participant A as Agent (鏈嶅姟绔?

    rect rgb(240, 248, 255)
        Note over C,A: 1. 鍒濆鍖栭樁娈?        C->>A: initialize (鍗忓晢鐗堟湰鍜岃兘鍔?
        A-->>C: InitializeResponse
    end

    rect rgb(240, 255, 240)
        Note over C,A: 2. 浼氳瘽寤虹珛
        C->>A: session/new (鍒涘缓浼氳瘽)
        A-->>C: NewSessionResponse (session_id)
    end

    rect rgb(255, 248, 240)
        Note over C,A: 3. Prompt 鍥炲悎
        C->>A: session/prompt (鍙戦€佺敤鎴锋秷鎭?
        A-)C: session/update (娴佸紡鏂囨湰)
        A-)C: session/update (宸ュ叿璋冪敤閫氱煡)
        A->>C: request_permission (鏉冮檺璇锋眰)
        C-->>A: permission response (approve/reject)
        A-)C: session/update (鏇村杩涘害)
        A-->>C: PromptResponse (鍥炲悎缁撴潫)
    end

    rect rgb(255, 240, 240)
        Note over C,A: 4. 鍙栨秷 (鍙€?
        C-)A: session/cancel (涓柇澶勭悊)
    end
</pre>

### Agent 绔柟娉?
| 鏂规硶 | 璇存槑 |
|------|------|
| `initialize` | 鍗忓晢鍗忚鐗堟湰锛屼氦鎹㈣兘鍔涘０鏄?|
| `session/new` | 鍒涘缓鏂扮殑瀵硅瘽浼氳瘽 |
| `session/prompt` | 鎺ユ敹鐢ㄦ埛娑堟伅骞跺鐞?|
| `session/set_mode` | 鍒囨崲 Agent 杩愯妯″紡锛堝彲閫夛級 |
| `session/cancel` | 鍙栨秷姝ｅ湪杩涜鐨勬搷浣滐紙閫氱煡锛?|

### Client 绔柟娉?
| 鏂规硶 | 璇存槑 |
|------|------|
| `session/request_permission` | 璇锋眰鐢ㄦ埛鎺堟潈宸ュ叿璋冪敤 |
| `session/update` | 鍙戦€佷細璇濇洿鏂伴€氱煡锛堟秷鎭€佸伐鍏疯皟鐢ㄣ€佽鍒掔瓑锛?|
| `fs/read_text_file` | 璇诲彇鏂囦欢鍐呭锛堝彲閫夛級 |
| `fs/write_text_file` | 鍐欏叆鏂囦欢鍐呭锛堝彲閫夛級 |
| `terminal/*` | 缁堢鎿嶄綔绯诲垪锛堝彲閫夛級 |


## Deep Agents 涓殑 ACP 鏋舵瀯

鍦?Deep Agents monorepo 涓紝ACP 闆嗘垚浣嶄簬 `libs/acp/` 鍖咃紙`deepagents-acp`锛夈€傚畠鐨勬牳蹇冧綔鐢ㄦ槸灏?Deep Agents SDK 鏋勫缓鐨?LangGraph Agent 妗ユ帴鍒?ACP 鍗忚锛屼娇鍏跺彲浠ュ湪 Zed 绛夋敮鎸?ACP 鐨勭紪杈戝櫒涓繍琛屻€?
### 鍖呯粨鏋?
```
libs/acp/
鈹溾攢鈹€ deepagents_acp/
鈹?  鈹溾攢鈹€ __init__.py          # 鍖呭叆鍙?鈹?  鈹溾攢鈹€ __main__.py          # 妯″潡鍏ュ彛鐐癸紙python -m deepagents_acp锛?鈹?  鈹溾攢鈹€ server.py            # 鏍稿績锛欰gentServerACP 绫?鈹?  鈹斺攢鈹€ utils.py             # 鍐呭鍧楄浆鎹€佸懡浠よВ鏋愮瓑宸ュ叿鍑芥暟
鈹溾攢鈹€ examples/
鈹?  鈹溾攢鈹€ demo_agent.py        # 瀹屾暣鐨?demo Agent锛堝惈妯″紡鍒囨崲銆丠ITL锛?鈹?  鈹斺攢鈹€ local_context.py     # 鏈湴涓婁笅鏂囦腑闂翠欢锛堟娴嬮」鐩幆澧冿級
鈹溾攢鈹€ tests/
鈹溾攢鈹€ pyproject.toml
鈹斺攢鈹€ run_demo_agent.sh        # Zed 鍚姩鑴氭湰
```

### 鏍稿績绫伙細`AgentServerACP`

`AgentServerACP` 鏄暣涓?ACP 闆嗘垚鐨勬牳蹇冿紝瀹冪户鎵胯嚜 `acp.Agent`锛圓CP Python SDK 鎻愪緵鐨勫熀绫伙級锛岃礋璐ｏ細

1. 瀹炵幇 ACP 鍗忚鐨勬墍鏈夌敓鍛藉懆鏈熸柟娉?2. 灏?ACP 鍐呭鍧楋紙鏂囨湰銆佸浘鐗囥€佽祫婧愮瓑锛夎浆鎹负 LangChain 鏍煎紡
3. 娴佸紡浼犺緭 Agent 鍝嶅簲鍒板鎴风
4. 澶勭悊 Human-in-the-Loop (HITL) 鏉冮檺璇锋眰
5. 绠＄悊浼氳瘽銆佽鍒掞紙Plan锛夊拰宸ュ叿璋冪敤鐘舵€?
```python
class AgentServerACP(ACPAgent):
    """ACP agent server that bridges Deep Agents with the Agent Client Protocol."""

    def __init__(
        self,
        agent: CompiledStateGraph | Callable[[AgentSessionContext], CompiledStateGraph],
        *,
        modes: SessionModeState | None = None,
    ) -> None:
        ...
```

鏋勯€犲嚱鏁版帴鍙椾袱绉嶅舰寮忕殑 Agent锛?- 鐩存帴浼犲叆涓€涓紪璇戝ソ鐨?`CompiledStateGraph`锛堢畝鍗曞満鏅級
- 浼犲叆涓€涓伐鍘傚嚱鏁?`Callable[[AgentSessionContext], CompiledStateGraph]`锛堥渶瑕佹牴鎹細璇濅笂涓嬫枃鍔ㄦ€佸垱寤?Agent锛?
### 鏋舵瀯灞傛鍥?
<pre class="mermaid">
graph TB
    subgraph Editor["缂栬緫鍣?(Zed 绛? 鈥?ACP Client"]
        UI[鐢ㄦ埛鐣岄潰]
    end

    UI <-->|"JSON-RPC 2.0 (stdio)"| ACP

    subgraph Server["AgentServerACP"]
        ACP["ACP 鍗忚灞?br/>initialize / new_session / prompt<br/>set_session_mode / cancel<br/>request_permission / session_update"]
        ACP -->|"鍐呭鍧楄浆鎹?(utils.py)"| SDK

        SDK["Deep Agents SDK (LangGraph)<br/>create_deep_agent()<br/>CompiledStateGraph.astream()"]
        SDK --- MW["涓棿浠舵爤"]
        MW --- HITL[HumanInTheLoopMiddleware]
        MW --- FS[FilesystemMiddleware]
        MW --- MEM[MemoryMiddleware]
        MW --- TODO[TodoListMiddleware]
    end

    style Editor fill:#e8f4fd,stroke:#4a90d9
    style Server fill:#f0f7e8,stroke:#6ab04c
    style ACP fill:#fff3e0,stroke:#f5a623
    style SDK fill:#fce4ec,stroke:#e57373
</pre>

### 鍏抽敭娴佺▼璇﹁В

#### 1. 浼氳瘽鍒涘缓 (`new_session`)

褰撶紪杈戝櫒鎵撳紑涓€涓柊鐨?Agent 绾跨▼鏃讹紝璋冪敤 `new_session`锛屼紶鍏ュ伐浣滅洰褰?`cwd` 鍜屽彲閫夌殑 MCP 鏈嶅姟鍣ㄥ垪琛ㄣ€傛湇鍔＄鐢熸垚鍞竴鐨?`session_id` 骞惰繑鍥炪€?
濡傛灉閰嶇疆浜?`modes`锛堣繍琛屾ā寮忥級锛屼細鍦ㄥ搷搴斾腑杩斿洖鍙敤妯″紡鍒楄〃銆?
#### 2. 娑堟伅澶勭悊 (`prompt`)

杩欐槸鏈€鏍稿績鐨勬柟娉曘€傚綋鐢ㄦ埛鍙戦€佹秷鎭椂锛?
1. 灏?ACP 鍐呭鍧楋紙`TextContentBlock`銆乣ImageContentBlock` 绛夛級杞崲涓?LangChain 鐨勫妯℃€佸唴瀹规牸寮?2. 閫氳繃 `agent.astream()` 娴佸紡鎵ц Agent
3. 瀹炴椂灏?Agent 鐨勬枃鏈緭鍑恒€佸伐鍏疯皟鐢ㄩ€氳繃 `session/update` 閫氱煡鎺ㄩ€佺粰瀹㈡埛绔?4. 閬囧埌涓柇锛坕nterrupt锛夋椂锛岄€氳繃 `request_permission` 璇锋眰鐢ㄦ埛鎺堟潈
5. 澶勭悊鐢ㄦ埛鐨勬巿鏉冨喅绛栵紙approve/reject/approve_always锛夛紝鐒跺悗鎭㈠鎵ц

#### 3. Human-in-the-Loop (HITL)

ACP 闆嗘垚鏀寔绮剧粏鐨勬潈闄愭帶鍒躲€傞€氳繃 `interrupt_on` 閰嶇疆锛屽彲浠ユ寚瀹氬摢浜涘伐鍏疯皟鐢ㄩ渶瑕佺敤鎴风‘璁わ細

```python
interrupt_config = {
    "edit_file": {"allowed_decisions": ["approve", "reject"]},
    "write_file": {"allowed_decisions": ["approve", "reject"]},
    "execute": {"allowed_decisions": ["approve", "reject"]},
}
```

鏉冮檺璇锋眰鏀寔涓夌鍐崇瓥锛?- `approve`锛氭壒鍑嗘湰娆℃搷浣?- `reject`锛氭嫆缁濇湰娆℃搷浣?- `approve_always`锛氬缁堟壒鍑嗗悓绫绘搷浣滐紙鍩轰簬鍛戒护绛惧悕鐨勬櫤鑳藉尮閰嶏級

#### 4. 杩愯妯″紡 (`modes`)

Agent 鍙互瀹氫箟澶氱杩愯妯″紡锛岀敤鎴峰彲浠ュ湪缂栬緫鍣ㄤ腑鍒囨崲锛?
```python
modes = SessionModeState(
    current_mode_id="accept_edits",
    available_modes=[
        SessionMode(id="ask_before_edits", name="Ask before edits", ...),
        SessionMode(id="accept_edits", name="Accept edits", ...),
        SessionMode(id="accept_everything", name="Accept everything", ...),
    ],
)
```

鍒囨崲妯″紡鏃讹紝Agent 浼氳閲嶇疆骞朵娇鐢ㄦ柊妯″紡鐨勯厤缃噸鏂板垱寤恒€?
#### 5. 璁″垝绠＄悊 (Plan/Todos)

ACP 鏀寔鍚戝鎴风灞曠ず Agent 鐨勬墽琛岃鍒掋€傚綋 Agent 璋冪敤 `write_todos` 宸ュ叿鏃讹紝璁″垝浼氶€氳繃 `AgentPlanUpdate` 鎺ㄩ€佸埌缂栬緫鍣?UI锛?
```python
## 璁″垝鏉＄洰鍖呭惈鍐呭銆佺姸鎬佸拰浼樺厛绾?PlanEntry(content="瀹炵幇鐢ㄦ埛璁よ瘉", status="in_progress", priority="medium")
```

璁″垝鏇存柊鏈夋櫤鑳界殑鑷姩鎵瑰噯閫昏緫鈥斺€斿鏋滃凡鏈変竴涓繘琛屼腑鐨勮鍒掞紝鍚庣画鐨勮鍒掓洿鏂颁細鑷姩鎵瑰噯锛屾棤闇€鐢ㄦ埛鍐嶆纭銆?

## 濡備綍浣跨敤锛氬揩閫熶笂鎵?
### 鍓嶇疆鏉′欢

- Python 3.11+
- [uv](https://docs.astral.sh/uv/) 鍖呯鐞嗗櫒
- 涓€涓敮鎸?ACP 鐨勭紪杈戝櫒锛堢洰鍓嶄富瑕佹槸 [Zed](https://zed.dev/)锛?- Anthropic API Key锛堟垨鍏朵粬 LLM 鎻愪緵鍟嗙殑 Key锛?
### 瀹夎

```bash
uv add deepagents-acp
```

### 绀轰緥 1锛氭渶绠€鍗曠殑 ACP Agent

杩欐槸鍒涘缓涓€涓?ACP Agent 鐨勬渶灏忎唬鐮侊細

```python
import asyncio

from acp import run_agent
from deepagents import create_deep_agent
from langgraph.checkpoint.memory import MemorySaver

from deepagents_acp.server import AgentServerACP


async def main() -> None:
    agent = create_deep_agent(
        # 榛樿浣跨敤 Claude Sonnet锛屼篃鍙互鎸囧畾鍏朵粬妯″瀷
        checkpointer=MemorySaver(),
    )
    server = AgentServerACP(agent)
    await run_agent(server)


if __name__ == "__main__":
    asyncio.run(main())
```

杩欎釜 Agent 浼氳嚜甯?Deep Agents 鐨勫唴缃伐鍏凤紙鏂囦欢璇诲啓銆乻hell 鎵ц绛夛級锛屽彲浠ョ洿鎺ュ湪缂栬緫鍣ㄤ腑杩涜浠ｇ爜缂栬緫銆?
### 绀轰緥 2锛氭坊鍔犺嚜瀹氫箟宸ュ叿

```python
import asyncio

from acp import run_agent
from deepagents import create_deep_agent
from langgraph.checkpoint.memory import MemorySaver

from deepagents_acp.server import AgentServerACP


async def get_weather(city: str) -> str:
    """Get weather for a given city."""
    return f"It's always sunny in {city}!"


async def search_docs(query: str) -> str:
    """Search internal documentation."""
    return f"Found 3 results for: {query}"


async def main() -> None:
    agent = create_deep_agent(
        tools=[get_weather, search_docs],
        system_prompt="You are a helpful coding assistant with access to weather and docs.",
        checkpointer=MemorySaver(),
    )
    server = AgentServerACP(agent)
    await run_agent(server)


if __name__ == "__main__":
    asyncio.run(main())
```

### 绀轰緥 3锛氬甫妯″紡鍒囨崲鍜?HITL 鐨勫畬鏁?Agent

杩欐槸涓€涓洿瀹屾暣鐨勭ず渚嬶紝灞曠ず浜嗗浣曢厤缃繍琛屾ā寮忓拰鏉冮檺鎺у埗锛?
```python
import asyncio

from acp import run_agent
from acp.schema import SessionMode, SessionModeState
from deepagents import create_deep_agent
from deepagents.backends import CompositeBackend, LocalShellBackend, StateBackend
from langgraph.checkpoint.memory import MemorySaver
from langgraph.graph.state import CompiledStateGraph
from langgraph.prebuilt import ToolRuntime

from deepagents_acp.server import AgentServerACP, AgentSessionContext


def get_interrupt_config(mode_id: str) -> dict:
    """鏍规嵁妯″紡杩斿洖涓嶅悓鐨勬潈闄愭帶鍒堕厤缃€?""
    configs = {
        "supervised": {
            "edit_file": {"allowed_decisions": ["approve", "reject"]},
            "write_file": {"allowed_decisions": ["approve", "reject"]},
            "execute": {"allowed_decisions": ["approve", "reject"]},
            "write_todos": {"allowed_decisions": ["approve", "reject"]},
        },
        "semi_auto": {
            "execute": {"allowed_decisions": ["approve", "reject"]},
            "write_todos": {"allowed_decisions": ["approve", "reject"]},
        },
        "autonomous": {},
    }
    return configs.get(mode_id, {})


async def main() -> None:
    checkpointer = MemorySaver()

    def build_agent(context: AgentSessionContext) -> CompiledStateGraph:
        """鏍规嵁浼氳瘽涓婁笅鏂囧姩鎬佸垱寤?Agent銆?""
        interrupt_config = get_interrupt_config(context.mode)

        def create_backend(tr: ToolRuntime | None = None) -> CompositeBackend:
            ephemeral = StateBackend(tr) if tr is not None else None
            shell = LocalShellBackend(root_dir=context.cwd, inherit_env=True)
            return CompositeBackend(
                default=shell,
                routes={"/memories/": ephemeral, "/conversation_history/": ephemeral}
                if ephemeral
                else {},
            )

        return create_deep_agent(
            checkpointer=checkpointer,
            backend=create_backend,
            interrupt_on=interrupt_config,
        )

    modes = SessionModeState(
        current_mode_id="semi_auto",
        available_modes=[
            SessionMode(
                id="supervised",
                name="鐩戠潱妯″紡",
                description="鎵€鏈夋枃浠剁紪杈戝拰鍛戒护鎵ц閮介渶瑕佺‘璁?,
            ),
            SessionMode(
                id="semi_auto",
                name="鍗婅嚜鍔ㄦā寮?,
                description="鑷姩鎵ц鏂囦欢缂栬緫锛屽懡浠ゆ墽琛岄渶瑕佺‘璁?,
            ),
            SessionMode(
                id="autonomous",
                name="鑷富妯″紡",
                description="鎵€鏈夋搷浣滆嚜鍔ㄦ墽琛?,
            ),
        ],
    )

    server = AgentServerACP(agent=build_agent, modes=modes)
    await run_agent(server)


if __name__ == "__main__":
    asyncio.run(main())
```

### 鍦?Zed 涓厤缃?
鍒涘缓濂?Agent 鑴氭湰鍚庯紝鍦?Zed 鐨?`settings.json` 涓坊鍔狅細

```json
{
  "agent_servers": {
    "MyAgent": {
      "type": "custom",
      "command": "/absolute/path/to/run_agent.sh"
    }
  }
}
```

鍚姩鑴氭湰 `run_agent.sh`锛?
```bash
#!/bin/bash
SCRIPT_DIR="$(dirname "$0")"
uv run --project "$SCRIPT_DIR" python "$SCRIPT_DIR/my_agent.py"
```

涔熷彲浠ヤ娇鐢?[Toad](https://github.com/nichochar/toad) 宸ュ叿蹇€熷惎鍔細

```bash
uv tool install -U batrachian-toad --python 3.14
toad acp "uv run python my_agent.py" .
```

## 宸ュ叿璋冪敤鐨?UI 灞曠ず

`AgentServerACP` 浼氭牴鎹伐鍏风被鍨嬭嚜鍔ㄧ敓鎴愬悎閫傜殑 UI 灞曠ず锛?
| 宸ュ叿鍚?| UI 灞曠ず | ToolKind |
|--------|---------|----------|
| `read_file` | `Read \`path/to/file\`` | `read` |
| `edit_file` | `Edit \`path/to/file\``锛堝惈 diff 棰勮锛?| `edit` |
| `write_file` | `Write \`path/to/file\`` | `edit` |
| `execute` | `Execute: \`command\`` | `execute` |
| `ls` / `glob` / `grep` | 鎼滅储绫诲睍绀?| `search` |
| 鍏朵粬宸ュ叿 | 宸ュ叿鍚嶇О | `other` |

瀵逛簬 `edit_file`锛屽鏋滄彁渚涗簡 `old_string` 鍜?`new_string`锛屼細鑷姩鐢熸垚 diff 鍐呭灞曠ず缁欑敤鎴枫€?
## 鍛戒护绛惧悕涓庢櫤鑳芥潈闄?
褰撶敤鎴烽€夋嫨 "Always allow" 鏌愪釜鍛戒护鏃讹紝绯荤粺浼氭彁鍙栧懡浠ょ鍚嶈繘琛屽尮閰嶏紝鑰屼笉鏄畝鍗曞湴鍖归厤鏁翠釜鍛戒护瀛楃涓层€傝繖鏍锋棦瀹夊叏鍙堟柟渚匡細

```python
## 鍛戒护绛惧悕鎻愬彇绀轰緥
"npm install"           鈫?"npm install"
"python -m pytest -q"   鈫?"python -m pytest"
"uv run ruff check ."   鈫?"uv run ruff"
"node -e 'code'"        鈫?"node -e"
"cd dir && npm test"    鈫?["cd", "npm test"]
```

瀵逛簬瀹夊叏鏁忔劅鐨勫懡浠わ紙python銆乶ode銆乶pm銆乽v 绛夛級锛岀鍚嶄細鍖呭惈瀛愬懡浠や互閬垮厤杩囧害鎺堟潈銆?
## 鍐呭鍧楄浆鎹?
ACP 瀹氫箟浜嗗绉嶅唴瀹瑰潡绫诲瀷锛宍deepagents-acp` 鐨?`utils.py` 璐熻矗灏嗗畠浠浆鎹负 LangChain 鐨勫妯℃€佹牸寮忥細

| ACP 鍐呭鍧?| 杞崲缁撴灉 |
|------------|---------|
| `TextContentBlock` | `{"type": "text", "text": "..."}` |
| `ImageContentBlock` | `{"type": "image_url", "image_url": {"url": "data:...;base64,..."}}` |
| `ResourceContentBlock` | 鏂囨湰鎻忚堪锛堝惈 URI銆佹弿杩般€丮IME 绫诲瀷锛?|
| `EmbeddedResourceContentBlock` | 鍐呰仈鏂囨湰鎴?base64 鏁版嵁 |
| `AudioContentBlock` | 鏆備笉鏀寔锛堟姏鍑?`NotImplementedError`锛?|

## 涓?MCP 鐨勫叧绯?
ACP 鍜?MCP (Model Context Protocol) 鏄簰琛ョ殑鍗忚锛?
- MCP锛氭爣鍑嗗寲 LLM 涓庡閮ㄥ伐鍏?鏁版嵁婧愪箣闂寸殑閫氫俊锛圓gent 鈫?宸ュ叿锛?- ACP锛氭爣鍑嗗寲缂栬緫鍣ㄤ笌 AI Agent 涔嬮棿鐨勯€氫俊锛堢紪杈戝櫒 鈫?Agent锛?
<pre class="mermaid">
graph LR
    E["馃枼锔?缂栬緫鍣?br/>(Zed, IDE)"] <-->|ACP| A["馃 Agent<br/>(Deep Agents)"] <-->|MCP| T["馃敡 宸ュ叿/鏁版嵁<br/>(澶栭儴鏈嶅姟)"]

    style E fill:#e8f4fd,stroke:#4a90d9
    style A fill:#f0f7e8,stroke:#6ab04c
    style T fill:#fff3e0,stroke:#f5a623
</pre>

鍦?Deep Agents 涓紝`new_session` 鏂规硶鎺ュ彈 `mcp_servers` 鍙傛暟锛屽厑璁稿鎴风浼犲叆 MCP 鏈嶅姟鍣ㄩ厤缃紝Agent 鍙互鍒╃敤杩欎簺澶栭儴宸ュ叿鏉ュ寮鸿兘鍔涖€?
## 渚濊禆鍏崇郴

```
deepagents-acp
鈹溾攢鈹€ agent-client-protocol >= 0.8.0   # ACP Python SDK
鈹溾攢鈹€ deepagents                        # Deep Agents SDK (LangGraph-based)
鈹斺攢鈹€ python-dotenv >= 1.2.1            # 鐜鍙橀噺绠＄悊
```

## 鍙傝€冭祫鏂?
- [ACP 鍗忚瑙勮寖](https://agentclientprotocol.com/protocol/overview) - 鍗忚瀹屾暣瀹氫箟
- [ACP Python SDK](https://github.com/agentclientprotocol/python-sdk) - Python 瀹炵幇
- [Deep Agents 鏂囨。](https://docs.langchain.com/oss/python/deepagents/overview) - SDK 鏂囨。
- [Zed 缂栬緫鍣╙(https://zed.dev/) - 鐩墠涓昏鐨?ACP 瀹㈡埛绔疄鐜?
