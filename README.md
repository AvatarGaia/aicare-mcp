# AIcare MCP

> **AI 康护评估 MCP Server** — 让任何 AI Agent 用一句话完成「看检测图 → 出康护评估 → 生成康复方案」。
> 由 [虚实科技](https://agent.avatargaia.top) 研发，面向康护机构、康复师、家庭照护场景。

[![MCP](https://img.shields.io/badge/MCP-streamable--http-blue)]()
[![License](https://img.shields.io/badge/license-see%20LICENSE-lightgrey)]()
[![aicare-mcp MCP connector – tool definition quality and endpoint health on Glama](https://glama.ai/mcp/connectors/group.aicare/aicare-mcp/badges/score.svg)](https://glama.ai/mcp/connectors/group.aicare/aicare-mcp)

---

## 这是什么

AIcare MCP 把「AIcare 智护工作台」的 AI 健康检测能力开放为标准 MCP 工具，任何支持 MCP 的客户端
（Claude、ChatGPT、Cursor、Cline、OpenClaw/Gaia Agent 等）都可以直接调用。

| 工具 | 作用 | 计费 |
|---|---|---|
| `aicare_list_kinds` | 列出支持的检测类型、哪些已开放、拍照/问卷要求。**先调它** | 免费 |
| `aicare_detect` | 提交照片 / 问卷 / 视频做一次 AI 健康检测，返回结构化报告 | 按次 |
| `aicare_get_job` | `aicare_detect` 返回 `status=running` 时，用 `job_id` 取最终结果 | 免费 |
| `aicare_get_detection` | 凭 `healthCheckId` 取回一次检测报告 | 免费 |
| `aicare_detect_history` | 查某用户某类检测的历史报告，用于前后对比 | 免费 |
| `aicare_tongue_diagnose` | 舌象分析（等价于 `aicare_detect(kind="tongue")`） | 按次 |
| `aicare_tongue_history` | 舌诊历史（等价于 `aicare_detect_history(kind="tongue")`） | 免费 |

检测类型覆盖舌象、面部、指甲、口腔、骨密度、中风风险、睡眠、居家照护、用药安全、步态等，**实际开放哪些以 `aicare_list_kinds` 返回的 `status=live` 为准**。
每次检测除结构化数据外，还返回 `reportUrl`（给人看的报告页）和 `embedUrl`（可 iframe 嵌入）。

> ⚕️ **合规声明**：本服务提供**健康评估与康护建议**，不构成医学诊断或治疗方案。
> 所有输出需由专业人员复核。

## 服务端点

```
POST https://agent.avatargaia.top/api/mcp/aicare
Transport: streamable-http
Auth: Authorization: Bearer <API_KEY>   （或请求头 X-API-KEY: <API_KEY>）
```

## 获取 API Key（自助，30 秒）

```bash
curl -X POST https://agent.avatargaia.top/api/dev/register \
  -H "Content-Type: application/json" \
  -d '{"name":"my-agent","email":"you@example.com"}'
# → 返回 dk_ 开头的 Key（只显示一次）+ 免费体验额度
```

机构批量接入、私有化部署，联系 `octopus@arplus.top`。

## 快速开始

### 1) Claude / 支持远程 MCP 的客户端

```json
{
  "mcpServers": {
    "aicare": {
      "type": "streamable-http",
      "url": "https://agent.avatargaia.top/api/mcp/aicare",
      "headers": { "Authorization": "Bearer ${AICARE_API_KEY}" }
    }
  }
}
```

### 2) 只支持 stdio 的客户端

用已发布的 stdio 马甲 [`@avatargaia/canvas-mcp`](https://www.npmjs.com/package/@avatargaia/canvas-mcp)，把地址指到 AICare 端点：

```bash
CANVAS_MCP_URL=https://agent.avatargaia.top/api/mcp/aicare \
CANVAS_MCP_TOKEN=dk_xxxxxxxx \
npx -y @avatargaia/canvas-mcp
```

### 3) curl（直接调工具）

```bash
curl -X POST https://agent.avatargaia.top/api/mcp/aicare \
  -H "Authorization: Bearer $AICARE_API_KEY" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json, text/event-stream" \
  -d '{
    "jsonrpc": "2.0", "id": 1, "method": "tools/call",
    "params": {
      "name": "aicare_detect",
      "arguments": { "kind": "tongue", "imageUrl": "https://example.com/tongue.jpg" }
    }
  }'
```

### 4) Python

```python
import requests, os
r = requests.post(
    "https://agent.avatargaia.top/api/mcp/aicare",
    headers={
        "Authorization": f"Bearer {os.environ['AICARE_API_KEY']}",
        "Accept": "application/json, text/event-stream",
    },
    json={"jsonrpc": "2.0", "id": 1, "method": "tools/list"},
)
print(r.text)
```

## 典型用法

```
用户：帮我看看这张舌象照片
Agent：① aicare_list_kinds → 确认 tongue 已开放、拍照要求
       ② aicare_detect(kind="tongue", imageUrl=...) → 若返回 job_id
       ③ aicare_get_job(job_id) → 结构化报告 + reportUrl（发给用户打开看）
```

## 相关仓库 / 链接

- 官网：https://agent.avatargaia.top
- 服务状态：`GET /api/health`
- 反馈：Issues

## 许可

- 本仓库内的**文档与示例代码**：MIT
- **MCP 服务本身**：商业服务，需 API Key，详见 [TERMS.md](TERMS.md)

---
<sub>虚实科技 · AvatarGaia · AIcare 智能康护</sub>
