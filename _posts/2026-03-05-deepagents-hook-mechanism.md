---
layout: post
date: 2026-03-05
title: "Deep Agents 中的 Hook 机制详解"
categories: tech_coding
tags:
  - LLM
  - AIAgent
  - DeepAgents
  - ProgrammingLanguage/Python
---

# Deep Agents 中的 Hook 机制详解

> **摘要：** Hook（钩子）是一种软件设计模式，允许开发者在程序执行的特定阶段插入自定义逻辑，而无需修改核心代码。本文深入分析 Deep Agents 项目中的 Hook 架构和实现细节。

---

## 1. 什么是 Hook？

**Hook（钩子）** 是一种软件设计模式，允许开发者在程序执行的特定阶段插入自定义逻辑，而无需修改核心代码。Hook 提供了一种**松耦合的扩展机制**，使得框架可以在预定义的生命周期点上调用用户提供的代码。

### Hook 的核心特点

- **拦截能力**：在特定执行点前后插入逻辑
- **动态修改**：可以修改输入参数、返回值或执行流程
- **状态持久化**：可以在多次调用之间维护状态
- **组合性**：多个 hook 可以链式组合使用

---

## 2. Deep Agents 项目中的 Hook 架构

在 Deep Agents 项目中，hook 机制主要通过 **Middleware（中间件）** 模式实现。项目的 middleware 系统基于 LangChain 的 `AgentMiddleware` 基类构建。

### 2.1 核心基类：AgentMiddleware

所有 middleware 都继承自 `langchain.agents.middleware.types.AgentMiddleware`，该类定义了以下核心 hook 方法：

```python
class AgentMiddleware:
    def wrap_model_call(
        self,
        request: ModelRequest,
        call_next: Callable[[ModelRequest], Awaitable[ModelResponse]],
    ) -> Awaitable[ModelResponse]:
        """拦截每次 LLM 请求的核心 hook"""
        pass
```

### 2.2 项目中的 Middleware 实现

项目实现了多种 middleware，每种都在特定的 hook 点上注入逻辑：

| Middleware | 文件位置 | Hook 用途 |
|------------|----------|----------|
| `FilesystemMiddleware` | `libs/deepagents/deepagents/middleware/filesystem.py` | 动态过滤工具、注入文件系统状态 |
| `SkillsMiddleware` | `libs/deepagents/deepagents/middleware/skills.py` | 注入 skill 指令到 system prompt |
| `MemoryMiddleware` | `libs/deepagents/deepagents/middleware/memory.py` | 跨 turn 状态维护 |
| `SummarizationMiddleware` | `libs/deepagents/deepagents/middleware/summarization.py` | 截断历史消息、注入摘要 |
| `SubAgentMiddleware` | `libs/deepagents/deepagents/middleware/subagents.py` | 子代理工具注入和路由 |

---

## 3. Hook 的具体实现分析

### 3.1 FilesystemMiddleware 的 Hook 实现

`FilesystemMiddleware` 展示了如何在 `wrap_model_call` hook 中动态修改请求：

```python
# libs/deepagents/deepagents/middleware/filesystem.py

class FilesystemMiddleware(AgentMiddleware[FilesystemState, ToolRuntime]):
    async def wrap_model_call(
        self,
        request: ModelRequest,
        call_next: Callable[[ModelRequest], Awaitable[ModelResponse]],
    ) -> ModelResponse:
        # Hook 点 1: 动态过滤工具列表
        tools = list(request.tools)
        if not self._backend_supports_execute():
            # 如果后端不支持 execute，移除该工具
            tools = [t for t in tools if t.name != "execute"]

        # Hook 点 2: 注入 system prompt 上下文
        modified_request = self._inject_filesystem_context(request, tools)

        # 调用下一个 middleware 或直接调用模型
        response = await call_next(modified_request)

        # Hook 点 3: 处理响应后的逻辑
        return self._process_response(response)
```

**关键设计模式**：
1. **请求拦截**：在 LLM 调用前修改 `ModelRequest`
2. **工具过滤**：根据运行时条件动态调整可用工具
3. **上下文注入**：向 system message 注入文件系统状态
4. **响应处理**：在返回前处理模型响应

### 3.2 SkillsMiddleware 的 Hook 实现

`SkillsMiddleware` 展示了如何注入 skill 指令：

```python
# libs/deepagents/deepagents/middleware/skills.py

class SkillsMiddleware(AgentMiddleware[SkillsState, ToolRuntime]):
    async def wrap_model_call(
        self,
        request: ModelRequest,
        call_next: Callable[[ModelRequest], Awaitable[ModelResponse]],
    ) -> ModelResponse:
        # 从配置的 sources 加载 skill 元数据
        skills_metadata = await self._load_skills_metadata()

        # 构建 skill 指令文本
        skill_instructions = self._build_skill_instructions(skills_metadata)

        # Hook: 修改 system message，追加 skill 指令
        modified_request = append_to_system_message(
            request,
            skill_instructions,
            after_heading="### Skills Directory",
        )

        return await call_next(modified_request)
```

### 3.3 SummarizationMiddleware 的复杂 Hook 逻辑

这是最复杂的 middleware，展示了多阶段 hook 处理：

```python
# libs/deepagents/deepagents/middleware/summarization.py

class SummarizationMiddleware(AgentMiddleware[SummarizationState, ToolRuntime]):
    async def wrap_model_call(
        self,
        request: ModelRequest,
        call_next: Callable[[ModelRequest], Awaitable[ModelResponse]],
    ) -> ModelResponse:
        # Hook 阶段 1: Token 计数和上下文窗口检查
        current_tokens = self._count_tokens(request.messages)
        context_limit = self._get_context_limit(request.model)

        # Hook 阶段 2: 如果超出阈值，执行截断和摘要
        if current_tokens > self._truncation_threshold:
            request = self._truncate_old_messages(request)
            request = self._inject_summary_prompt(request)

        # Hook 阶段 3: 响应后处理，更新摘要状态
        response = await call_next(request)
        self._update_summarization_state(response)

        return response
```

---

## 4. Hook 的状态管理

Middleware 使用 `AgentState` 来维护跨 turn 的状态：

```python
# 定义状态结构
class FilesystemState(AgentState):
    files: Annotated[NotRequired[dict[str, FileData]], _file_data_reducer]
    """Files in the filesystem."""

class SkillsState(AgentState):
    skills_metadata: NotRequired[Annotated[list[SkillMetadata], PrivateStateAttr]]
    """List of loaded skill metadata from configured sources."""

class SummarizationState(AgentState):
    truncation_events: NotRequired[list[dict]]
    """History of truncation events."""
```

**状态特性**：
- `PrivateStateAttr`：标记为私有状态，不传播到父 agent
- `Annotated[..., _reducer]`：定义状态合并的 reducer 函数
- `NotRequired`：状态字段是可选的

---

## 5. Hook 的执行流程

<pre class="mermaid">
flowchart TD
    A[User Message] --> B[Agent Graph<br/>create_deep_agent]
    B --> C[FilesystemMiddleware<br/>过滤工具 + 注入上下文]
    C --> D[SkillsMiddleware<br/>注入 skill 指令]
    D --> E[SummarizationMiddleware<br/>Token 管理 + 截断]
    E --> F[SubAgentMiddleware<br/>子代理路由]
    F --> G[LLM Model Call]
    G --> H[Response Processing]
    H --> I[Agent Response]
</pre>

---

## 6. CLI 层的 Hook 扩展

除了 SDK 层的 middleware，CLI 还有额外的 hook 机制：

### 6.1 本地上下文 Hook (`local_context.py`)

```python
# libs/cli/deepagents_cli/local_context.py

class LocalContextMiddleware(AgentMiddleware):
    """CLI 特定的中间件，处理本地项目上下文"""

    async def wrap_model_call(
        self,
        request: ModelRequest,
        call_next: Callable[[ModelRequest], Awaitable[ModelResponse]],
    ) -> ModelResponse:
        # 检测项目根目录
        project_root = self._find_project_root()

        # 注入项目特定的配置
        request = self._inject_project_context(request, project_root)

        return await call_next(request)
```

### 6.2 Tool Call Hook（工具调用拦截）

CLI 在 `textual_adapter.py` 中实现了对工具调用的拦截：

```python
# libs/cli/deepagents_cli/textual_adapter.py

async def execute_task_textual(...):
    # Hook: 在工具调用前请求用户批准
    if tool_call.requires_approval:
        approved = await self._request_user_approval(tool_call)
        if not approved:
            return self._reject_tool_call(tool_call)

    # Hook: 工具调用后更新 UI
    result = await execute_tool(tool_call)
    await self._update_ui_with_tool_result(result)
```

---

## 7. Hook 的实际应用场景

### 场景 1: 动态工具过滤

当后端不支持某些功能时，`FilesystemMiddleware` 在 hook 中移除相关工具：

```python
if not self._backend_supports_execute():
    tools = [t for t in tools if t.name != "execute"]
```

### 场景 2: System Prompt 注入

`SkillsMiddleware` 在每次 LLM 调用前注入 skill 指令：

```python
skill_instructions = self._build_skill_instructions(skills_metadata)
modified_request = append_to_system_message(
    request,
    skill_instructions,
    after_heading="### Skills Directory",
)
```

### 场景 3: 上下文窗口管理

`SummarizationMiddleware` 在 hook 中管理 token 使用：

```python
if current_tokens > self._truncation_threshold:
    request = self._truncate_old_messages(request)
    request = self._inject_summary_prompt(request)
```

### 场景 4: Human-in-the-Loop 审批

CLI 在工具调用前拦截并请求用户批准（通过 `approval.py` widget）：

```python
# libs/cli/deepagents_cli/widgets/approval.py
class ApprovalMenu:
    async def request_approval(self, tool_call: ToolCall) -> bool:
        # 显示审批对话框
        # 等待用户决策
        # 返回 Approve/Reject/Edit
        pass
```

---

## 8. 创建自定义 Hook

要创建自定义 hook，需要继承 `AgentMiddleware` 并实现 `wrap_model_call`：

```python
from langchain.agents.middleware import AgentMiddleware
from langchain.agents.middleware.types import ModelRequest, ModelResponse

class CustomMiddleware(AgentMiddleware[CustomState, ToolRuntime]):
    async def wrap_model_call(
        self,
        request: ModelRequest,
        call_next: Callable[[ModelRequest], Awaitable[ModelResponse]],
    ) -> ModelResponse:
        # 前置处理：修改请求
        modified_request = self._preprocess(request)

        # 调用下一个 middleware 或模型
        response = await call_next(modified_request)

        # 后置处理：修改响应
        return self._postprocess(response)
```

然后在创建 agent 时注册：

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

## 9. 总结

Deep Agents 项目中的 hook 机制具有以下特点：

1. **基于 Middleware 模式**：所有 hook 都通过 `AgentMiddleware` 基类实现
2. **链式执行**：多个 middleware 按注册顺序形成处理链
3. **状态隔离**：每个 middleware 有自己的私有状态空间
4. **动态修改能力**：可以在 hook 中修改请求、响应、工具列表、system prompt
5. **组合性**：middleware 可以组合使用，形成复杂的处理逻辑

这种设计使得 Deep Agents 能够：
- 灵活扩展新功能而无需修改核心代码
- 支持多种后端和运行时环境
- 实现 fine-grained 的访问控制和工具过滤
- 提供跨 turn 的上下文管理和状态跟踪

---

**参考资料：**
- Deep Agents Repository: https://github.com/langchain-ai/deepagents
- LangChain Agents Middleware: https://python.langchain.com/docs/modules/agents/middleware

---

*Published: 2026-03-05*  
*Categories: Tech & Coding*  
*Tags: LLM, AIAgent, DeepAgents, Python*
