---
layout: post
date: 2026-03-13
title: "DeepAgents Subagent 使用指南"
categories: tech_coding
tags:
  - DeepAgents
  - AIAgent
  - LLM
  - Python
---

本文档详细解释了 DeepAgents 系统中 subagent（子代理）的使用方法、定义方式和最佳实践。

---

## 目录

1. [Subagent 概述](#subagent-概述)
2. [Subagent 的两种定义方式](#subagent-的两种定义方式)
3. [方式一：代码内联定义](#方式一代码内联定义)
4. [方式二：文件系统定义](#方式二文件系统定义)
5. [Subagent 的配置选项](#subagent-的配置选项)
6. [Task 工具的使用](#task-工具的使用)
7. [最佳实践](#最佳实践)
8. [完整示例](#完整示例)

---

## Subagent 概述

### 什么是 Subagent？

Subagent 是一个**临时性的、独立的**代理实例，由主代理通过 `task` 工具创建。它具有以下特点：

- **生命周期短暂**：仅在任务执行期间存在，完成后立即销毁
- **独立的上下文窗口**：拥有自己的对话历史，不会污染主代理的上下文
- **单次通信**：只能返回一个最终结果给主代理
- **并行执行**：可以同时启动多个 subagent 执行独立任务

### 何时使用 Subagent？

**✅ 适合使用 Subagent 的场景：**

| 场景 | 说明 |
|------|------|
| 复杂多步骤任务 | 需要多个步骤才能完成的独立任务 |
| 重型上下文任务 | 需要大量 token/上下文的专业任务（如代码分析、研究报告） |
| 并行独立任务 | 多个互不依赖的任务可以同时执行 |
| 领域专家代理 | 具有特定技能和工具的专业代理（如研究员、代码审查员） |
| 隔离执行 | 需要沙箱隔离的操作（如代码执行、数据格式化） |

**❌ 不适合使用 Subagent 的场景：**

| 场景 | 说明 |
|------|------|
| 简单任务 | 只需几次工具调用即可完成的任务 |
| 需要中间结果 | 主代理需要查看子任务的中间步骤 |
| 需要交互 | 任务执行过程中需要与用户交流 |
| 无益处拆分 | 拆分后不会减少 token 使用或复杂度 |

---

## Subagent 的两种定义方式

DeepAgents 支持两种方式定义 subagent：

### 1. 代码内联定义（推荐用于动态/临时 subagent）

在调用 `create_deep_agent()` 时直接定义 subagent 配置。

```python
from deepagents import create_deep_agent

research_sub_agent = {
    "name": "research-agent",
    "description": "研究复杂主题并返回综合报告",
    "system_prompt": "你是一名研究助理...",
    "tools": [search_tool],
    "model": "anthropic:claude-sonnet-4-5-20250929",
}

agent = create_deep_agent(
    model=model,
    subagents=[research_sub_agent],
)
```

### 2. 文件系统定义（推荐用于 CLI/项目化 subagent）

在 `.deepagents/agents/` 目录下创建 Markdown 文件定义 subagent。

```
.deepagents/
└── agents/
    └── researcher/
        └── AGENTS.md
```

---

## 方式一：代码内联定义

### 基本语法

```python
from deepagents import create_deep_agent
from langchain.chat_models import init_chat_model

# 定义 subagent
my_subagent = {
    # 必填字段
    "name": "my-subagent",                    # 唯一标识符
    "description": "这个 subagent 做什么",     # 主代理根据这个决定何时委托
    "system_prompt": "给 subagent 的详细指令", # subagent 的系统提示词

    # 可选字段
    "model": "provider:model-name",          # 覆盖主代理的模型
    "tools": [tool1, tool2],                 # subagent 可用的工具
    "middleware": [middleware1, middleware2], # 额外的中间件
    "interrupt_on": {...},                   # 需要人工批准的工具
    "skills": ["/path/to/skills"],           # 技能目录路径
}

# 创建主代理
agent = create_deep_agent(
    model="anthropic:claude-sonnet-4-5-20250929",
    subagents=[my_subagent],
)
```

### 完整示例

```python
"""Research Agent with Subagents"""

from datetime import datetime
from langchain.chat_models import init_chat_model
from deepagents import create_deep_agent

from tools import tavily_search, think_tool

current_date = datetime.now().strftime("%Y-%m-%d")

# 研究子代理 - 负责深入调查研究主题
research_sub_agent = {
    "name": "research-agent",
    "description": "委托研究任务给 subagent。一次只给一个研究主题。",
    "system_prompt": f"""你是一名专业研究助理。今天是 {current_date}。

## 你的工具
- tavily_search: 搜索网络获取最新信息
- think_tool: 进行战略性思考和分析

## 你的流程
1. 理解研究主题
2. 制定搜索策略
3. 执行搜索并收集信息
4. 综合分析结果
5. 返回结构化的研究报告

## 输出要求
- 清晰的结构（背景、发现、结论）
- 引用具体来源
- 提供关键数据和统计
""",
    "tools": [tavily_search, think_tool],
    "model": "anthropic:claude-haiku-4-5-20251001",  # 使用更轻量的模型节省成本
}

# 创建主代理
model = init_chat_model(model="anthropic:claude-sonnet-4-5-20250929")

agent = create_deep_agent(
    model=model,
    tools=[tavily_search, think_tool],
    subagents=[research_sub_agent],
)
```

---

## 方式二：文件系统定义

文件系统定义是 CLI 环境中推荐的方式，特别适合项目化的 subagent 管理。

### 目录结构

```
项目根目录/
├── .deepagents/
│   └── agents/
│       ├── researcher/
│       │   └── AGENTS.md
│       ├── code-reviewer/
│       │   └── AGENTS.md
│       └── greeting-responder/
│           └── AGENTS.md
└── 其他项目文件...
```

### AGENTS.md 文件格式

每个 subagent 在一个独立的文件夹中，包含一个 `AGENTS.md` 文件：

```markdown
---
name: researcher
description: 研究复杂主题，搜索网络信息，综合分析结果
model: anthropic:claude-haiku-4-5-20251001
---

你是一名专业研究助理，擅长深度信息搜集和综合分析。

## 你的能力
- 网络搜索和信息检索
- 多来源信息综合分析
- 数据和统计提取
- 来源可信度评估

## 工作流程
1. 理解研究问题和背景
2. 制定搜索策略（2-3 个针对性查询）
3. 搜集关键数据、引用和案例
4. 综合分析并形成结论

## 输出格式
- 背景：问题的背景信息
- 关键发现：3-5 个核心发现
- 数据来源：具体来源引用
- 结论：综合分析结论
```

### YAML Frontmatter 字段说明

| 字段 | 必填 | 说明 |
|------|------|------|
| `name` | ✅ | subagent 的唯一标识符，与 `task()` 工具的 `subagent_type` 参数对应 |
| `description` | ✅ | 简短描述，主代理根据这个决定何时委托任务 |
| `model` | ❌ | 可选的模型覆盖，格式为 `provider:model-name` |
| `system_prompt` | ✅ (文件主体) | Markdown 正文部分作为系统提示词 |

### 加载 Filesystem Subagents

在 CLI 环境中，subagent 会自动从以下目录加载：

```python
from deepagents_cli.subagents import list_subagents
from pathlib import Path

# 加载用户级和项目级 subagents
subagents = list_subagents(
    user_agents_dir=Path("~/.deepagents/agents").expanduser(),
    project_agents_dir=Path("./.deepagents/agents"),
)

# 返回 SubagentMetadata 列表
for sub in subagents:
    print(f"名称：{sub['name']}")
    print(f"描述：{sub['description']}")
    print(f"来源：{sub['source']}")  # 'user' 或 'project'
    print(f"路径：{sub['path']}")
```

### 项目优先级

- **项目 subagent 覆盖用户 subagent**：如果同名 subagent 同时存在于用户目录和项目目录，项目版本优先
- **来源标识**：每个 subagent 的 `source` 字段标识其来源（`user` 或 `project`）

---

## Subagent 的配置选项

### 完整配置字段

```python
SubAgent = {
    # 必填字段
    "name": str,              # 唯一标识符
    "description": str,       # 功能描述
    "system_prompt": str,     # 系统提示词

    # 可选字段
    "tools": Sequence[BaseTool | Callable | dict],  # 工具列表
    "model": str | BaseChatModel,                   # 模型覆盖
    "middleware": list[AgentMiddleware],            # 中间件
    "interrupt_on": dict[str, bool | InterruptOnConfig],  # 人工批准配置
    "skills": list[str],        # 技能目录路径
}
```

### 字段详解

#### `name` (必填)

subagent 的唯一标识符，用于 `task()` 工具的 `subagent_type` 参数。

```python
"name": "research-agent"  # ✅ 好的命名
"name": "researcher"      # ✅ 简洁明了
"name": "my-super-agent"  # ✅ 使用连字符分隔
```

#### `description` (必填)

简短描述 subagent 的功能。主代理根据这个描述决定何时委托任务。

**好的描述：**
```python
"description": "研究复杂主题，搜索网络信息并综合分析"
"description": "代码审查员，检查安全漏洞和最佳实践"
"description": "在创建重要内容后审查质量"
```

**差的描述：**
```python
"description": "一个代理"           # ❌ 太模糊
"description": "可以做很多事情"     # ❌ 不具体
```

#### `system_prompt` (必填)

给 subagent 的详细指令，包括：
- 角色定位
- 工具使用指南
- 工作流程
- 输出格式要求

```python
"system_prompt": """你是一名代码审查专家。

## 你的工具
- read_file: 读取代码文件
- grep: 搜索代码模式

## 审查重点
1. 安全漏洞（OWASP Top 10）
2. 代码质量问题
3. 性能瓶颈

## 输出格式
- 问题列表（严重程度 + 位置 + 修复建议）
- 总体评价
"""
```

#### `tools` (可选)

subagent 可以使用的工具列表。如果不指定，会从主代理继承工具。

```python
from langchain_core.tools import tool

@tool
def web_search(query: str, max_results: int = 5) -> dict:
    """搜索网络"""
    ...

subagent = {
    "name": "researcher",
    "tools": [web_search],  # 只提供搜索工具
}
```

#### `model` (可选)

覆盖主代理使用的模型。使用 `provider:model-name` 格式。

```python
"model": "anthropic:claude-haiku-4-5-20251001"  # 轻量级模型，节省成本
"model": "openai:gpt-4o"                         # 使用 OpenAI 模型
"model": "google:gemini-2.5-flash"              # 使用 Google 模型
```

#### `middleware` (可选)

额外的中间件配置。

```python
from deepagents.middleware import SkillsMiddleware

subagent = {
    "name": "skilled-agent",
    "middleware": [
        SkillsMiddleware(backend=backend, sources=["/skills"]),
    ],
}
```

#### `interrupt_on` (可选)

配置需要人工批准的工具调用。

```python
subagent = {
    "name": "safe-agent",
    "interrupt_on": {
        "execute": {"allowed_decisions": ["approve", "reject"]},
        "write_file": {"allowed_decisions": ["approve", "reject"]},
    },
}
```

---

## Task 工具的使用

### Task 工具简介

当 subagent 被添加到 `create_deep_agent()` 时，会自动添加一个 `task` 工具到主代理。

### Task 工具签名

```python
task(
    description: str,      # 详细的任务描述
    subagent_type: str,    # subagent 的名称
) -> str
```

### 调用示例

```python
# 主代理调用 subagent
response = agent.invoke({
    "messages": [{
        "role": "user",
        "content": "研究可再生能源的环境影响"
    }]
})

# 主代理内部会调用 task 工具
task(
    description="""研究可再生能源的环境影响，包括：
1. 太阳能、风能、水能的生产过程排放
2. 设备生命周期评估
3. 与传统能源的对比
4. 最新统计数据（2024-2025）

请将研究结果保存到 research/renewable-energy.md""",
    subagent_type="research-agent"
)
```

### Task 工具的描述模板

系统会自动生成 task 工具的描述，包含所有可用的 subagent：

```
Available agent types and the tools they have access to:
- research-agent: 研究复杂主题，搜索网络信息并综合分析
- code-reviewer: 代码审查员，检查安全漏洞和最佳实践
- general-purpose: 通用代理，处理复杂多步骤任务
```

### 自定义 Task 工具描述

```python
from deepagents.middleware.subagents import SubAgentMiddleware

middleware = SubAgentMiddleware(
    backend=backend,
    subagents=[...],
    task_description="""启动 subagent 处理独立复杂任务。

{available_agents}  # 会被自动替换为可用的 subagent 列表

使用指南：
1. 尽可能并行启动多个 subagent
2. 任务描述要详细具体
3. 明确指定输出格式
""",
)
```

---

## 最佳实践

### 1. 并行化独立任务

```python
# ✅ 好的做法：并行执行独立研究任务
# 主代理单次响应中调用多个 task
task(description="研究 Lebron James 的成就", subagent_type="researcher")
task(description="研究 Michael Jordan 的成就", subagent_type="researcher")
task(description="研究 Kobe Bryant 的成就", subagent_type="researcher")

# ❌ 差的做法：串行执行
# 等待第一个 subagent 完成后再调用第二个
```

### 2. 通过文件传递大数据

```python
# ✅ 好的做法：使用文件系统传递大数据
task(description="""
分析 data/input.csv 中的数据模式。
将分析结果保存到 analysis/report.md

分析要求：
1. 数据分布统计
2. 异常值检测
3. 趋势分析
""", subagent_type="analyst")

# ❌ 差的做法：在 task 描述中传递大量数据
```

### 3. 详细的任务规范

```python
# ✅ 好的做法：详细的任务描述
task(description="""
研究 Python 异步编程的最佳实践。

研究范围：
- async/await 基础语法
- 并发与并行的区别
- asyncio 库的核心概念
- 常见陷阱和解决方案

输出格式：
1. 概念解释（每部分 200-300 字）
2. 代码示例（带注释）
3. 实际应用场景

保存到 docs/async-best-practices.md""",
subagent_type="researcher")

# ❌ 差的做法：模糊的任务描述
task(description="研究异步编程", subagent_type="researcher")
```

### 4. 合理选择模型

```python
# 根据任务复杂度选择模型
subagents = [
    {
        "name": "researcher",
        "model": "anthropic:claude-sonnet-4-5-20250929",  # 复杂分析用强大模型
    },
    {
        "name": "summarizer",
        "model": "anthropic:claude-haiku-4-5-20251001",  # 简单任务用轻量模型
    },
]
```

### 5. 状态隔离

Subagent 运行在独立的状态中，以下状态键会被排除：

```python
_EXCLUDED_STATE_KEYS = {
    "messages",      # 消息历史（单独处理）
    "todos",         # 待办事项
    "structured_response",  # 结构化响应
    "skills_metadata",      # 技能元数据
    "memory_contents",      # 记忆内容
}
```

这意味着 subagent 不会继承主代理的待办事项列表或记忆内容。

---

## 完整示例

### 示例 1：研究代理系统

```python
"""多代理研究系统 - 并行研究多个主题并综合结果"""

from langchain.chat_models import init_chat_model
from deepagents import create_deep_agent

# 研究工具
from tools import web_search, save_research

# 主模型 - 负责综合和协调
main_model = init_chat_model("anthropic:claude-sonnet-4-5-20250929")

# 子代理 - 负责具体研究执行
research_subagent = {
    "name": "researcher",
    "description": "深入研究特定主题，返回综合报告",
    "system_prompt": """你是一名研究专家。

## 你的工具
- web_search(query, max_results=5): 搜索网络
- save_research(path, content): 保存研究结果

## 研究流程
1. 理解研究主题和具体要求
2. 制定 2-3 个针对性搜索查询
3. 收集关键数据、统计、引用
4. 综合分析并撰写报告
5. 保存到指定文件路径

## 报告结构
- 背景介绍
- 核心发现（3-5 点）
- 数据支持
- 结论""",
    "tools": [web_search, save_research],
    "model": "anthropic:claude-haiku-4-5-20251001",
}

# 创建主代理
agent = create_deep_agent(
    model=main_model,
    tools=[web_search, save_research],
    subagents=[research_subagent],
)

# 使用示例：并行研究多个主题
result = agent.invoke({
    "messages": [{
        "role": "user",
        "content": """比较太阳能、风能和核能的优缺点。
需要研究每种能源的：成本、环境影响、效率、可扩展性。"""
    }]
})
```

### 示例 2：内容构建代理

```python
"""内容构建代理 - 使用 subagent 进行研究，然后创建内容"""

from pathlib import Path
from deepagents import create_deep_agent
from deepagents.backends import FilesystemBackend

# 加载 subagent 配置（从 YAML 文件）
import yaml

def load_subagents(config_path: Path):
    """从 YAML 配置加载 subagent"""
    with open(config_path) as f:
        config = yaml.safe_load(f)

    subagents = []
    for name, spec in config.items():
        subagent = {
            "name": name,
            "description": spec["description"],
            "system_prompt": spec["system_prompt"],
        }
        if "model" in spec:
            subagent["model"] = spec["model"]
        if "tools" in spec:
            subagent["tools"] = spec["tools"]
        subagents.append(subagent)

    return subagents

# 创建代理
agent = create_deep_agent(
    model="anthropic:claude-sonnet-4-5-20250929",
    memory=["./AGENTS.md"],
    skills=["./skills/"],
    subagents=load_subagents(Path("./subagents.yaml")),
    backend=FilesystemBackend(root_dir=Path("./")),
)
```

### 示例 3：文件系统 Subagent 定义

```yaml
# subagents.yaml
# 定义多个 subagent

researcher:
  description: |
    总是首先使用这个 subagent 研究任何主题。
    搜索网络获取当前信息、统计数据和来源。
  model: anthropic:claude-haiku-4-5-20251001
  system_prompt: |
    你是一名研究助理。你可以访问 web_search 和 write_file 工具。

    ## 你的工具
    - web_search(query, max_results=5, topic="general")
    - write_file(file_path, content)

    ## 你的流程
    1. 使用 web_search 查找主题信息
    2. 进行 2-3 次针对性搜索
    3. 收集关键统计、引用和案例
    4. 将发现保存到指定文件路径

    ## 重要
    - 使用用户指定的确切文件路径
    - 始终包含来源 URL
    - 保持内容简洁但有信息量
  tools:
    - web_search

code-reviewer:
  description: |
    在创建重要内容后使用这个 subagent 审查质量。
    检查安全问题、代码质量和最佳实践。
  model: anthropic:claude-sonnet-4-5-20250929
  system_prompt: |
    你是一名代码审查专家。

    ## 审查重点
    1. 安全漏洞（OWASP Top 10）
    2. 代码质量和可维护性
    3. 性能问题
    4. 测试覆盖

    ## 输出格式
    - 问题列表（严重程度 + 位置 + 建议）
    - 总体评价
    - 优先级建议
```

---

## 附录：常见问题

### Q1: Subagent 可以访问主代理的记忆吗？

**A:** 默认情况下，`memory_contents` 状态键会被排除，subagent 不会自动继承主代理的记忆。如果 subagent 需要访问记忆，需要在配置中明确添加 `SkillsMiddleware` 或 `MemoryMiddleware`。

### Q2: Subagent 可以调用其他 subagent 吗？

**A:** 可以。Subagent 可以有自己的 `subagents` 配置，形成嵌套结构。但要注意避免循环依赖。

### Q3: 如何调试 subagent 的执行？

**A:** 使用 `agent.stream(..., subgraphs=True)` 可以查看 subagent 的执行过程：

```python
for update in agent.stream(inputs, subgraphs=True, stream_mode="updates"):
    print(update)
```

### Q4: Subagent 的费用如何计算？

**A:** Subagent 的 token 使用独立计算。使用轻量级模型（如 Haiku）可以降低成本。

---

## 参考资源

- [DeepAgents 官方文档](https://docs.langchain.com/oss/python/deepagents/overview)
- [Subagent Middleware 源码](libs/deepagents/deepagents/middleware/subagents.py)
- [CLI Subagents 加载器](libs/cli/deepagents_cli/subagents.py)
- [集成测试示例](libs/deepagents/tests/integration_tests/test_subagent_middleware.py)