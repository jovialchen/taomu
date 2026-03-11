---
layout: post
date: 2026-03-05
title: "Deep Agents 涓殑 Hook 鏈哄埗璇﹁В"
categories: tech_coding
tags:
  - LLM
  - AIAgent
  - DeepAgents
  - ProgrammingLanguage/Python
---

## Deep Agents 涓殑 Hook 鏈哄埗璇﹁В

> **鎽樿锛?* Hook锛堥挬瀛愶級鏄竴绉嶈蒋浠惰璁℃ā寮忥紝鍏佽寮€鍙戣€呭湪绋嬪簭鎵ц鐨勭壒瀹氶樁娈垫彃鍏ヨ嚜瀹氫箟閫昏緫锛岃€屾棤闇€淇敼鏍稿績浠ｇ爜銆傛湰鏂囨繁鍏ュ垎鏋?Deep Agents 椤圭洰涓殑 Hook 鏋舵瀯鍜屽疄鐜扮粏鑺傘€?
---

## 1. 浠€涔堟槸 Hook锛?
**Hook锛堥挬瀛愶級** 鏄竴绉嶈蒋浠惰璁℃ā寮忥紝鍏佽寮€鍙戣€呭湪绋嬪簭鎵ц鐨勭壒瀹氶樁娈垫彃鍏ヨ嚜瀹氫箟閫昏緫锛岃€屾棤闇€淇敼鏍稿績浠ｇ爜銆侶ook 鎻愪緵浜嗕竴绉?*鏉捐€﹀悎鐨勬墿灞曟満鍒?*锛屼娇寰楁鏋跺彲浠ュ湪棰勫畾涔夌殑鐢熷懡鍛ㄦ湡鐐逛笂璋冪敤鐢ㄦ埛鎻愪緵鐨勪唬鐮併€?
### Hook 鐨勬牳蹇冪壒鐐?
- **鎷︽埅鑳藉姏**锛氬湪鐗瑰畾鎵ц鐐瑰墠鍚庢彃鍏ラ€昏緫
- **鍔ㄦ€佷慨鏀?*锛氬彲浠ヤ慨鏀硅緭鍏ュ弬鏁般€佽繑鍥炲€兼垨鎵ц娴佺▼
- **鐘舵€佹寔涔呭寲**锛氬彲浠ュ湪澶氭璋冪敤涔嬮棿缁存姢鐘舵€?- **缁勫悎鎬?*锛氬涓?hook 鍙互閾惧紡缁勫悎浣跨敤

---

## 2. Deep Agents 椤圭洰涓殑 Hook 鏋舵瀯

鍦?Deep Agents 椤圭洰涓紝hook 鏈哄埗涓昏閫氳繃 **Middleware锛堜腑闂翠欢锛?* 妯″紡瀹炵幇銆傞」鐩殑 middleware 绯荤粺鍩轰簬 LangChain 鐨?`AgentMiddleware` 鍩虹被鏋勫缓銆?
### 2.1 鏍稿績鍩虹被锛欰gentMiddleware

鎵€鏈?middleware 閮界户鎵胯嚜 `langchain.agents.middleware.types.AgentMiddleware`锛岃绫诲畾涔変簡浠ヤ笅鏍稿績 hook 鏂规硶锛?
```python
class AgentMiddleware:
    def wrap_model_call(
        self,
        request: ModelRequest,
        call_next: Callable[[ModelRequest], Awaitable[ModelResponse]],
    ) -> Awaitable[ModelResponse]:
        """鎷︽埅姣忔 LLM 璇锋眰鐨勬牳蹇?hook"""
        pass
```

### 2.2 椤圭洰涓殑 Middleware 瀹炵幇

椤圭洰瀹炵幇浜嗗绉?middleware锛屾瘡绉嶉兘鍦ㄧ壒瀹氱殑 hook 鐐逛笂娉ㄥ叆閫昏緫锛?
| Middleware | 鏂囦欢浣嶇疆 | Hook 鐢ㄩ€?|
|------------|----------|----------|
| `FilesystemMiddleware` | `libs/deepagents/deepagents/middleware/filesystem.py` | 鍔ㄦ€佽繃婊ゅ伐鍏枫€佹敞鍏ユ枃浠剁郴缁熺姸鎬?|
| `SkillsMiddleware` | `libs/deepagents/deepagents/middleware/skills.py` | 娉ㄥ叆 skill 鎸囦护鍒?system prompt |
| `MemoryMiddleware` | `libs/deepagents/deepagents/middleware/memory.py` | 璺?turn 鐘舵€佺淮鎶?|
| `SummarizationMiddleware` | `libs/deepagents/deepagents/middleware/summarization.py` | 鎴柇鍘嗗彶娑堟伅銆佹敞鍏ユ憳瑕?|
| `SubAgentMiddleware` | `libs/deepagents/deepagents/middleware/subagents.py` | 瀛愪唬鐞嗗伐鍏锋敞鍏ュ拰璺敱 |

---

## 3. Hook 鐨勫叿浣撳疄鐜板垎鏋?
### 3.1 FilesystemMiddleware 鐨?Hook 瀹炵幇

`FilesystemMiddleware` 灞曠ず浜嗗浣曞湪 `wrap_model_call` hook 涓姩鎬佷慨鏀硅姹傦細

```python
## libs/deepagents/deepagents/middleware/filesystem.py

class FilesystemMiddleware(AgentMiddleware[FilesystemState, ToolRuntime]):
    async def wrap_model_call(
        self,
        request: ModelRequest,
        call_next: Callable[[ModelRequest], Awaitable[ModelResponse]],
    ) -> ModelResponse:
        # Hook 鐐?1: 鍔ㄦ€佽繃婊ゅ伐鍏峰垪琛?        tools = list(request.tools)
        if not self._backend_supports_execute():
            # 濡傛灉鍚庣涓嶆敮鎸?execute锛岀Щ闄よ宸ュ叿
            tools = [t for t in tools if t.name != "execute"]

        # Hook 鐐?2: 娉ㄥ叆 system prompt 涓婁笅鏂?        modified_request = self._inject_filesystem_context(request, tools)

        # 璋冪敤涓嬩竴涓?middleware 鎴栫洿鎺ヨ皟鐢ㄦā鍨?        response = await call_next(modified_request)

        # Hook 鐐?3: 澶勭悊鍝嶅簲鍚庣殑閫昏緫
        return self._process_response(response)
```

**鍏抽敭璁捐妯″紡**锛?1. **璇锋眰鎷︽埅**锛氬湪 LLM 璋冪敤鍓嶄慨鏀?`ModelRequest`
2. **宸ュ叿杩囨护**锛氭牴鎹繍琛屾椂鏉′欢鍔ㄦ€佽皟鏁村彲鐢ㄥ伐鍏?3. **涓婁笅鏂囨敞鍏?*锛氬悜 system message 娉ㄥ叆鏂囦欢绯荤粺鐘舵€?4. **鍝嶅簲澶勭悊**锛氬湪杩斿洖鍓嶅鐞嗘ā鍨嬪搷搴?
### 3.2 SkillsMiddleware 鐨?Hook 瀹炵幇

`SkillsMiddleware` 灞曠ず浜嗗浣曟敞鍏?skill 鎸囦护锛?
```python
## libs/deepagents/deepagents/middleware/skills.py

class SkillsMiddleware(AgentMiddleware[SkillsState, ToolRuntime]):
    async def wrap_model_call(
        self,
        request: ModelRequest,
        call_next: Callable[[ModelRequest], Awaitable[ModelResponse]],
    ) -> ModelResponse:
        # 浠庨厤缃殑 sources 鍔犺浇 skill 鍏冩暟鎹?        skills_metadata = await self._load_skills_metadata()

        # 鏋勫缓 skill 鎸囦护鏂囨湰
        skill_instructions = self._build_skill_instructions(skills_metadata)

        # Hook: 淇敼 system message锛岃拷鍔?skill 鎸囦护
        modified_request = append_to_system_message(
            request,
            skill_instructions,
            after_heading="### Skills Directory",
        )

        return await call_next(modified_request)
```

### 3.3 SummarizationMiddleware 鐨勫鏉?Hook 閫昏緫

杩欐槸鏈€澶嶆潅鐨?middleware锛屽睍绀轰簡澶氶樁娈?hook 澶勭悊锛?
```python
## libs/deepagents/deepagents/middleware/summarization.py

class SummarizationMiddleware(AgentMiddleware[SummarizationState, ToolRuntime]):
    async def wrap_model_call(
        self,
        request: ModelRequest,
        call_next: Callable[[ModelRequest], Awaitable[ModelResponse]],
    ) -> ModelResponse:
        # Hook 闃舵 1: Token 璁℃暟鍜屼笂涓嬫枃绐楀彛妫€鏌?        current_tokens = self._count_tokens(request.messages)
        context_limit = self._get_context_limit(request.model)

        # Hook 闃舵 2: 濡傛灉瓒呭嚭闃堝€硷紝鎵ц鎴柇鍜屾憳瑕?        if current_tokens > self._truncation_threshold:
            request = self._truncate_old_messages(request)
            request = self._inject_summary_prompt(request)

        # Hook 闃舵 3: 鍝嶅簲鍚庡鐞嗭紝鏇存柊鎽樿鐘舵€?        response = await call_next(request)
        self._update_summarization_state(response)

        return response
```

---

## 4. Hook 鐨勭姸鎬佺鐞?
Middleware 浣跨敤 `AgentState` 鏉ョ淮鎶よ法 turn 鐨勭姸鎬侊細

```python
## 瀹氫箟鐘舵€佺粨鏋?class FilesystemState(AgentState):
    files: Annotated[NotRequired[dict[str, FileData]], _file_data_reducer]
    """Files in the filesystem."""

class SkillsState(AgentState):
    skills_metadata: NotRequired[Annotated[list[SkillMetadata], PrivateStateAttr]]
    """List of loaded skill metadata from configured sources."""

class SummarizationState(AgentState):
    truncation_events: NotRequired[list[dict]]
    """History of truncation events."""
```

**鐘舵€佺壒鎬?*锛?- `PrivateStateAttr`锛氭爣璁颁负绉佹湁鐘舵€侊紝涓嶄紶鎾埌鐖?agent
- `Annotated[..., _reducer]`锛氬畾涔夌姸鎬佸悎骞剁殑 reducer 鍑芥暟
- `NotRequired`锛氱姸鎬佸瓧娈垫槸鍙€夌殑

---

## 5. Hook 鐨勬墽琛屾祦绋?
<pre class="mermaid">
flowchart TD
    A[User Message] --> B[Agent Graph<br/>create_deep_agent]
    B --> C[FilesystemMiddleware<br/>杩囨护宸ュ叿 + 娉ㄥ叆涓婁笅鏂嘳
    C --> D[SkillsMiddleware<br/>娉ㄥ叆 skill 鎸囦护]
    D --> E[SummarizationMiddleware<br/>Token 绠＄悊 + 鎴柇]
    E --> F[SubAgentMiddleware<br/>瀛愪唬鐞嗚矾鐢盷
    F --> G[LLM Model Call]
    G --> H[Response Processing]
    H --> I[Agent Response]
</pre>

---

## 6. CLI 灞傜殑 Hook 鎵╁睍

闄や簡 SDK 灞傜殑 middleware锛孋LI 杩樻湁棰濆鐨?hook 鏈哄埗锛?
### 6.1 鏈湴涓婁笅鏂?Hook (`local_context.py`)

```python
## libs/cli/deepagents_cli/local_context.py

class LocalContextMiddleware(AgentMiddleware):
    """CLI 鐗瑰畾鐨勪腑闂翠欢锛屽鐞嗘湰鍦伴」鐩笂涓嬫枃"""

    async def wrap_model_call(
        self,
        request: ModelRequest,
        call_next: Callable[[ModelRequest], Awaitable[ModelResponse]],
    ) -> ModelResponse:
        # 妫€娴嬮」鐩牴鐩綍
        project_root = self._find_project_root()

        # 娉ㄥ叆椤圭洰鐗瑰畾鐨勯厤缃?        request = self._inject_project_context(request, project_root)

        return await call_next(request)
```

### 6.2 Tool Call Hook锛堝伐鍏疯皟鐢ㄦ嫤鎴級

CLI 鍦?`textual_adapter.py` 涓疄鐜颁簡瀵瑰伐鍏疯皟鐢ㄧ殑鎷︽埅锛?
```python
## libs/cli/deepagents_cli/textual_adapter.py

async def execute_task_textual(...):
    # Hook: 鍦ㄥ伐鍏疯皟鐢ㄥ墠璇锋眰鐢ㄦ埛鎵瑰噯
    if tool_call.requires_approval:
        approved = await self._request_user_approval(tool_call)
        if not approved:
            return self._reject_tool_call(tool_call)

    # Hook: 宸ュ叿璋冪敤鍚庢洿鏂?UI
    result = await execute_tool(tool_call)
    await self._update_ui_with_tool_result(result)
```

---

## 7. Hook 鐨勫疄闄呭簲鐢ㄥ満鏅?
### 鍦烘櫙 1: 鍔ㄦ€佸伐鍏疯繃婊?
褰撳悗绔笉鏀寔鏌愪簺鍔熻兘鏃讹紝`FilesystemMiddleware` 鍦?hook 涓Щ闄ょ浉鍏冲伐鍏凤細

```python
if not self._backend_supports_execute():
    tools = [t for t in tools if t.name != "execute"]
```

### 鍦烘櫙 2: System Prompt 娉ㄥ叆

`SkillsMiddleware` 鍦ㄦ瘡娆?LLM 璋冪敤鍓嶆敞鍏?skill 鎸囦护锛?
```python
skill_instructions = self._build_skill_instructions(skills_metadata)
modified_request = append_to_system_message(
    request,
    skill_instructions,
    after_heading="### Skills Directory",
)
```

### 鍦烘櫙 3: 涓婁笅鏂囩獥鍙ｇ鐞?
`SummarizationMiddleware` 鍦?hook 涓鐞?token 浣跨敤锛?
```python
if current_tokens > self._truncation_threshold:
    request = self._truncate_old_messages(request)
    request = self._inject_summary_prompt(request)
```

### 鍦烘櫙 4: Human-in-the-Loop 瀹℃壒

CLI 鍦ㄥ伐鍏疯皟鐢ㄥ墠鎷︽埅骞惰姹傜敤鎴锋壒鍑嗭紙閫氳繃 `approval.py` widget锛夛細

```python
## libs/cli/deepagents_cli/widgets/approval.py
class ApprovalMenu:
    async def request_approval(self, tool_call: ToolCall) -> bool:
        # 鏄剧ず瀹℃壒瀵硅瘽妗?        # 绛夊緟鐢ㄦ埛鍐崇瓥
        # 杩斿洖 Approve/Reject/Edit
        pass
```

---

## 8. 鍒涘缓鑷畾涔?Hook

瑕佸垱寤鸿嚜瀹氫箟 hook锛岄渶瑕佺户鎵?`AgentMiddleware` 骞跺疄鐜?`wrap_model_call`锛?
```python
from langchain.agents.middleware import AgentMiddleware
from langchain.agents.middleware.types import ModelRequest, ModelResponse

class CustomMiddleware(AgentMiddleware[CustomState, ToolRuntime]):
    async def wrap_model_call(
        self,
        request: ModelRequest,
        call_next: Callable[[ModelRequest], Awaitable[ModelResponse]],
    ) -> ModelResponse:
        # 鍓嶇疆澶勭悊锛氫慨鏀硅姹?        modified_request = self._preprocess(request)

        # 璋冪敤涓嬩竴涓?middleware 鎴栨ā鍨?        response = await call_next(modified_request)

        # 鍚庣疆澶勭悊锛氫慨鏀瑰搷搴?        return self._postprocess(response)
```

鐒跺悗鍦ㄥ垱寤?agent 鏃舵敞鍐岋細

```python
from deepagents import create_deep_agent

agent = create_deep_agent(
    tools=my_tools,
    middleware=[
        CustomMiddleware(...),
        # ... other middleware
    ],
)
```

---

## 9. 鎬荤粨

Deep Agents 椤圭洰涓殑 hook 鏈哄埗鍏锋湁浠ヤ笅鐗圭偣锛?
1. **鍩轰簬 Middleware 妯″紡**锛氭墍鏈?hook 閮介€氳繃 `AgentMiddleware` 鍩虹被瀹炵幇
2. **閾惧紡鎵ц**锛氬涓?middleware 鎸夋敞鍐岄『搴忓舰鎴愬鐞嗛摼
3. **鐘舵€侀殧绂?*锛氭瘡涓?middleware 鏈夎嚜宸辩殑绉佹湁鐘舵€佺┖闂?4. **鍔ㄦ€佷慨鏀硅兘鍔?*锛氬彲浠ュ湪 hook 涓慨鏀硅姹傘€佸搷搴斻€佸伐鍏峰垪琛ㄣ€乻ystem prompt
5. **缁勫悎鎬?*锛歮iddleware 鍙互缁勫悎浣跨敤锛屽舰鎴愬鏉傜殑澶勭悊閫昏緫

杩欑璁捐浣垮緱 Deep Agents 鑳藉锛?- 鐏垫椿鎵╁睍鏂板姛鑳借€屾棤闇€淇敼鏍稿績浠ｇ爜
- 鏀寔澶氱鍚庣鍜岃繍琛屾椂鐜
- 瀹炵幇 fine-grained 鐨勮闂帶鍒跺拰宸ュ叿杩囨护
- 鎻愪緵璺?turn 鐨勪笂涓嬫枃绠＄悊鍜岀姸鎬佽窡韪?
---

**鍙傝€冭祫鏂欙細**
- Deep Agents Repository: https://github.com/langchain-ai/deepagents
- LangChain Agents Middleware: https://python.langchain.com/docs/modules/agents/middleware

---

*Published: 2026-03-05*  
*Categories: Tech & Coding*  
*Tags: LLM, AIAgent, DeepAgents, Python*
