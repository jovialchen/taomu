---
layout: post
date: 2026-03-04
title: "OpenClaw Agent ReAct 架构与实现深度解析"
categories: tech_coding
tags:
  - OpenClaw
  - AIAgent
  - ReAct
  - LLM
  - Architecture
  - DeepAgents
---

> OpenClaw 项目中 Agent 的 Reasoning 和 Acting 完整实现细节

---

## 目录

### 第一部分：架构概览
1. [项目概述](#项目概述)
2. [整体架构图](#整体架构图)
3. [消息流转图](#消息流转图)

### 第二部分：ReAct 核心机制
4. [ReAct 循环概览](#react-循环概览)
5. [Reasoning 机制详解](#reasoning-机制详解)
6. [Acting 机制详解](#acting-机制详解)

### 第三部分：工具系统
7. [工具调用完整流程](#工具调用完整流程)
8. [Bash 工具执行详解](#bash-工具执行详解)

### 第四部分：支撑系统
9. [事件订阅系统](#事件订阅系统)
10. [会话管理](#会话管理)
11. [上下文管理](#上下文管理)

### 第五部分：错误处理与扩展
12. [错误处理与重试](#错误处理与重试)
13. [插件系统](#插件系统)
14. [配置系统](#配置系统)

### 第六部分：实战示例
15. [完整 ReAct 循环示例](#完整-react-循环示例)

---

## 项目概述

**OpenClaw** 是一个个人 AI 助手平台，采用本地优先 (Local-first) 架构。

### 核心特性

| 特性 | 描述 |
|------|------|
| **多通信渠道** | WhatsApp, Telegram, Slack, Discord, Signal, iMessage 等 20+ 平台 |
| **本地网关** | WebSocket 控制平面 (端口 18789) |
| **多客户端** | macOS 应用，iOS/Android 节点，WebChat UI |
| **插件化架构** | 所有渠道和功能都以插件形式实现 |
| **技能系统** | 可扩展的工具和技能注册表 |

---

## 整体架构图

<pre class="mermaid">
graph LR
    subgraph Channels["通信渠道层 (Channels)"]
        direction LR
        C1[WhatsApp]
        C2[Telegram]
        C3[Slack]
        C4[Discord]
        C5[Signal]
        C6[iMessage]
        C7[Google Chat]
        C8[IRC / Teams / Matrix / LINE]
    end

    subgraph Gateway["Gateway WebSocket 服务器 (127.0.0.1:18789)"]
        direction LR
        G1[Channel Manager]
        G2[Agent Router]
        G3[Canvas Host]
        G4[Control UI]
        G5[Hooks System]
        G6[Cron Jobs]
        G7[Webhooks]
        G8[Auth/Pairing]
    end

    subgraph Clients["客户端"]
        direction LR
        CL1[CLI 工具]
        CL2[macOS App]
        CL3[WebChat UI]
    end

    subgraph Nodes["节点"]
        direction LR
        N1[iOS Node]
        N2[Android Node]
        N3[Node 节点]
    end

    Channels --> Gateway
    Gateway --> Clients
    Clients <--> Nodes
</pre>

---

## 消息流转图

<pre class="mermaid">
flowchart TD
    Start([用户发送消息]) --> ChannelPlugin["Channel Plugin\nTelegram/WhatsApp/Discord/Slack\n接收原始消息\n标准化格式\n提取媒体附件"]

    ChannelPlugin --> InboundRouter["Inbound Router\nsrc/gateway/inbound/\n渠道路由\n账户匹配\n会话键生成"]

    InboundRouter --> QueueManager["Queue Manager\nsrc/agents/pi-embedded-runner/runs.ts\n会话队列\n并发控制\nSteering 支持"]

    QueueManager --> AgentExec["Agent Execution\nrunEmbeddedPiAgent\n加载 Session\nReAct 循环\n工具执行"]

    AgentExec --> Tools{工具类型}
    Tools --> Bash[Bash 工具]
    Tools --> File[File 工具]
    Tools --> Send[Send 工具]
    Tools --> SubAgent[SubAgent 工具]

    Bash --> ResponseFormatter
    File --> ResponseFormatter
    Send --> ResponseFormatter
    SubAgent --> ResponseFormatter

    ResponseFormatter["Response Formatter\nMarkdown 渲染\n媒体处理\n分块发送"] --> ChannelOutbound["Channel Outbound\nAPI 调用\n错误重试\n状态追踪"]

    ChannelOutbound --> End([发送回用户])
</pre>

---

## ReAct 循环概览

### 完整 ReAct 流程图

<pre class="mermaid">
sequenceDiagram
    participant User as 用户
    participant Gateway as Gateway
    participant Agent as Agent Core
    participant LLM as LLM API
    participant Tool as Tool Executor
    participant Session as Session Manager

    User->>Gateway: 发送消息
    Gateway->>Agent: 触发 runEmbeddedPiAgent()

    loop ReAct 循环
        Agent->>Session: 加载历史消息
        Agent->>Agent: 构建 System Prompt
        Agent->>LLM: prompt(messages + user_input)

        LLM-->>Agent: 流式响应

        alt Reasoning 阶段
            LLM-->>Agent: thinking_delta (推理内容)
            Agent-->>Gateway: onReasoningStream (可选)
        end

        alt Acting 阶段
            LLM-->>Agent: tool_call {name, args}
            Agent->>Agent: handleToolExecutionStart
            Agent->>Tool: execute(toolName, args)
            Tool-->>Agent: tool_result {output, error}
            Agent->>Agent: handleToolExecutionEnd
            Agent->>Session: 追加 tool_result 到历史
            Agent->>LLM: 继续推理 (自动)
        end

        alt 文本响应阶段
            LLM-->>Agent: text_delta (最终回复)
            Agent-->>Gateway: onPartialReply / onBlockReply
        end

        Agent->>Agent: 检查是否完成
    end

    Agent-->>Gateway: 返回最终结果
    Gateway-->>User: 发送回复
</pre>

### ReAct 状态机

<pre class="mermaid">
stateDiagram-v2
    [*] --> Idle: 等待用户消息

    Idle --> Reasoning: 调用 LLM
    Reasoning --> Reasoning: 接收 thinking_delta
    Reasoning --> Acting: 接收 tool_call
    Reasoning --> Responding: 接收 text_delta
    Reasoning --> Completed: 接收 done

    Acting --> ToolExecuting: 开始工具执行
    ToolExecuting --> ToolExecuting: 接收 partial_result
    ToolExecuting --> ContextUpdating: 工具完成

    ContextUpdating --> Reasoning: LLM 继续推理
    ContextUpdating --> Completed: 无更多工具调用

    Responding --> Responding: 接收更多 text_delta
    Responding --> Completed: 接收 done

    Completed --> Idle: 等待下一条消息
</pre>

### 核心代码位置

```
src/
├── agents/
│   ├── pi-embedded-runner/
│   │   ├── run.ts              # 主运行循环 (外层重试/认证配置轮转)
│   │   ├── run/
│   │   │   └── attempt.ts      # 单次尝试执行 (核心 ReAct 循环)
│   │   ├── runs.ts             # 运行状态管理
│   │   └── types.ts            # 类型定义
│   ├── pi-embedded.ts          # 导出入口
│   ├── pi-embedded-subscribe.ts # 事件订阅处理
│   └── pi-tools.ts             # 工具定义
```

### ReAct 循环关键代码

#### 1. 入口点：`runEmbeddedPiAgent` (run.ts)

```typescript
// src/agents/pi-embedded-runner/run.ts

export async function runEmbeddedPiAgent(params: RunEmbeddedPiAgentParams) {
  // 1. 解析认证配置 (支持 OAuth/API Key 轮转)
  const profileOrder = resolveAuthProfileOrder({...});
  const profileCandidates = lockedProfileId ? [lockedProfileId] : profileOrder;

  // 2. 外层重试循环 (处理认证失败/模型故障转移)
  for (let iteration = 0; iteration < maxIterations; iteration++) {
    // 3. 选择认证配置
    const profileId = profileCandidates[profileIndex % profileCandidates.length];
    const apiKeyInfo = await getApiKeyForModel({...});

    // 4. 执行单次尝试
    const result = await runEmbeddedAttempt({
      ...params,
      authStorage,
      modelRegistry,
    });

    // 5. 处理结果 (成功/失败转移)
    if (result.status === "success") {
      return result;
    }
  }
}
```

#### 2. 核心循环：`runEmbeddedAttempt` (attempt.ts)

```typescript
// src/agents/pi-embedded-runner/run/attempt.ts

export async function runEmbeddedAttempt(params: EmbeddedRunAttemptParams) {
  // === 初始化阶段 ===

  // 1. 创建 Session Manager (JSONL 文件存储)
  sessionManager = guardSessionManager(
    SessionManager.open(params.sessionFile),
    {...}
  );

  // 2. 创建工具定义
  const tools = createOpenClawCodingTools({...});

  // 3. 创建 Agent Session
  ({ session } = await createAgentSession({
    cwd: resolvedWorkspace,
    model: params.model,
    tools: builtInTools,
    customTools: allCustomTools,
    sessionManager,
  }));

  // 4. 应用 System Prompt
  applySystemPromptOverrideToSession(session, systemPromptText);

  // === ReAct 循环阶段 ===

  // 5. 订阅事件流 (处理工具调用/响应流)
  const subscription = subscribeEmbeddedPiSession({
    session: activeSession,
    onToolResult: params.onToolResult,
    onPartialReply: params.onPartialReply,
    ...
  });

  // 6. 执行 Prompt (触发 LLM 调用)
  await activeSession.prompt(effectivePrompt, { images });

  // 7. 等待完成并处理结果
  const messagesSnapshot = activeSession.messages;
  const assistantTexts = subscription.assistantTexts;
  const toolMetas = subscription.toolMetas;

  // 8. 返回结果
  return {
    payloads: buildPayloadsFromAssistantTexts(assistantTexts),
    meta: {
      sessionId,
      usage: usageTotals,
      toolCalls: toolMetas.length,
    },
  };
}
```

---

## Reasoning 机制详解

### 1. Reasoning 模式

OpenClaw 支持三种 Reasoning 模式：

```typescript
type ReasoningMode = "off" | "on" | "stream";

// off     - 不处理推理标签，直接输出
// on      - 等待完整响应后提取推理内容
// stream  - 实时流式输出推理内容
```

### 2. 推理标签识别

```typescript
// 支持的推理标签格式
const THINKING_TAG_SCAN_RE = /<\s*(\/?)\s*(?:think(?:ing)?|thought|antthinking)\s*>/gi;
const FINAL_TAG_SCAN_RE = /<\s*(\/?)\s*final\s*>/gi;

// 示例 LLM 响应:
// <think>
// 用户想要查询天气，我需要先调用天气 API...
// 让我先检查参数是否完整。
//</think>
//
// <final>
// 我已经调用了天气 API，结果显示...
// </final>
```

### 3. 推理内容提取流程

<pre class="mermaid">
flowchart TD
    A[LLM 流式响应] --> B{检查 reasoningMode}

    B -->|off| C[直接输出文本]
    B -->|on| D[累积完整响应]
    B -->|stream| E[实时提取推理]

    D --> F[extractThinkingFromTaggedText]
    F --> G[分离 <think> 内容和普通文本]
    G --> H[调用 onReasoningEnd]

    E --> I[extractThinkingFromTaggedStream]
    I --> J[检测标签开闭状态]
    J --> K[调用 onReasoningStream]
    K --> L[调用 onReasoningEnd]
</pre>

### 4. 推理状态追踪

```typescript
interface EmbeddedPiSubscribeState {
  // 推理模式配置
  reasoningMode: "off" | "on" | "stream";
  includeReasoning: boolean;      // 是否包含推理内容
  streamReasoning: boolean;       // 是否流式输出推理

  // 推理流状态
  reasoningStreamOpen: boolean;   // 推理流是否开启
  lastReasoningSent: string;      // 最后发送的推理内容
  lastStreamedReasoning: string;  // 最后流式输出的推理

  // 标签状态追踪
  blockState: {
    thinking: boolean;   // 是否在 <think> 块内
    final: boolean;      // 是否在 <final> 块内
    inlineCode: InlineCodeState;
  };

  // 缓冲区域
  deltaBuffer: string;   // 当前文本缓冲
  blockBuffer: string;   // 当前块缓冲
}
```

### 5. 推理事件处理

```typescript
// handleMessageUpdate - 处理 LLM 消息更新
function handleMessageUpdate(ctx, evt) {
  const assistantEvent = evt.assistantMessageEvent;
  const evtType = assistantEvent?.type;

  // 处理 thinking 事件
  if (evtType === "thinking_start" ||
      evtType === "thinking_delta" ||
      evtType === "thinking_end") {

    // 提取推理内容
    const thinkingDelta = assistantEvent.delta;
    const thinkingContent = assistantEvent.content;

    // 流式输出推理
    if (ctx.state.streamReasoning) {
      const partialThinking = extractAssistantThinking(msg);
      ctx.emitReasoningStream(partialThinking || thinkingContent);
    }

    // 推理结束
    if (evtType === "thinking_end") {
      emitReasoningEnd(ctx);
    }
  }
}
```

---

## Acting 机制详解

### 1. 工具调用识别

当 LLM 决定调用工具时，会返回一个 tool_call 对象：

```json
{
  "type": "tool_call",
  "toolName": "bash",
  "toolCallId": "tc_001",
  "args": {
    "command": "ls -la"
  }
}
```

### 2. 工具执行三阶段

<pre class="mermaid">
sequenceDiagram
    participant Agent as Agent
    participant Handler as Event Handler
    participant Executor as Tool Executor
    participant Hook as Hook System
    participant Gateway as Gateway

    Note over Agent,Hook: 阶段 1: tool_execution_start

    Agent->>Handler: handleToolExecutionStart(evt)
    Handler->>Handler: 记录工具开始时间
    Handler->>Handler: 推断工具 meta 信息
    Handler->>Gateway: emitAgentEvent(phase: "start")
    Handler->>Gateway: onAgentEvent(tool start)
    Handler->>Handler: 跟踪 pending 消息 (如果是 messaging tool)

    Note over Agent,Hook: 阶段 2: tool_execution_update (可选)

    Agent->>Handler: handleToolExecutionUpdate(evt)
    Handler->>Gateway: emitAgentEvent(phase: "update")
    Handler->>Gateway: onAgentEvent(tool update)

    Note over Agent,Hook: 阶段 3: tool_execution_end

    Agent->>Handler: handleToolExecutionEnd(evt)
    Handler->>Hook: runAfterToolCall hook
    Handler->>Handler: 提交/丢弃 pending 消息
    Handler->>Handler: 记录工具错误 (如果有)
    Handler->>Gateway: emitAgentEvent(phase: "end")
    Handler->>Gateway: onToolResult (如果配置)
</pre>

### 3. 工具调用状态追踪

```typescript
interface ToolHandlerState {
  // 工具元数据
  toolMetas: ToolMeta[];                    // 已执行工具列表
  toolMetaById: Map<string, ToolCallSummary>; // 按 ID 索引
  toolSummaryById: Set<string>;             // 已发送摘要的工具

  // 错误追踪
  lastToolError?: {
    toolName: string;
    meta?: string;
    error: string;
    mutatingAction?: string;
    actionFingerprint?: string;
  };

  // Messaging 工具追踪
  pendingMessagingTexts: Map<string, string>;      // 待提交文本
  pendingMessagingTargets: Map<string, string>;    // 待提交目标
  messagingToolSentTexts: string[];                // 已发送文本
  messagingToolSentTargets: string[];              // 已发送目标
  messagingToolSentMediaUrls: string[];            // 已发送媒体
}
```

### 4. 工具执行详细实现

```typescript
// handleToolExecutionStart - 工具执行开始
export async function handleToolExecutionStart(ctx, evt) {
  const toolName = normalizeToolName(evt.toolName);
  const toolCallId = evt.toolCallId;
  const args = evt.args;

  // 1. 记录工具开始时间和参数
  toolStartData.set(`${runId}:${toolCallId}`, {
    startTime: Date.now(),
    args
  });

  // 2. 推断并存储工具元数据
  const meta = extendExecMeta(toolName, args, inferToolMetaFromArgs(toolName, args));
  ctx.state.toolMetaById.set(toolCallId, buildToolCallSummary(toolName, args, meta));

  // 3. 发送工具开始事件
  emitAgentEvent({
    runId,
    stream: "tool",
    data: {
      phase: "start",
      name: toolName,
      toolCallId,
      args
    }
  });

  // 4. 跟踪 messaging 工具的待发送内容
  if (isMessagingTool(toolName)) {
    const sendTarget = extractMessagingToolSend(toolName, argsRecord);
    if (sendTarget) {
      ctx.state.pendingMessagingTargets.set(toolCallId, sendTarget);
    }
    const text = argsRecord.content ?? argsRecord.message;
    if (text) {
      ctx.state.pendingMessagingTexts.set(toolCallId, text);
    }
  }

  // 5. 发送工具摘要 (如果配置了 onToolResult)
  if (ctx.shouldEmitToolResult()) {
    ctx.emitToolSummary(toolName, meta);
  }
}

// handleToolExecutionEnd - 工具执行结束
export async function handleToolExecutionEnd(ctx, evt) {
  const toolName = evt.toolName;
  const toolCallId = evt.toolCallId;
  const isError = evt.isError;
  const result = evt.result;

  // 1. 获取工具开始数据
  const startData = toolStartData.get(`${runId}:${toolCallId}`);
  toolStartData.delete(`${runId}:${toolCallId}`);

  // 2. 清理工具元数据
  const callSummary = ctx.state.toolMetaById.get(toolCallId);
  ctx.state.toolMetas.push({ toolName, meta: callSummary?.meta });
  ctx.state.toolMetaById.delete(toolCallId);

  // 3. 处理工具错误
  if (isError) {
    const errorMessage = extractToolErrorMessage(result);
    ctx.state.lastToolError = {
      toolName,
      meta: callSummary?.meta,
      error: errorMessage,
      mutatingAction: callSummary?.mutatingAction
    };
  }

  // 4. 提交 messaging 工具的已发送内容
  const pendingText = ctx.state.pendingMessagingTexts.get(toolCallId);
  if (pendingText && !isError) {
    ctx.state.messagingToolSentTexts.push(pendingText);
    ctx.state.pendingMessagingTexts.delete(toolCallId);
  }

  // 5. 执行 after_tool_call hooks
  const hookRunner = getGlobalHookRunner();
  if (hookRunner?.hasHooks("after_tool_call")) {
    await hookRunner.runAfterToolCall({
      toolName,
      result: sanitizedResult,
      durationMs: Date.now() - startData.startTime
    });
  }

  // 6. 发送工具结果事件
  emitToolResultOutput({
    ctx,
    toolName,
    meta: callSummary?.meta,
    isToolError: isError,
    result,
    sanitizedResult
  });
}
```

---

## 工具调用完整流程

### 工具类型分类

<pre class="mermaid">
mindmap
  root((工具类型))
    核心工具
      bash/exec
        命令执行
        PTY 支持
        Elevated 模式
      file_ops
        read
        write
        edit
        apply_patch
      search
        ls
        glob
        grep
    通信工具
      send
      reply
      react
      edit_message
    Agent 工具
      subagent
      memory
      cron
      sessions
    系统工具
      location
      canvas
      camera
      screen_record
    自定义工具
      Python 脚本
      Shell 脚本
      npm 包
      MCP 工具
</pre>

### 工具执行时序图

<pre class="mermaid">
sequenceDiagram
    participant LLM
    participant Agent as Agent Core
    participant Subscribe as Subscribe Handler
    participant Executor as Tool Executor
    participant Hooks as Hook System
    participant Session as Session Manager

    LLM->>Agent: tool_call {name: "bash", args: {...}}

    Agent->>Subscribe: handleToolExecutionStart
    Subscribe->>Subscribe: 记录工具开始时间
    Subscribe->>Hooks: before_tool_call hook
    Subscribe-->>Agent: 准备执行

    Agent->>Executor: execute(toolName, args)
    Executor->>Executor: 预检命令
    Executor->>Executor: 派生进程
    Executor->>Executor: 等待完成

    opt 长时间运行工具
      Executor-->>Subscribe: partial_result (流式更新)
      Subscribe->>Hooks: tool_execution_update
    end

    Executor-->>Agent: tool_result {output, error}

    Agent->>Subscribe: handleToolExecutionEnd
    Subscribe->>Subscribe: 清理工具元数据
    Subscribe->>Subscribe: 处理错误/提交消息
    Subscribe->>Hooks: after_tool_call hook
    Subscribe->>Session: 追加 tool_result 到历史

    Session-->>LLM: 更新后的上下文
    LLM->>LLM: 基于工具结果继续推理
</pre>

---

## Bash 工具执行详解

### Bash 工具定义

```typescript
// src/agents/bash-tools.ts

const bashTool = {
  name: "bash",
  description: "Execute bash commands",
  inputSchema: {
    command: { type: "string" },
    background: { type: "boolean", optional: true },
    elevated: { type: "boolean", optional: true },
  },

  execute: async (callId: string, params: BashParams, signal?: AbortSignal) => {
    // 1. 命令预检
    const preflightResult = await scriptPreflight(params.command);
    if (preflightResult.blocked) {
      throw new Error(`Command blocked: ${preflightResult.reason}`);
    }

    // 2. 获取进程监督器
    const supervisor = getProcessSupervisor();

    // 3. 生成执行参数
    const execArgs = buildExecArgs({
      command: params.command,
      elevated: params.elevated,
      ...
    });

    // 4. 派生进程
    const managedRun = await supervisor.spawn({
      argv: [shell, ...execArgs],
      cwd: workspaceDir,
      env: processEnv,
      timeoutMs: timeout,
    });

    // 5. 等待完成
    const result = await managedRun.wait();

    // 6. 返回结果
    return {
      stdout: result.stdout,
      stderr: result.stderr,
      exitCode: result.exitCode,
    };
  }
};
```

### Bash 执行流程

<pre class="mermaid">
flowchart TD
    A[接收 tool_call: bash] --> B[命令预检 scriptPreflight]
    B --> C{命令是否被阻止？}
    C -->|Yes| D[抛出错误：Command blocked]
    C -->|No| E[获取进程监督器]

    E --> F[构建执行参数]
    F --> G{elevated=true?}
    G -->|Yes| H[请求管理员权限]
    G -->|No| I[使用普通权限]

    H --> J[派生进程 supervisor.spawn]
    I --> J

    J --> K{background=true?}
    K -->|Yes| L[后台运行模式]
    K -->|No| M[前台运行模式]

    L --> N[返回进程 ID]
    M --> O[等待完成 managedRun.wait]

    O --> P{pty=true?}
    P -->|Yes| Q[使用 PTY 执行]
    P -->|No| R[使用标准执行]

    Q --> S[捕获 stdout/stderr]
    R --> S

    S --> T{超时？}
    T -->|Yes| U[终止进程]
    T -->|No| V[获取退出码]

    U --> W[返回超时错误]
    V --> X[返回执行结果]

    X --> Y[handleToolExecutionEnd]
    W --> Y
    D --> Y
    N --> Y

    Y --> Z[记录到 Session Manager]
</pre>

---

## 事件订阅系统

### 事件类型

```typescript
type EmbeddedPiSubscribeEvent =
  | { type: "message_start"; message: AgentMessage }
  | { type: "message_update"; message: AgentMessage }
  | { type: "message_end"; message: AgentMessage }
  | { type: "tool_execution_start"; toolName: string; toolCallId: string; args: unknown }
  | { type: "tool_execution_update"; toolName: string; toolCallId: string; partialResult: unknown }
  | { type: "tool_execution_end"; toolName: string; toolCallId: string; result: unknown; isError: boolean }
  | { type: "agent_start" }
  | { type: "agent_end" }
  | { type: "auto_compaction_start" }
  | { type: "auto_compaction_end" }
  | { type: "thinking_start"; delta: string; content: string }
  | { type: "thinking_delta"; delta: string; content: string }
  | { type: "thinking_end"; delta: string; content: string }
  | { type: "text_start"; delta: string; content: string }
  | { type: "text_delta"; delta: string; content: string }
  | { type: "text_end"; delta: string; content: string }
  | { type: "done" };
```

### 事件处理器注册

<pre class="mermaid">
flowchart LR
    A[Session 事件流] --> B[createEmbeddedPiSessionEventHandler]

    B --> C{事件类型判断}

    C -->|"message_*"| D[handleMessageStart/Update/End]
    C -->|"tool_execution_*"| E[handleToolExecutionStart/Update/End]
    C -->|"agent_*"| F[handleAgentStart/End]
    C -->|"auto_compaction_*"| G[handleAutoCompactionStart/End]

    D --> H[更新 assistant 消息状态]
    E --> I[执行工具处理和钩子]
    F --> J[生命周期管理]
    G --> K[上下文压缩管理]

    H --> L[触发回调]
    I --> L
    J --> L
    K --> L

    L --> M[onPartialReply / onBlockReply / onToolResult / ...]
</pre>

### 订阅状态管理

```typescript
function subscribeEmbeddedPiSession(params) {
  const state: EmbeddedPiSubscribeState = {
    // 响应累积
    assistantTexts: [],
    toolMetas: [],

    // 推理状态
    reasoningMode: params.reasoningMode ?? "off",
    includeReasoning: params.reasoningMode === "on",
    streamReasoning: params.reasoningMode === "stream",
    reasoningStreamOpen: false,

    // 流式处理
    deltaBuffer: "",
    blockBuffer: "",
    blockChunker: new EmbeddedBlockChunker(),

    // 标签状态
    blockState: { thinking: false, final: false, inlineCode: {...} },
    partialBlockState: { thinking: false, final: false, inlineCode: {...} },

    // 消息追踪
    assistantMessageIndex: 0,
    assistantTextBaseline: 0,
    lastAssistantTextNormalized: undefined,

    // Messaging 工具追踪
    messagingToolSentTexts: [],
    messagingToolSentTextsNormalized: [],
    pendingMessagingTexts: new Map(),

    // 压缩状态
    compactionInFlight: false,
    pendingCompactionRetry: 0,

    // 使用情况
    usageTotals: { input: 0, output: 0, cacheRead: 0, cacheWrite: 0 },
  };

  // 创建事件处理器
  const handler = createEmbeddedPiSessionEventHandler({ params, state, log });

  // 订阅 session 事件
  const unsubscribe = session.agent.subscribe(handler);

  return {
    assistantTexts: state.assistantTexts,
    toolMetas: state.toolMetas,
    unsubscribe,
    waitForCompactionRetry,
    getUsageTotals,
    ...
  };
}
```

---

## 会话管理

### Session 文件结构

```
~/.openclaw/agents/<agentId>/sessions/
├── <sessionId>.jsonl          # 会话记录 (JSON Lines)
├── <sessionId>.meta.json      # 元数据
└── <sessionId>.lock           # 写锁文件
```

### JSONL 格式

```json
{"type":"message","role":"user","content":"Hello","timestamp":1234567890}
{"type":"message","role":"assistant","content":"Hi there!","timestamp":1234567891}
{"type":"tool_call","id":"tc_001","name":"bash","params":{"command":"ls -la"},"timestamp":1234567892}
{"type":"tool_result","id":"tc_001","result":"total 48...","timestamp":1234567893}
{"type":"message","role":"assistant","content":"Here are the files...","timestamp":1234567894}
```

### Session Manager 架构

<pre class="mermaid">
flowchart LR
    subgraph SM["Session Manager"]
        A["SessionManager.open\n加载 JSONL 文件到内存树结构"]
        B["buildLeafTree\n构建分支树"]
        C["Session Context\nmessages[]\ntoolDefs[]\ncurrentLeaf"]
    end

    A --> B
    B --> C

    subgraph Operations["关键操作"]
        D["branch\n切换分支"]
        E["resetLeaf\n重置叶节点"]
        F["getLeafEntry\n获取叶节点"]
        G["buildSessionCtx\n构建上下文"]
        H["append\n追加条目"]
        I["flush\n持久化"]
    end
```

| 方法 | 描述 |
|------|------|
| `branch(parentId)` | 切换到指定父节点创建新分支 |
| `resetLeaf()` | 重置当前叶节点 |
| `getLeafEntry()` | 获取当前叶节点条目 |
| `buildSessionCtx()` | 构建会话上下文 (messages + tools) |
| `append(entry)` | 追加新条目到当前分支 |
| `flush()` | 持久化到 JSONL 文件 |

### 会话队列机制

```typescript
// src/agents/pi-embedded-runner/runs.ts

// 按会话键创建队列，确保同一会话的消息串行执行
const sessionQueues = new Map<string, AsyncQueue>();

export async function queueEmbeddedPiMessage(params: {
  sessionKey: string;
  message: string;
}): Promise<void> {
  let queue = sessionQueues.get(params.sessionKey);
  if (!queue) {
    queue = createAsyncQueue();
    sessionQueues.set(params.sessionKey, queue);
  }

  // 队列等待当前运行完成
  await queue.add(async () => {
    // 如果正在流式输出，使用 steer 注入
    if (activeRun?.isStreaming) {
      await activeRun.queueMessage(params.message);
    } else {
      // 否则启动新运行
      await runEmbeddedPiAgent({...});
    }
  });
}
```

---

## 上下文管理

### Session 消息结构

<pre class="mermaid">
graph LR
    subgraph SessionManager["Session Manager (JSONL 存储)"]
        A[Leaf Entry Tree]
    end

    subgraph Messages["消息结构"]
        B1[User Message]
        B2[Assistant Message]
        B3[Tool Call]
        B4[Tool Result]
    end

    subgraph Context["LLM Context"]
        C1[System Prompt]
        C2[Bootstrap Files]
        C3[History Messages]
        C4[Tool Definitions]
    end

    A --> B1
    A --> B2
    A --> B3
    A --> B4

    B1 --> C3
    B2 --> C3
    B3 --> C3
    B4 --> C3
</pre>

### 消息格式

```typescript
// User Message
{
  "role": "user",
  "content": "帮我查看当前目录的文件"
}

// Assistant Message (文本响应)
{
  "role": "assistant",
  "content": "好的，我来查看当前目录的文件列表。"
}

// Assistant Message (工具调用)
{
  "role": "assistant",
  "content": [
    {
      "type": "toolCall",
      "id": "tc_001",
      "name": "bash",
      "input": { "command": "ls -la" }
    }
  ]
}

// Tool Result
{
  "role": "user",
  "content": [
    {
      "type": "toolResult",
      "toolCallId": "tc_001",
      "output": "total 48\ndrwxr-xr-x  5 user staff  160 Mar  4 10:00 .\n..."
    }
  ]
}
```

### 上下文更新流程

<pre class="mermaid">
sequenceDiagram
    participant Agent as Agent
    participant SessionMgr as Session Manager
    participant LLM as LLM API

    Agent->>SessionMgr: append tool_result entry
    SessionMgr->>SessionMgr: flush to JSONL file
    SessionMgr-->>Agent: acknowledged

    Agent->>SessionMgr: buildSessionContext()
    SessionMgr->>SessionMgr: traverse leaf tree
    SessionMgr->>SessionMgr: collect messages
    SessionMgr-->>Agent: { messages[], tools[] }

    Agent->>LLM: stream(context.messages)
    LLM-->>Agent: continue reasoning or response
</pre>

---

## 错误处理与重试

### 故障转移策略

| 错误类型 | 处理策略 |
|----------|----------|
| 认证失败 | 切换到下一个认证配置 |
| 速率限制 | 冷却后重试/切换配置 |
| 上下文溢出 | 自动压缩/切换小上下文模型 |
| 超时 | 重试/切换模型 |
| 工具错误 | 返回错误给 LLM，继续循环 |
| 网络错误 | 指数退避重试 |

### 错误分类与处理流程

<pre class="mermaid">
flowchart TD
    A[错误发生] --> B{错误类型}

    B -->|tool_error| C[记录到 lastToolError]
    C --> D[LLM 看到错误结果]
    D --> E[LLM 决定重试或继续]

    B -->|auth_error| F[markAuthProfileFailure]
    F --> G[切换到下一个认证配置]
    G --> H[重试请求]

    B -->|rate_limit| I[设置 cooldown 计时器]
    I --> J[等待冷却结束]
    J --> K[重试或切换配置]

    B -->|context_overflow| L[触发自动压缩]
    L --> M[compactEmbeddedPiSession]
    M --> N{压缩成功？}
    N -->|Yes| O[使用压缩后上下文重试]
    N -->|No| P[切换小上下文模型]

    B -->|timeout| Q[检查是否在压缩中]
    Q --> R{压缩中？}
    R -->|Yes| S[使用压缩前快照]
    R -->|No| T[直接重试]

    B -->|network_error| U[指数退避]
    U --> V[重试请求]
</pre>

### 重试循环结构

<pre class="mermaid">
flowchart TD
    Start([开始重试循环]) --> SelectProfile["选择认证配置\nprofileOrder"]
    SelectProfile --> CheckCooldown["检查冷却状态\nisProfileInCooldown"]
    CheckCooldown --> RunAttempt[运行 attempt]
    RunAttempt --> ResultCheck{结果类型}

    ResultCheck -->|Success| Return([返回成功])
    ResultCheck -->|AuthError| MarkFailure["markAuthProfileFailure\n继续下一次迭代"]
    ResultCheck -->|RateLimit| Cooldown["cooldown\n继续下一次迭代"]
    ResultCheck -->|ContextOverflow| Compact["compact\n继续下一次迭代"]
    ResultCheck -->|Timeout| Retry[继续下一次迭代]

    MarkFailure --> NextIter{iteration < maxIterations?}
    Cooldown --> NextIter
    Compact --> NextIter
    Retry --> NextIter

    NextIter -->|Yes| SelectProfile
    NextIter -->|No| Fail([失败返回])
</pre>

### 最大迭代次数计算

```typescript
const maxIterations = clamp(
  BASE_ITERATIONS + profileCount * ITERATIONS_PER_PROFILE,
  MIN_ITERATIONS,
  MAX_ITERATIONS
);
// 典型值：32-160 次迭代
```

### 工具错误处理

```typescript
// handleToolExecutionEnd - 错误处理分支
if (isToolError) {
  // 提取错误消息
  const errorMessage = extractToolErrorMessage(sanitizedResult);

  // 记录错误
  ctx.state.lastToolError = {
    toolName,
    meta,
    error: errorMessage,
    mutatingAction: callSummary?.mutatingAction,
    actionFingerprint: callSummary?.actionFingerprint,
  };

  // 如果是 mutating 操作失败，保持错误状态直到成功
  if (ctx.state.lastToolError.mutatingAction) {
    // 不会清除，等待相同 action 成功
  } else {
    // 非 mutating 错误在下次成功时清除
  }
} else if (ctx.state.lastToolError) {
  // 检查是否解决了之前的错误
  if (isSameToolMutationAction(ctx.state.lastToolError, {
    toolName,
    meta,
    actionFingerprint: callSummary?.actionFingerprint,
  })) {
    // 相同 mutating action 成功，清除错误
    ctx.state.lastToolError = undefined;
  } else {
    // 其他成功，清除错误
    ctx.state.lastToolError = undefined;
  }
}
```

### 上下文溢出处理

```typescript
// src/agents/pi-embedded-runner/run.ts

// 上下文溢出检测和自动压缩
async function handleContextOverflow(params: {
  sessionManager: SessionManager;
  model: ModelInfo;
}): Promise<void> {
  const diagId = createCompactionDiagId();
  const compactionTimeoutSnapshot = selectCompactionTimeoutSnapshot(cfg);

  try {
    // 执行压缩
    const compactResult = await compactEmbeddedPiSessionDirect({
      sessionManager: params.sessionManager,
      model: params.model,
      timeoutMs: compactionTimeoutSnapshot.compactMs,
      provider: params.model.provider,
      modelId: params.modelId,
      authStorage: authStorage,
      runId: params.runId,
    });

    // 压缩成功后继续
    return { shouldRetry: true };
  } catch (compactionErr) {
    // 压缩失败，可能需要切换模型
    if (isCompactionFailureError(compactionErr)) {
      return { shouldRetry: true, switchModel: true };
    }
    throw compactionErr;
  }
}
```

### 压缩超时恢复

```typescript
// 压缩超时快照选择
const snapshotSelection = selectCompactionTimeoutSnapshot({
  timedOutDuringCompaction,
  preCompactionSnapshot,
  preCompactionSessionId,
  currentSnapshot: activeSession.messages.slice(),
  currentSessionId: activeSession.sessionId,
});

// 如果压缩中超时，使用压缩前的快照
if (timedOutDuringCompaction && preCompactionSnapshot) {
  messagesSnapshot = preCompactionSnapshot;
  sessionIdUsed = preCompactionSessionId;
  log.warn(`using pre-compaction snapshot: timed out during compaction`);
} else {
  // 否则使用当前快照
  messagesSnapshot = currentSnapshot;
  sessionIdUsed = currentSessionId;
}
```

---

## 插件系统

### 插件架构

<pre class="mermaid">
mindmap
  root((插件系统))
    插件来源
      Bundled - 捆绑插件
      Managed - ~/.openclaw
      Workspace - [workspace]/skills
    插件类型
      Channel Plugin - 通信渠道集成
      Tool Plugin - 工具扩展
      Skill Plugin - 技能包 (Python/Shell/Node)
      Hook Plugin - 生命周期钩子
      Service Plugin - 后台服务 (cron/webhook)
</pre>

### Hook 系统

```typescript
// src/plugins/types.ts

interface PluginHooks {
  // LLM 输入阶段
  llm_input: (event: {
    runId: string;
    sessionId: string;
    provider: string;
    model: string;
    systemPrompt: string;
    prompt: string;
    historyMessages: AgentMessage[];
  }) => Promise<void>;

  // LLM 输出阶段
  llm_output: (event: {
    runId: string;
    sessionId: string;
    response: string;
    usage: Usage;
  }) => Promise<void>;

  // Agent 开始前 (可覆盖 provider/model/systemPrompt)
  before_agent_start: (event: {
    prompt: string;
    messages: AgentMessage[];
  }) => Promise<{
    providerOverride?: string;
    modelOverride?: string;
    systemPrompt?: string;
    prependContext?: string;
  }>;

  // 工具调用前
  before_tool_call: (event: {
    toolName: string;
    params: Record<string, unknown>;
  }) => Promise<void>;

  // 工具调用后
  after_tool_call: (event: {
    toolName: string;
    result: unknown;
  }) => Promise<void>;
}
```

### Hook 执行流程

<pre class="mermaid">
sequenceDiagram
    participant Agent as Agent Run
    participant Hook as Hook System

    Note over Hook: before_model_resolve - 可覆盖 provider/model

    Note over Hook: before_agent_start / before_prompt_build - 可覆盖 systemPrompt/prependContext

    loop ReAct 循环
        Note over Hook: llm_input - 审计/修改输入

        loop 工具调用
            Note over Hook: before_tool_call - 可修改/阻止工具调用
            Note over Hook: after_tool_call - 审计/修改结果
        end

        Note over Hook: llm_output - 审计/修改输出
    end

    Note over Hook: after_agent_end - 清理/审计
</pre>

---

## 配置系统

### 配置文件结构

```json
// ~/.openclaw/openclaw.json

{
  // Gateway 配置
  "gateway": {
    "bind": "loopback",
    "port": 18789,
    "auth": {
      "mode": "password",
      "password": "..."
    },
    "tailscale": {
      "mode": "off|serve|funnel"
    }
  },

  // Agent 配置
  "agents": {
    "defaults": {
      "workspace": "~/.openclaw/workspace",
      "model": "anthropic/claude-sonnet-4-5",
      "blockStreamingDefault": "off",
      "heartbeat": {
        "prompt": "..."
      }
    }
  },

  // 模型配置
  "models": {
    "providers": {
      "anthropic": {
        "authType": "api-key"
      },
      "openai": {
        "authType": "oauth"
      }
    }
  },

  // 渠道配置
  "channels": {
    "telegram": {
      "allowFrom": ["+1234567890"],
      "dmPolicy": "pairing"
    },
    "whatsapp": {
      "allowFrom": ["*"]
    }
  },

  // 工具配置
  "tools": {
    "exec": {
      "applyPatch": true,
      "allowlist": ["bash", "read", "write", "edit"]
    }
  },

  // 技能配置
  "skills": {
    "entries": [
      { "name": "github", "enabled": true },
      { "name": "canvas", "enabled": true }
    ]
  }
}
```

### 配置加载流程

<pre class="mermaid">
flowchart TD
    A[1. 加载 .env 文件] --> B["2. 加载主配置文件\n~/.openclaw/openclaw.json"]
    B --> C[3. 加载 include 文件]
    C --> D[4. 应用环境变量覆盖]
    D --> E[5. 验证配置 Schema]
    E --> F[6. 迁移旧版配置]
    F --> G[7. 返回最终配置对象]
</pre>

---

## 完整 ReAct 循环示例

### 用户请求："帮我创建一个新项目"

<pre class="mermaid">
sequenceDiagram
    autonumber
    participant User
    participant Agent
    participant LLM
    participant Tool

    User->>Agent: "帮我创建一个新项目"

    Agent->>LLM: prompt + history

    LLM-->>Agent: thinking: "用户想创建项目，需要使用 bash 执行 mkdir..."

    Agent-->>User: onReasoningStream "正在思考..."

    LLM-->>Agent: tool_call: bash("mkdir new-project && cd new-project")

    Agent->>Tool: execute("bash", {command: "mkdir..."})
    Tool-->>Agent: tool_result: ""

    Agent->>LLM: continue (tool result)

    LLM-->>Agent: tool_call: write({path: "package.json", content: "..."})

    Agent->>Tool: execute("write", {...})
    Tool-->>Agent: tool_result: "File created successfully"

    Agent->>LLM: continue (tool result)

    LLM-->>Agent: text: "项目创建完成！已创建目录和 package.json"

    Agent-->>User: onBlockReply "项目创建完成！..."

    LLM-->>Agent: done

    Agent-->>User: 返回完整结果
</pre>

### 最终响应格式

```typescript
{
  // 成功标志
  aborted: false,
  timedOut: false,
  promptError: null,

  // 使用情况
  attemptUsage: {
    input: 1500,
    output: 500,
    cacheRead: 10000,
    cacheWrite: 0,
    total: 12000
  },

  // 工具调用
  toolMetas: [
    { toolName: "bash", meta: "mkdir" },
    { toolName: "write", meta: "package.json" }
  ],

  // 响应文本
  assistantTexts: [
    "项目创建完成！已创建目录和 package.json",
    "\n\n运行 `cd new-project && npm install` 开始使用。"
  ],

  // 压缩统计
  compactionCount: 0
}
```

---

## 总结

OpenClaw 的 Agent ReAct 实现具有以下特点：

### 架构优势

| 特点 | 描述 |
|------|------|
| **本地优先** | 所有数据和控制在本地，支持完全离线 |
| **插件化** | 所有功能通过插件扩展，易于定制 |
| **多模型支持** | 支持 20+ AI 提供商，自动故障转移 |
| **会话持久化** | JSONL 存储，支持恢复和分支 |
| **流式处理** | 支持工具调用和响应的流式输出 |

### 核心技术栈

- **运行时**: Node.js ≥22, TypeScript
- **AI 框架**: @mariozechner/pi-agent-core, pi-ai, pi-coding-agent
- **会话管理**: 自定义 SessionManager (JSONL 存储)
- **工具执行**: 进程监督器 + PTY 支持
- **通信**: WebSocket (Gateway), REST API (渠道)

### ReAct 循环关键数据

<pre class="mermaid">
graph LR
    subgraph Metrics["ReAct 循环关键数据"]
        M1["最大迭代次数\n32-160 次"]
        M2["会话上下文窗口\n100k-200k tokens"]
        M3["工具调用超时\n60-300 秒"]
        M4["认证配置轮转\n无限个配置"]
        M5["会话队列并发\n每会话 1 个运行"]
    end
</pre>

---

*文档生成时间：2026-03-04*
*基于 OpenClaw 版本：2026.3.2*
