---
layout: post
date: 2026-03-03
title: "agent SDK"
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

## 1. Goose
- **Owner:** Block
- **Source Code:** https://github.com/block/goose
- **Documentation:** https://block.github.io/goose/docs/quickstart
- **Open Source:** Yes
- **Provide SDK:** No
- **Support Skills:** Yes
- **Programming Language:** Rust + TypeScript
- **Notes:** 开源、可扩展的 AI 代理，超越代码建议，支持安装、执行、编辑和测试任何 LLM

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
  - 终端 AI 编码代理
  - 支持 75+ LLM 提供商
  - 具有原生 LSP 支持
  - 支持多会话并行运行多个代理


**SDK具体引用和使用方式如下：**

1. **安装SDK**
    
    在您的项目目录中，通过npm或yarn等包管理器进行安装：
    
    ```
    npm install @opencode-ai/sdk
    # 或
    yarn add @opencode-ai/sdk
    ```
    
2. **在代码中导入和初始化**
    
    您可以在代码文件中导入SDK并创建客户端实例：
    
    ```
    // 方式一：让SDK自动创建或连接到本地的Opencode实例
    import { createOpencode } from "@opencode-ai/sdk";
    const { client } = await createOpencode();
    
    // 方式二：连接到指定地址的已有Opencode实例
    import { createOpencodeClient } from "@opencode-ai/sdk";
    const client = createOpencodeClient({ baseUrl: "http://localhost:4096" });
    ```
    
3. **调用SDK提供的方法**
    
    获得`client`实例后，即可调用其丰富的API方法，例如：
    
    ```
    // 创建一个新的会话
    const session = await client.sessions.create();
    
    // 在会话中执行指令
    const response = await client.sessions.instruction(session.id, {
      instruction: "请分析当前目录下的package.json文件"
    });
    
    // 搜索项目中的文件
    const files = await client.files.search({
      query: "function component"
    });
    ```
    

**总结**：Opencode SDK的设计初衷就是为了被开发者**在代码中引用**，它将Opencode智能体的能力（如会话管理、文件操作、任务执行等）封装成了一系列编程接口，使您可以轻松地将AI代理功能嵌入到自己的应用程序、自动化脚本或工具链中。

---

## 3. Claude Code
- **Owner:** Anthropic
- **Source Code:** 专有（闭源）
- **Documentation:** https://claude.ai/docs/code
- **Open Source:** No
- **Provide SDK:**  [anthropics/claude-agent-sdk-python](https://github.com/anthropics/claude-agent-sdk-python)
- **Support Skills:** Yes
- **Programming Language:** TypeScript + Rust
- **Notes:** 
  - Anthropic 开发的基于终端的 AI 编码代理
  - 具有完整的文件系统访问权限
  - 可以读取、写入和编辑项目文件
  - 运行终端命令
  - 管理 Git 工作流

**SDK具体引用和使用方式如下：**
这是 Anthropic 官方提供的 **Claude Agent SDK 的 Python 版本**。
其核心功能是让开发者能在自己的 **Python 应用程序中直接集成和调用 Claude Code（Claude AI 代理）的全部能力**，而不仅限于通过命令行交互。
- **嵌入式集成**：SDK 已自动捆绑 Claude Code CLI，无需单独安装，可直接在代码中调用。  
- **双向对话**：支持与 Claude Code 进行双向、交互式的程序化对话
- **自定义工具**：允许你将 Python 函数定义为“工具”供 Claude 调用，这些工具以**进程内 MCP 服务器**的形式运行，无需独立进程。
- **钩子 (Hooks)**：允许你在 Claude 代理执行循环的特定节点插入自定义的 Python 函数，以实现确定的逻辑处理或自动反馈。
    

---

## 4. Openclaw
- **Owner:** Peter Steinberger
- **Source Code:** https://github.com/openclaw/openclaw
- **Documentation:** [OpenClaw - OpenClaw](https://docs.openclaw.ai/)
- **Open Source:** Yes
- **Provide SDK:** No
- **Support Skills:** Yes
- **Support ACP:** Yes, https://docs.openclaw.ai/tools/acp-agents
- **Programming Language:** TypeScript + Node.js
- **Notes:** 
  - 开源自主 AI 代理框架
  - 可在本地运行
  - 连接超过 20 个消息平台
  - 具有心跳调度器
  - 支持浏览器自动化、文件读写、shell 命令等

OpenClaw 的 ACP Agents 是一个用于调度外部 AI 编码工具（如 Claude Code、Codex）的协议系统。我试了一下没试出来。

---

## 5. Agentic Seek
- **Owner:** 社区开源
- **Source Code:** [Fosowl/agenticSeek: Fully Local Manus AI. No APIs, No $200 monthly bills. Enjoy an autonomous agent that thinks, browses the web, and code for the sole cost of electricity. 🔔 Official updates only via twitter @Martin993886460 (Beware of fake account)](https://github.com/Fosowl/agenticSeek)
- **Documentation:** https://fosowl.github.io/agenticSeek.html
- **Open Source:** Yes
- **Provide SDK:** No
- **Support Skills:** Yes
- **Programming Language:** Python
- **Notes:** 
  - 完全免费、开源的通用 AI 助手
  - 具有语音功能
  - 可以自主浏览网页
  - 编写代码和执行复杂任务
  - 数据本地存储

---

## 6. Deepagents
- **Owner:** LangChain AI
- **Source Code:** https://github.com/langchain-ai/deepagents
- **Documentation:** https://docs.langchain.com/deepagents
- **Open Source:** Yes
- **Provide SDK:** Yes
- **Support Skills:** Yes
- **Programming Language:** Python
- **Notes:** 
  - 基于 LangChain 和 LangGraph 构建的代理框架
  - 具有规划工具
  - 文件系统后端
  - 生成子代理的能力
  - 适用于复杂的代理任务
  - **最新版本:** 0.4.4 (2026-02-26)

---

## 7. AgentZero
- **Owner:** 社区开源
- **Source Code:** [agent0ai/agent-zero: Agent Zero AI framework](https://github.com/agent0ai/agent-zero)
- **Documentation:** https://agentzero.ai/docs
- **Open Source:** Yes
- **Provide SDK:** No
- **Support Skills:** Yes
- **Programming Language:** TypeScript + Python

---

## 8. OpenAI Agents SDK
- **Owner:** OpenAI
- **Source Code:**  [openai/openai-agents-python: A lightweight, powerful framework for multi-agent workflows](https://github.com/openai/openai-agents-python)
- **Documentation:**[OpenAI Agents SDK](https://openai.github.io/openai-agents-python/)
- **Open Source:** Yes
- **Provide SDK:** Yes 
- **Support Skills:** Yes [Tools - OpenAI Agents SDK](https://openai.github.io/openai-agents-python/tools/#hosted-container-shell-skills)
- **Programming Language:**  Python
- **Notes:** 


```python
from agents import Agent, Runner

agent = Agent(name="Assistant", instructions="You are a helpful assistant")

result = Runner.run_sync(agent, "Write a haiku about recursion in programming.")
print(result.final_output)

# Code within the code,
# Functions calling themselves,
# Infinite loop's dance.
```


---

## 9. Google ADK (Agent Development Kit)
- **Owner:** Google
- **Source Code:**  [google/adk-python: An open-source, code-first Python toolkit for building, evaluating, and deploying sophisticated AI agents with flexibility and control.](https://github.com/google/adk-python)
- **Documentation:**  [Index - Agent Development Kit (ADK)](https://google.github.io/adk-docs/)
- **Open Source:** Yes
- **Provide SDK:** Yes
- **Support Skills:** Yes
- **Programming Language:** TypeScript + Python + Java + Go
- **Notes:** 
  - Google 的代理开发工具包
  - 开源框架
  - 帮助开发者构建、测试、评估和部署自主 AI 代理和多代理系统
  - 深度集成 Google 生态系统

```python
from google.adk.agents.llm_agent import Agent

# Mock tool implementation
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