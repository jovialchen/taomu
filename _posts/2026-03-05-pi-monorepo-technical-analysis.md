---
layout: post
date: 2026-03-05
title: "Pi Monorepo 鎶€鏈灦鏋勫垎鏋愭枃妗?
categories: tech_coding
tags:
  - LLM
  - AIAgent
  - TypeScript
  - OpenSource
---

## Pi Monorepo 鎶€鏈灦鏋勫垎鏋愭枃妗?
> **鎽樿锛?* Pi Monorepo 鏄竴涓敤浜庢瀯寤?AI 浠ｇ悊鍜岀鐞?LLM 閮ㄧ讲鐨勫伐鍏烽泦銆傝繖鏄竴涓?TypeScript 缂栧啓鐨?monorepo 椤圭洰锛岄噰鐢?npm workspaces 绠＄悊澶氫釜鍖呫€傛湰鏂囨繁鍏ュ垎鏋愬叾鏋舵瀯璁捐銆佹牳蹇冪粍浠跺拰鎵╁睍绯荤粺銆?
---

## 1. 椤圭洰姒傝堪

**Pi Monorepo** 鏄竴涓敤浜庢瀯寤?AI 浠ｇ悊鍜岀鐞?LLM 閮ㄧ讲鐨勫伐鍏烽泦銆傝繖鏄竴涓?TypeScript 缂栧啓鐨?monorepo 椤圭洰锛岄噰鐢?npm workspaces 绠＄悊澶氫釜鍖呫€?
### 1.1 浠撳簱淇℃伅
- **浠撳簱鍦板潃**: https://github.com/badlogic/pi-mono
- **License**: MIT
- **Node 鐗堟湰瑕佹眰**: >=20.0.0
- **鍖呯鐞嗗櫒**: npm (workspaces)

### 1.2 鏍稿績璁捐鐞嗗康
- **鏈€灏忓寲鏍稿績**: 鏍稿績鍔熻兘淇濇寔绮剧畝锛岄€氳繃鎵╁睍绯荤粺娣诲姞鍔熻兘
- **鍙墿灞曟€?*: 鏀寔 Extensions銆丼kills銆丳rompt Templates銆乀hemes
- **澶氭彁渚涜€呮敮鎸?*: 缁熶竴鐨?LLM API锛屾敮鎸?15+ 涓彁渚涜€?- **璺ㄥ钩鍙?*: 鏀寔 Windows銆丩inux銆乵acOS銆乀ermux (Android)

---

## 2. Monorepo 缁撴瀯

```
pi-mono/
鈹溾攢鈹€ packages/
鈹?  鈹溾攢鈹€ ai/           # @mariozechner/pi-ai - 缁熶竴 LLM API
鈹?  鈹溾攢鈹€ agent/        # @mariozechner/pi-agent-core - Agent 杩愯鏃?鈹?  鈹溾攢鈹€ coding-agent/ # @mariozechner/pi-coding-agent - 浜や簰寮忕紪鐮佷唬鐞?CLI
鈹?  鈹溾攢鈹€ mom/          # @mariozechner/pi-mom - Slack 鏈哄櫒浜?鈹?  鈹溾攢鈹€ tui/          # @mariozechner/pi-tui - 缁堢 UI 搴?鈹?  鈹溾攢鈹€ web-ui/       # @mariozechner/pi-web-ui - Web 鑱婂ぉ UI 缁勪欢
鈹?  鈹斺攢鈹€ pods/         # @mariozechner/pi-pods - vLLM 閮ㄧ讲绠＄悊 CLI
鈹溾攢鈹€ scripts/          # 鏋勫缓鍜屽彂甯冭剼鏈?鈹溾攢鈹€ .pi/              # 閰嶇疆鐩綍缁撴瀯绀轰緥
鈹斺攢鈹€ docs/             # 鏂囨。
```

---

## 3. 鍚勫寘璇︾粏鍒嗘瀽

### 3.1 @mariozechner/pi-ai (packages/ai)

**瀹氫綅**: 缁熶竴鐨勫鎻愪緵鑰?LLM API 灞?
**鏍稿績鍔熻兘**:
- 缁熶竴鐨勬祦寮?API 鎺ュ彛
- 鑷姩妯″瀷鍙戠幇鍜屾彁渚涜€呴厤缃?- Token 鍜屾垚鏈拷韪?- 涓婁笅鏂囧簭鍒楀寲鍜岃法鎻愪緵鑰呭垏鎹?
**鏀寔鐨勬彁渚涜€?*:

| 鎻愪緵鑰?| API 绫诲瀷 | 璁よ瘉鏂瑰紡 |
|--------|---------|---------|
| Anthropic | 鍘熺敓 API | API Key / OAuth |
| OpenAI | Chat Completions / Responses API | API Key / OAuth |
| Google | Gemini API / Vertex AI | API Key / OAuth |
| Amazon Bedrock | Converse API | AWS Credentials |
| Azure OpenAI | Responses API | API Key |
| Mistral | 鍘熺敓 API | API Key |
| Groq | OpenAI 鍏煎 | API Key |
| xAI | OpenAI 鍏煎 | API Key |
| OpenRouter | OpenAI 鍏煎 | API Key |
| Cerebras | OpenAI 鍏煎 | API Key |
| Vercel AI Gateway | OpenAI 鍏煎 | API Key |
| GitHub Copilot | 鍘熺敓 API | OAuth |
| Google Gemini CLI | 鍘熺敓 API | OAuth |
| Google Antigravity | 鍘熺敓 API | OAuth |
| Hugging Face | OpenAI 鍏煎 | API Key |

**鏍稿績鎶€鏈粍浠?*:

```typescript
// src/types.ts - 鏍稿績绫诲瀷瀹氫箟
interface Model<O> {
  id: string;
  provider: string;
  api: Api;  // API 绫诲瀷鏍囪瘑
  options: O;
}

interface Message {
  role: "user" | "assistant" | "toolResult";
  content: Array<TextContent | ImageContent | ToolContent>;
}

interface ToolDefinition {
  name: string;
  description: string;
  parameters: TSchema;  // TypeBox schema
}

// src/stream.ts - 娴佸紡澶勭悊
streamSimple<T extends Api>(model: Model<T>, context: StreamContext<T>): AsyncIterable<AssistantMessageEvent>
```

**鍖呬緷璧?*:
- `@anthropic-ai/sdk` - Anthropic 瀹㈡埛绔?- `openai` - OpenAI 瀹㈡埛绔?- `@google/genai` - Google AI 瀹㈡埛绔?- `@aws-sdk/client-bedrock-runtime` - AWS Bedrock
- `@sinclair/typebox` - Schema 楠岃瘉
- `zod-to-json-schema` - Zod 杞?JSON Schema

---

### 3.2 @mariozechner/pi-agent-core (packages/agent)

**瀹氫綅**: 鐘舵€佺鐞嗙殑 Agent 杩愯鏃舵鏋?
**鏍稿績鏋舵瀯**:

```typescript
// Agent 鐘舵€佺鐞?interface AgentState {
  systemPrompt: string;
  model: Model<any>;
  thinkingLevel: ThinkingLevel;
  tools: AgentTool<any>[];
  messages: AgentMessage[];
  isStreaming: boolean;
  streamMessage: AgentMessage | null;
  pendingToolCalls: Set<string>;
  error?: string;
}

// 浜嬩欢绯荤粺
type AgentEvent =
  | { type: "agent_start" }
  | { type: "agent_end"; messages: AgentMessage[] }
  | { type: "turn_start" }
  | { type: "turn_end"; message: AgentMessage; toolResults: ToolResult[] }
  | { type: "message_start"; message: AgentMessage }
  | { type: "message_update"; message: AgentMessage }
  | { type: "message_end"; message: AgentMessage }
  | { type: "tool_execution_start"; toolCallId: string; toolName: string; args: any }
  | { type: "tool_execution_update"; toolCallId: string; partialResult: ToolResult }
  | { type: "tool_execution_end"; toolCallId: string; result: ToolResult };
```

**鏍稿績鐗规€?*:

1. **娑堟伅娴佽浆**:
   ```
   AgentMessage[] 鈫?transformContext() 鈫?AgentMessage[] 鈫?convertToLlm() 鈫?Message[] 鈫?LLM
   ```

2. **Steering 鍜?Follow-up**:
   - `steer()`: 涓柇褰撳墠宸ュ叿鎵ц锛屾彃鍏?steering 娑堟伅
   - `followUp()`: 鍦?agent 瀹屾垚鍚庢墽琛?follow-up 娑堟伅
   - 鏀寔 `"one-at-a-time"` 鍜?`"all"` 涓ょ妯″紡

3. **宸ュ叿绯荤粺**:
   ```typescript
   interface AgentTool<T> {
     name: string;
     label?: string;
     description: string;
     parameters: TSchema;
     execute: (
       toolCallId: string,
       params: T,
       signal: AbortSignal,
       onUpdate?: (partial: ToolResult) => void
     ) => Promise<ToolResult>;
   }
   ```

4. **Session 绠＄悊**: 閫氳繃 `sessionId` 鏀寔鎻愪緵鑰呯骇鍒殑涓婁笅鏂囩紦瀛?
**鍖呬緷璧?*:
- `@mariozechner/pi-ai` - LLM API 灞?
#### 3.2.1 Agent 鐘舵€佹満

Agent 鐨勬牳蹇冩槸涓€涓弻灞傚惊鐜姸鎬佹満锛氬灞傚惊鐜鐞?Follow-up 娑堟伅闃熷垪锛屽唴灞傚惊鐜鐞嗗伐鍏疯皟鐢ㄥ拰 Steering 娑堟伅銆?
<pre class="mermaid">
stateDiagram-v2
    [*] --> Idle

    state Running {
        state InnerLoop {
            CheckSteering: 妫€鏌?Steering 娑堟伅
            StreamResponse: LLM 娴佸紡鍝嶅簲
            CheckToolCalls: 鏈夊伐鍏疯皟鐢紵
            ExecuteTools: 鎵ц宸ュ叿
            CheckSteeringAfter: 妫€鏌?Steering 娑堟伅 (宸ュ叿鍚?

            [*] --> CheckSteering
            CheckSteering --> StreamResponse
            StreamResponse --> CheckToolCalls
            CheckToolCalls --> ExecuteTools: 鏈夊伐鍏疯皟鐢?            CheckToolCalls --> CheckSteeringAfter: 鏃犲伐鍏疯皟鐢?            ExecuteTools --> CheckSteeringAfter
            CheckSteeringAfter --> CheckSteering: 鏈?Steering 娑堟伅
            CheckSteeringAfter --> [*]: 鏃?Steering 娑堟伅
        }

        CheckFollowUp: 妫€鏌?Follow-up 娑堟伅

        InnerLoop --> CheckFollowUp: 鍐呭眰寰幆瀹屾垚
        CheckFollowUp --> InnerLoop: 鏈?Follow-up 娑堟伅
        CheckFollowUp --> [*]: 鏃?Follow-up 娑堟伅
    }

    Idle --> Running: prompt() / continue()
    Running --> Idle: agent_end 浜嬩欢
    Running --> Error: error / aborted
    Error --> Idle: reset()
</pre>

#### 3.2.2 鐘舵€佽鏄?
| 鐘舵€?| 瑙﹀彂鏉′欢 | 杞Щ |
|------|---------|------|
| `Idle` | 鍒濆鐘舵€佹垨 `reset()` 鍚?| 鈫?`Running` (璋冪敤 `prompt()` 鎴?`continue()`) |
| `Running` | `prompt()` / `continue()` 璋冪敤 | 鈫?`Idle` (`agent_end`) 鎴?鈫?`Error` (閿欒/涓) |
| `CheckSteering` | 姣忎釜 turn 寮€濮嬪墠 | 妫€鏌?steering 闃熷垪锛屾敞鍏ユ秷鎭悗杩涘叆娴佸紡鍝嶅簲 |
| `StreamResponse` | LLM 娴佸紡璋冪敤 | 鍙戝嚭 `message_start/update/end` 浜嬩欢 |
| `ExecuteTools` | assistant 娑堟伅鍖呭惈 toolCall | 鍙戝嚭 `tool_execution_start/update/end` 浜嬩欢 |
| `CheckFollowUp` | 鍐呭眰寰幆瀹屾垚鍚?| 鏈?follow-up 鍒欑户缁惊鐜紝鍚﹀垯閫€鍑?|
| `Error` | 閿欒鎴栬涓 | 鈫?`Idle` (璋冪敤 `reset()`) |

#### 3.2.3 浜嬩欢搴忓垪

**姝ｅ父瀵硅瘽娴佺▼** (`prompt("Hello")`):

<pre class="mermaid">
sequenceDiagram
    participant User
    participant Agent
    participant LLM
    participant Listener

    User->>Agent: prompt("Hello")
    Agent->>Listener: agent_start
    Agent->>Listener: turn_start
    Agent->>Listener: message_start (user)
    Agent->>Listener: message_end (user)
    Agent->>LLM: streamSimple(context)

    loop 娴佸紡鍝嶅簲
        LLM-->>Agent: text_delta / thinking_delta
        Agent->>Listener: message_update
    end

    LLM-->>Agent: done (finalMessage)
    Agent->>Listener: message_end (assistant)
    Agent->>Listener: turn_end
    Agent->>Listener: agent_end
    Agent-->>User: Promise resolved
</pre>

**甯﹀伐鍏疯皟鐢ㄧ殑娴佺▼** (`prompt("Read config.json")`):

<pre class="mermaid">
sequenceDiagram
    participant User
    participant Agent
    participant LLM
    participant Tool
    participant Listener

    User->>Agent: prompt("Read config.json")
    Agent->>Listener: agent_start, turn_start
    Agent->>LLM: streamSimple(context, tools)
    LLM-->>Agent: toolCall (read_file)
    Agent->>Listener: message_end (with toolCall)
    Agent->>Listener: tool_execution_start
    Agent->>Tool: execute()
    Tool-->>Agent: result
    Agent->>Listener: tool_execution_end
    Agent->>Listener: message_start/end (toolResult)
    Agent->>Listener: turn_end

    Agent->>LLM: streamSimple(context)
    LLM-->>Agent: text response
    Agent->>Listener: message_update
    LLM-->>Agent: done
    Agent->>Listener: message_end, turn_end
    Agent->>Listener: agent_end
</pre>

**Steering 娑堟伅涓柇娴佺▼**:

<pre class="mermaid">
sequenceDiagram
    participant User
    participant Agent
    participant LLM
    participant Tool1
    participant Tool2
    participant Listener

    User->>Agent: prompt("Do A and B")
    Agent->>LLM: streamSimple()
    LLM-->>Agent: toolCall_A, toolCall_B
    Agent->>Listener: message_end
    Agent->>Tool1: execute_A()
    Agent->>Listener: tool_execution_start/end (A)

    Note over Agent: Steering 娑堟伅鍒拌揪!
    User->>Agent: steer("Stop! Do C instead")

    Agent->>Listener: tool_execution_end (B, skipped)
    Agent->>Listener: message_start/end (user steering)
    Agent->>LLM: streamSimple(context)
    LLM-->>Agent: response to steering
    Agent->>Listener: agent_end
</pre>

---

### 3.3 @mariozechner/pi-coding-agent (packages/coding-agent)

**瀹氫綅**: 浜や簰寮忕紪鐮佷唬鐞?CLI锛宲i 鐨勪富瑕佺敤鎴风晫闈?
**杩愯妯″紡**:

| 妯″紡 | 璇存槑 |
|------|------|
| Interactive (榛樿) | 浜や簰寮?TUI 鐣岄潰 |
| Print (`-p`) | 鎵撳嵃鍝嶅簲鍚庨€€鍑?|
| JSON (`--mode json`) | JSONL 浜嬩欢娴佽緭鍑?|
| RPC (`--mode rpc`) | 杩涚▼闆嗘垚妯″紡 |

**鏍稿績鏋舵瀯**:

```typescript
// 鏍稿績妯″潡缁撴瀯
coding-agent/
鈹溾攢鈹€ src/
鈹?  鈹溾攢鈹€ cli/           # CLI 鍙傛暟瑙ｆ瀽鍜屽懡浠ゅ鐞?鈹?  鈹溾攢鈹€ core/
鈹?  鈹?  鈹溾攢鈹€ agent-session.ts  # Agent 浼氳瘽绠＄悊
鈹?  鈹?  鈹溾攢鈹€ model-registry.ts # 妯″瀷娉ㄥ唽鍜屽彂鐜?鈹?  鈹?  鈹溾攢鈹€ session-manager.ts # 浼氳瘽鎸佷箙鍖?鈹?  鈹?  鈹斺攢鈹€ hooks/     # 鐢熷懡鍛ㄦ湡閽╁瓙
鈹?  鈹溾攢鈹€ modes/
鈹?  鈹?  鈹溾攢鈹€ interactive/  # 浜や簰寮忔ā寮?鈹?  鈹?  鈹?  鈹溾攢鈹€ theme/    # 涓婚绯荤粺
鈹?  鈹?  鈹?  鈹溾攢鈹€ editor/   # 缂栬緫鍣ㄧ粍浠?鈹?  鈹?  鈹?  鈹斺攢鈹€ ui/       # UI 娓叉煋
鈹?  鈹?  鈹溾攢鈹€ print/     # 鎵撳嵃妯″紡
鈹?  鈹?  鈹斺攢鈹€ json/      # JSON 妯″紡
鈹?  鈹溾攢鈹€ tools/         # 鍐呯疆宸ュ叿
鈹?  鈹?  鈹溾攢鈹€ read.ts    # 璇诲彇鏂囦欢
鈹?  鈹?  鈹溾攢鈹€ write.ts   # 鍐欏叆鏂囦欢
鈹?  鈹?  鈹溾攢鈹€ edit.ts    # 缂栬緫鏂囦欢
鈹?  鈹?  鈹溾攢鈹€ bash.ts    # 鎵ц鍛戒护
鈹?  鈹?  鈹溾攢鈹€ grep.ts    # 鎼滅储鍐呭
鈹?  鈹?  鈹溾攢鈹€ find.ts    # 鏌ユ壘鏂囦欢
鈹?  鈹?  鈹斺攢鈹€ ls.ts      # 鍒楀嚭鐩綍
鈹?  鈹斺攢鈹€ extensions/    # 鎵╁睍绯荤粺
```

**鎵╁睍绯荤粺**:

```typescript
// ExtensionAPI
interface ExtensionAPI {
  // 宸ュ叿娉ㄥ唽
  registerTool(tool: AgentTool<any>): void;

  // 鍛戒护娉ㄥ唽
  registerCommand(name: string, handler: CommandHandler): void;

  // 浜嬩欢鐩戝惉
  on(event: "tool_call" | "message" | "turn_end", handler: EventHandler): void;

  // UI 缁勪欢
  ui: {
    showOverlay(component: Component, options?: OverlayOptions): OverlayHandle;
    hideOverlay(): void;
  };

  // 閰嶇疆
  settings: {
    get<T>(key: string): Promise<T>;
    set<T>(key: string, value: T): Promise<void>;
  };
}
```

**浼氳瘽绠＄悊**:
- 浼氳瘽浠?JSONL 鏍煎紡瀛樺偍鍦?`~/.pi/agent/sessions/`
- 鏍戝舰缁撴瀯鏀寔鍒嗘敮鍜岃烦杞?(`/tree`, `/fork`)
- 鑷姩鍘嬬缉闃叉涓婁笅鏂囨孩鍑?
**鍖呬緷璧?*:
- `@mariozechner/pi-ai`
- `@mariozechner/pi-agent-core`
- `@mariozechner/pi-tui`
- `@silvia-odwyer/photon-node` - 鍥惧儚澶勭悊
- `marked` - Markdown 娓叉煋
- `diff` - 浠ｇ爜宸紓鏄剧ず
- `cli-highlight` - 璇硶楂樹寒

---

### 3.4 @mariozechner/pi-tui (packages/tui)

**瀹氫綅**: 杞婚噺绾х粓绔?UI 妗嗘灦

**鏍稿績鎶€鏈?*:

1. **宸垎娓叉煋绛栫暐**:
   - **棣栨娓叉煋**: 杈撳嚭鎵€鏈夎
   - **瀹藉害鍙樺寲**: 娓呭睆骞跺畬鏁撮噸缁?   - **甯歌鏇存柊**: 浠呮覆鏌撳彉鍖栫殑琛?
2. **鍚屾杈撳嚭**: 浣跨敤 CSI 2026 瀹炵幇鍘熷瓙灞忓箷鏇存柊锛堟棤闂儊锛?
3. **缁勪欢鏋舵瀯**:

```typescript
interface Component {
  render(width: number): string[];  // 娓叉煋涓哄瓧绗︿覆鏁扮粍
  handleInput?(data: string): void; // 澶勭悊閿洏杈撳叆
  invalidate?(): void;              // 娓呴櫎缂撳瓨
}

interface Focusable extends Component {
  focused: boolean;  // IME 鍏夋爣瀹氫綅鏀寔
}
```

**鍐呯疆缁勪欢**:

| 缁勪欢 | 鐢ㄩ€?|
|------|------|
| `Container` | 缁勪欢瀹瑰櫒 |
| `Box` | 甯﹁儗鏅拰杈硅窛鐨勫鍣?|
| `Text` | 澶氳鏂囨湰锛堣嚜鍔ㄦ崲琛岋級 |
| `TruncatedText` | 鍗曡鎴柇鏂囨湰 |
| `Input` | 鍗曡杈撳叆 |
| `Editor` | 澶氳缂栬緫鍣紙鏀寔鑷姩瀹屾垚锛?|
| `Markdown` | Markdown 娓叉煋锛堝甫璇硶楂樹寒锛?|
| `SelectList` | 鍙€夋嫨鍒楄〃 |
| `SettingsList` | 璁剧疆闈㈡澘 |
| `Loader` / `CancellableLoader` | 鍔犺浇鍔ㄧ敾 |
| `Image` | 鍐呰仈鍥惧儚锛圞itty/iTerm2 鍗忚锛?|
| `Spacer` | 绌虹櫧闂撮殧 |

**鍏抽敭鎶€鏈壒鎬?*:

```typescript
// 瑕嗙洊灞傜郴缁?tui.showOverlay(component, {
  anchor: 'center' | 'top-left' | 'bottom-right' | ...,
  width: 60 | "80%",
  maxWidth: 80,
  margin: { top: 1, right: 2, bottom: 1, left: 2 },
  row: "25%",  // 鐧惧垎姣斿畾浣?  col: 10,     // 缁濆瀹氫綅
});

// 鐒︾偣鍜?IME 鏀寔
import { CURSOR_MARKER } from "@mariozechner/pi-tui";
// 鍦ㄦ覆鏌撹緭鍑轰腑鎻掑叆 CURSOR_MARKER 浠ュ畾浣嶇‖浠跺厜鏍?
// 閿洏澶勭悊
import { matchesKey, Key } from "@mariozechner/pi-tui";
if (matchesKey(data, Key.ctrl("c"))) { ... }
if (matchesKey(data, Key.shift("tab"))) { ... }
```

**鍖呬緷璧?*:
- `chalk` - ANSI 鏍峰紡
- `marked` - Markdown 瑙ｆ瀽
- `get-east-asian-width` - 涓滀簹瀛楃瀹藉害澶勭悊
- `@xterm/headless` - 娴嬭瘯鏀寔

---

### 3.5 @mariozechner/pi-mom (packages/mom)

**瀹氫綅**: Slack 鏈哄櫒浜猴紝鍙嚜涓荤鐞嗗伐鍏峰拰鍑瘉

**鏍稿績鐗规€?*:

1. **鑷鐞嗙幆澧?*:
   - 鑷姩瀹夎宸ュ叿锛坅pk銆乶pm 绛夛級
   - 鑷閰嶇疆鍑瘉
   - 鑷富鍒涘缓宸ヤ綔娴佸伐鍏凤紙Skills锛?
2. **Docker 娌欑洅**:
   ```bash
   # 鍒涘缓娌欑洅
   docker run -d --name mom-sandbox \
     -v $(pwd)/data:/workspace \
     alpine:latest tail -f /dev/null

   # 杩愯 mom
   mom --sandbox=docker:mom-sandbox ./data
   ```

3. **鏁版嵁瀛樺偍缁撴瀯**:
   ```
   data/
   鈹溾攢鈹€ MEMORY.md              # 鍏ㄥ眬璁板繂
   鈹溾攢鈹€ settings.json          # 鍏ㄥ眬璁剧疆
   鈹溾攢鈹€ skills/                # 鍏ㄥ眬鎶€鑳?   鈹溾攢鈹€ events/                # 浜嬩欢璋冨害
   鈹斺攢鈹€ <channel-id>/          # 姣忎釜 Slack 棰戦亾
       鈹溾攢鈹€ MEMORY.md          # 棰戦亾璁板繂
       鈹溾攢鈹€ log.jsonl          # 瀹屾暣娑堟伅鍘嗗彶
       鈹溾攢鈹€ context.jsonl      # LLM 涓婁笅鏂?       鈹溾攢鈹€ attachments/       # 闄勪欢
       鈹溾攢鈹€ scratch/           # 宸ヤ綔鐩綍
       鈹斺攢鈹€ skills/            # 棰戦亾鎶€鑳?   ```

4. **浜嬩欢璋冨害绯荤粺**:
   ```typescript
   // 浜嬩欢绫诲瀷
   type Event =
     | { type: "immediate"; channelId: string; text: string }  // 绔嬪嵆瑙﹀彂
     | { type: "one-shot"; channelId: string; text: string; at: string }  // 涓€娆℃€?     | { type: "periodic"; channelId: string; text: string; schedule: string };  // 鍛ㄦ湡鎬?   ```

5. **宸ュ叿闆?*:
   - `bash` - 鎵ц Shell 鍛戒护
   - `read` - 璇诲彇鏂囦欢
   - `write` - 鍒涘缓/瑕嗙洊鏂囦欢
   - `edit` - 绮剧‘缂栬緫鏂囦欢
   - `attach` - 鍙戦€佹枃浠跺埌 Slack

**鍖呬緷璧?*:
- `@mariozechner/pi-agent-core`
- `@mariozechner/pi-ai`
- `@mariozechner/pi-coding-agent`
- `@slack/socket-mode`
- `@slack/web-api`
- `croner` - Cron 璋冨害
- `@anthropic-ai/sandbox-runtime` - 娌欑洅杩愯鏃?
---

### 3.6 @mariozechner/pi-web-ui (packages/web-ui)

**瀹氫綅**: Web 鑱婂ぉ UI 缁勪欢搴?
**鎶€鏈爤**:
- mini-lit Web Components
- Tailwind CSS v4

**鏍稿績缁勪欢**:

```typescript
// ChatPanel - 楂樼骇鑱婂ぉ鐣岄潰
const chatPanel = new ChatPanel();
await chatPanel.setAgent(agent, {
  onApiKeyRequired: (provider) => ApiKeyPromptDialog.prompt(provider),
  onBeforeSend: async () => { /* 淇濆瓨鑽夌绛?*/ },
  toolsFactory: (agent, artifactsPanel) => [createJavaScriptReplTool()],
});

// AgentInterface - 搴曞眰鑱婂ぉ鐣岄潰
const chat = document.createElement('agent-interface') as AgentInterface;
chat.session = agent;
chat.enableAttachments = true;
chat.enableModelSelector = true;
```

**娑堟伅绫诲瀷鎵╁睍**:

```typescript
// 閫氳繃澹版槑鍚堝苟鎵╁睍娑堟伅绫诲瀷
interface UserMessageWithAttachments {
  role: 'user-with-attachments';
  content: string;
  attachments: Attachment[];
  timestamp: number;
}

interface ArtifactMessage {
  role: 'artifact';
  action: 'create' | 'update' | 'delete';
  filename: string;
  content: string;
  timestamp: string;
}

declare module '@mariozechner/pi-agent-core' {
  interface CustomAgentMessages {
    'user-with-attachments': UserMessageWithAttachments;
    'artifact': ArtifactMessage;
  }
}
```

**瀛樺偍绯荤粺**:

```typescript
// IndexedDB 瀛樺偍鍚庣
const backend = new IndexedDBStorageBackend({
  dbName: 'my-app',
  stores: [
    settings.getConfig(),
    providerKeys.getConfig(),
    sessions.getConfig(),
  ],
});

// 瀛樺偍 API
await storage.settings.set('proxy.enabled', true);
await storage.providerKeys.set('anthropic', 'sk-ant-...');
await storage.sessions.save(sessionData, metadata);
```

**闄勪欢澶勭悊**:
- 鏀寔鏍煎紡锛歅DF銆丏OCX銆乆LSX銆丳PTX銆佸浘鐗?- 鑷姩鏂囨湰鎻愬彇
- 棰勮鍥惧儚鐢熸垚

**鍖呬緷璧?*:
- `@mariozechner/pi-ai`
- `@mariozechner/pi-tui`
- `@mariozechner/mini-lit`
- `pdfjs-dist` - PDF 瑙ｆ瀽
- `xlsx` - Excel 澶勭悊
- `docx-preview` - Word 棰勮
- `lucide` - 鍥炬爣搴?
---

### 3.7 @mariozechner/pi-pods (packages/pods)

**瀹氫綅**: GPU Pod 涓婄殑 vLLM 閮ㄧ讲绠＄悊宸ュ叿

**鏍稿績鍔熻兘**:

1. **Pod 绠＄悊**:
   ```bash
   # 璁剧疆 Pod
   pi pods setup dc1 "ssh root@1.2.3.4" \
     --mount "sudo mount -t nfs ... /mnt/hf-models"

   # 鍒楀嚭 Pod
   pi pods

   # SSH 杩炴帴
   pi shell dc1
   ```

2. **妯″瀷绠＄悊**:
   ```bash
   # 鍚姩妯″瀷
   pi start Qwen/Qwen2.5-Coder-32B-Instruct --name qwen

   # 閰嶇疆閫夐」
   --memory <percent>    # GPU 鍐呭瓨鍒嗛厤
   --context <size>      # 涓婁笅鏂囩獥鍙?   --gpus <count>        # GPU 鏁伴噺
   --vllm <args...>      # 鐩存帴浼犻€?vLLM 鍙傛暟
   ```

3. **棰勫畾涔夋ā鍨嬮厤缃?*:
   - Qwen 绯诲垪 (2.5-Coder-32B, Qwen3-Coder-30B/480B)
   - GPT-OSS 绯诲垪 (20B, 120B)
   - GLM 绯诲垪 (GLM-4.5, GLM-4.5-Air)

4. **Agent 闆嗘垚**:
   ```bash
   # 鍗曟瀵硅瘽
   pi agent qwen "What is the Fibonacci sequence?"

   # 浜や簰妯″紡
   pi agent qwen -i

   # 缁х画涓婃浼氳瘽
   pi agent qwen -i -c
   ```

**鏀寔鐨?Pod 鎻愪緵鑰?*:
- **DataCrunch**: 鏀寔 NFS 鍏变韩瀛樺偍锛堟帹鑽愶級
- **RunPod**: 缃戠粶鍗锋寔涔呭寲
- **Vast.ai**銆?*Prime Intellect**銆?*AWS EC2**

**鍖呬緷璧?*:
- `@mariozechner/pi-agent-core`
- `chalk` - 缁堢鏍峰紡

---

## 4. 鏋勫缓鍜屽紑鍙戝伐鍏?
### 4.1 鏋勫缓绯荤粺

```json
{
  "scripts": {
    "clean": "cd packages/* && npm run clean",
    "build": "cd packages/tui && npm run build && cd ../ai && npm run build && ...",
    "dev": "concurrently ...",  // 骞惰鐩戞帶鎵€鏈夊寘
    "check": "biome check --write --error-on-warnings . && tsgo --noEmit",
    "test": "npm run test --workspaces"
  }
}
```

**鏋勫缓宸ュ叿**:
- `tsgo` (TypeScript Go) - 涓昏缂栬瘧鍣?- `concurrently` - 骞惰浠诲姟
- `biome` - 浠ｇ爜鏍煎紡鍖栧拰 linting
- `shx` - 璺ㄥ钩鍙?shell 鍛戒护

### 4.2 鍙戝竷娴佺▼

```bash
## 鐗堟湰绠＄悊锛堥攣瀹氭璋冿紝鎵€鏈夊寘鐗堟湰涓€鑷达級
npm run release:patch    # Bug 淇鍜屾柊鍔熻兘
npm run release:minor    # API 鐮村潖鎬у彉鏇?
## 鍙戝竷鑴氭湰鑷姩鍖栧鐞?
## 1. 鐗堟湰鍙锋彁鍗?# 2. CHANGELOG 瀹氱
## 3. Git 鎻愪氦鍜屾爣绛?# 4. npm 鍙戝竷
## 5. 娣诲姞鏂扮殑 [Unreleased] 绔犺妭
```

### 4.3 浠ｇ爜璐ㄩ噺

```json
{
  "devDependencies": {
    "@biomejs/biome": "2.3.5",       // Linting 鍜屾牸寮忓寲
    "@typescript/native-preview": "...",  // TypeScript 棰勮
    "typescript": "^5.9.2",
    "vitest": "^3.2.4",              // 娴嬭瘯妗嗘灦
    "husky": "^9.1.7"                // Git hooks
  }
}
```

---

## 5. 鎵╁睍绯荤粺璇﹁В

### 5.1 鎵╁睍绫诲瀷

| 绫诲瀷 | 浣嶇疆 | 鐢ㄩ€?|
|------|------|------|
| Extensions | `~/.pi/agent/extensions/`, `.pi/extensions/` | TypeScript 妯″潡锛屽畬鏁村姛鑳芥墿灞?|
| Skills | `~/.pi/agent/skills/`, `.pi/skills/` | Markdown 鏍煎紡鐨勫伐鍏疯鏄?|
| Prompt Templates | `~/.pi/agent/prompts/`, `.pi/prompts/` | 鍙鐢ㄧ殑鎻愮ず妯℃澘 |
| Themes | `~/.pi/agent/themes/`, `.pi/themes/` | TUI 涓婚 |

### 5.2 Pi Packages

```json
{
  "name": "my-pi-package",
  "keywords": ["pi-package"],
  "pi": {
    "extensions": ["./extensions"],
    "skills": ["./skills"],
    "prompts": ["./prompts"],
    "themes": ["./themes"]
  }
}
```

**瀹夎鏂瑰紡**:
```bash
pi install npm:@foo/pi-tools
pi install git:github.com/user/repo
pi install https://github.com/user/repo@v1
```

---

## 6. 瀹夊叏鑰冭檻

### 6.1 Mom 瀹夊叏椋庨櫓

1. **鎻愮ず娉ㄥ叆鏀诲嚮**:
   - 鐩存帴娉ㄥ叆锛氭伓鎰忕敤鎴风洿鎺ヨ姹傛晱鎰熶俊鎭?   - 闂存帴娉ㄥ叆锛氶€氳繃鎭舵剰鍐呭闅愯棌鎸囦护

2. **缂撹В鎺柦**:
   - 浣跨敤 Docker 娌欑洅闅旂
   - 鏈€灏忔潈闄愬師鍒欓厤缃嚟璇?   - 澶氬疄渚嬮殧绂讳笉鍚屽畨鍏ㄧ骇鍒殑鍥㈤槦

### 6.2 鎵╁睍瀹夊叏

> **璀﹀憡**: Pi 鍖呬互瀹屽叏鏉冮檺杩愯锛屾墿灞曟墽琛屼换鎰忎唬鐮侊紝Skills 鍙寚绀烘ā鍨嬫墽琛屼换浣曟搷浣溿€傚畨瑁呭墠蹇呴』瀹℃煡婧愪唬鐮併€?
---

## 7. 璁捐鍝插

### 7.1 鏍稿績鍘熷垯

1. **鏈€灏忓寲鏍稿績**: 鏍稿績涓嶅寘鎷?MCP銆佸瓙浠ｇ悊銆佹潈闄愬脊绐椼€佽鍒掓ā寮忕瓑鍔熻兘
2. **鐢ㄦ埛鑷畾涔?*: 閫氳繃鎵╁睍绯荤粺锛岀敤鎴峰彲浠ユ寜闇€娣诲姞鍔熻兘
3. **涓嶉璁惧伐浣滄祦**: 涓嶅悓鍥㈤槦鏈変笉鍚岀殑闇€姹傦紝鏍稿績涓嶅己鍒朵换浣曞伐浣滄祦

### 7.2 涓嶅唴缃殑鍔熻兘鍙婂師鍥?
| 鍔熻兘 | 鍘熷洜 | 鏇夸唬鏂规 |
|------|------|---------|
| MCP | 澧炲姞澶嶆潅鎬?| 鏋勫缓 CLI 宸ュ叿鎴栨墿灞?|
| 瀛愪唬鐞?| 瀹炵幇鏂瑰紡澶氭牱 | tmux 鎴栬嚜瀹氫箟鎵╁睍 |
| 鏉冮檺寮圭獥 | 瀹夊叏闇€姹傚悇寮?| 瀹瑰櫒闅旂鎴栬嚜瀹氫箟鎵╁睍 |
| 璁″垝妯″紡 | 宸ヤ綔娴佸樊寮傚ぇ | 鏂囦欢璁板綍鎴栨墿灞?|
| TODO 鍒楄〃 | 娣锋穯妯″瀷 | TODO.md 鏂囦欢鎴栨墿灞?|
| 鍚庡彴 bash | 鍙娴嬫€у樊 | tmux |

---

## 8. 鎶€鏈寒鐐?
### 8.1 宸垎娓叉煋
TUI 浣跨敤涓夌瓥鐣ユ覆鏌撶郴缁燂細
1. 棣栨娓叉煋锛氬畬鏁磋緭鍑?2. 瀹藉害鍙樺寲锛氭竻灞忛噸缁?3. 甯歌鏇存柊锛氫粎鍙樺寲琛?
缁撳悎 CSI 2026 鍚屾杈撳嚭锛屽疄鐜版棤闂儊娓叉煋銆?
### 8.2 浜嬩欢椹卞姩鏋舵瀯
鎵€鏈夊寘浣跨敤缁熶竴鐨勪簨浠剁郴缁燂細
```typescript
type AgentEvent =
  | { type: "agent_start" }
  | { type: "message_update"; message: AgentMessage }
  | { type: "tool_execution_end"; result: ToolResult }
  | ...
```

### 8.3 璺ㄦ彁渚涜€呭垏鎹?鏀寔鍦ㄤ細璇濅腑鏃犵紳鍒囨崲 LLM 鎻愪緵鑰咃紝淇濇寔涓婁笅鏂囪繛缁€с€?
### 8.4 鏍戝舰浼氳瘽瀛樺偍
JSONL 鏍煎紡 + 鐖跺瓙 ID 缁撴瀯锛屾敮鎸侊細
- 鍘熷湴鍒嗘敮
- 浠绘剰鐐硅烦杞?- 鍘嗗彶淇濈暀

---

## 9. 鎬荤粨

Pi Monorepo 鏄竴涓璁＄簿鑹殑 AI 浠ｇ悊宸ュ叿闆嗭紝鍏锋湁浠ヤ笅鐗圭偣锛?
1. **妯″潡鍖栬璁?*: 7 涓嫭绔嬪寘锛岃亴璐ｆ竻鏅板垎绂?2. **缁熶竴 API**: 缁熶竴鐨?LLM 鍜?Agent 鎺ュ彛
3. **楂樺害鍙墿灞?*: Extensions銆丼kills銆乀hemes銆丳ackages
4. **璺ㄥ钩鍙版敮鎸?*: 缁堢銆乄eb銆丼lack 澶氱晫闈?5. **寮€鍙戣€呭弸濂?*: TypeScript銆佸畬鏁寸被鍨嬪畾涔夈€佽缁嗘枃妗?6. **瀹夊叏鎰忚瘑**: Docker 娌欑洅銆佹潈闄愰殧绂?
**鏍稿績浠ｇ爜閲忎及璁?*:
- **pi-ai**: ~5000 琛岋紙鎻愪緵鑰呭疄鐜颁负涓伙級
- **pi-agent-core**: ~1500 琛岋紙Agent 鐘舵€佸拰浜嬩欢绠＄悊锛?- **pi-coding-agent**: ~10000+ 琛岋紙CLI銆乀UI銆佸伐鍏枫€佹墿灞曠郴缁燂級
- **pi-tui**: ~5000 琛岋紙缁勪欢搴撳拰娓叉煋寮曟搸锛?- **pi-mom**: ~3000 琛岋紙Slack 闆嗘垚鍜屾矙鐩掞級
- **pi-web-ui**: ~5000 琛岋紙Web 缁勪欢锛?- **pi-pods**: ~1500 琛岋紙Pod 鍜?vLLM 绠＄悊锛?
**鎬昏绾?30000+ 琛?TypeScript 浠ｇ爜銆?*

---

**鍙傝€冭祫鏂欙細**
- Pi Monorepo: https://github.com/badlogic/pi-mono

---

*Published: 2026-03-05*  
*Categories: Tech & Coding*  
*Tags: LLM, AIAgent, TypeScript, OpenSource*
