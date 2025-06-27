---
title: "用Dify使用自己定义的MCP Server遇到的两个问题"
layout: post
date: 2025-06-27
categories: tech_coding
tags:
    - model_context_protocol
---



## 情况1
你遇到的情况是：

* 用 `python main.py …` 执行时，`mcp.run(...)` 会正确调用 Uvicorn，监听 `0.0.0.0:8899` 并正常运行。
* 而用 `uv run mcp run main.py`（实际上是 `uv` CLI）执行时，却没有输出也不启动服务，脚本似乎“卡住”了。

---

### 🛠️ 原因分析

1. **`uv` CLI 执行行为差异**
   `uv run` 是一个 Rust 实现的运行器，它会：

   * 在指定的目录创建虚拟环境
   * 安装依赖
   * 然后尝试运行给定脚本
     但它不像直接运行 Python，可能不会正确触发 `if __name__ == "__main__":` 下的 `mcp.run()`，导致你的脚本入口没有被执行。

2. **已知 issue：`uv run` 中运行 mcp 会“卡住”**
   官方 GitHub issue 报告指出，`uv run` 启动 MCP 时不会正常完成执行，程序停在某处，且没有日志输出识别 ([gofastmcp.com][1], [github.com][2], [stackoverflow.com][3])。



### ✅ 总结建议

* **短期**：继续使用 `python main.py`，省心高效。
* **管理依赖**：可以让 `uv` 管，但运行脚本留给 Python。
* **不用 `uv run` 来执行服务**，这是已知不稳定/卡住的组合 ([gofastmcp.com][1])。

如果你坚持用 `uv run` 来跑服务，需要等 upstream 修复那个 issue。现在最可靠的方法还是直接用 Python。

[1]: https://gofastmcp.com/deployment/running-server?utm_source=chatgpt.com "Running Your FastMCP Server - FastMCP"
[2]: https://github.com/astral-sh/uv/issues/12658?utm_source=chatgpt.com "`uv run` not running to completion · Issue #12658 · astral-sh/uv - GitHub"
[3]: https://stackoverflow.com/questions/79237776/invalid-command-name-mainapp-when-i-execute-uvicorn-mainapp-reload?utm_source=chatgpt.com "invalid command name 'main:app' when I execute \"uvicorn main:app ..."

## 情况2
你遇到的 400（Bad Request）是因为 FastMCP 的 Streamable‑HTTP transport 不支持通过 GET 请求打开 `/mcp` 的流。目前实现上只有 POST 是有效的，而 Dify（特别是它的 MCP‑SSE 插件）期望通过 GET 建立 SSE（Server‑Sent Events）连接，才会进行后续的 tool/resource 探测。这个问题在 FastMCP 的 issue #828 已被确认：**GET /mcp 返回 400** ([github.com][1])。

---

### ✅ 两种解决思路

#### 1. 切换到 SSE 传输模式

FastMCP 支持 `transport="sse"`，它会对 `/mcp` 端点返回可用工具列表并以 SSE 持续推送内容，完全符合 MCP protocol 规范。你的 server.py 代码只需改为：

```python
if __name__ == "__main__":
    mcp.run(transport="sse", host="0.0.0.0", port=8899)
```

然后在 Dify 的配置里指向 `http://你的地址:8899/mcp`，用 GET 请求就能正常订阅工具列表，不会再 400。

#### 2. 等待 FastMCP 修复

FastMCP 团队已意识到这个缺陷，预计会支持 GET+Streamable-HTTP 来代替 SSE ([blog.csdn.net][2])。但目前只有 POST 可用。如果你必须用 `transport="streamable-http"`，建议先改用 SSE，等他们发布支持更新——你可以注目 issue #828。

---

### 🛠 推荐方案总结

| 方案                  | 易用性  | 可行性         | 说明                  |
| ------------------- | ---- | ----------- | ------------------- |
| 切换到 SSE transport   | ✅ 高  | ✅ 已可用       | Dify + FastMCP 完全兼容 |
| Streamable‑HTTP 等修复 | ⚠️ 中 | ❌ 当前未支持 GET | 需等待 bug 修复          |

---

### ✅ 操作建议

1. 将 `mcp.run(transport="sse", ...)` 修改为 SSE 模式；
2. Dify 中的 JSON 配置改为：

```json
{
  "sbc": {
    "url": "http://10.99.42.70:8899/mcp",
    "transport": "streamable_http",
    "headers": { "Accept": "application/json, text/event-stream" }
  }
}
```

（注意 `url` 中可不加 `/` 结尾）

3. 保存并重新运行，在 server 日志里你会看到类似 `GET /mcp HTTP/1.1` 被处理，并返回工具列表和流。

---

### 🔁 后续跟进

可以关注 FastMCP GitHub 上的问题 #828，等他们添加完整 GET support。当前 SSE 路径是最稳妥、最快速的解决方案。

[1]: https://github.com/jlowin/fastmcp/issues/828?utm_source=chatgpt.com "Support GET request to /mcp endpoint · Issue #828 · jlowin/fastmcp"
[2]: https://blog.csdn.net/weixin_44894663/article/details/147858052?utm_source=chatgpt.com "dify插件接入fastmcp示例 - CSDN博客"

