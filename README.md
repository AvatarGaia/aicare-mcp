# AIcare MCP

> **AI 康护评估 MCP Server** — 让任何 AI Agent 用一句话完成「看检测图 → 出康护评估 → 生成康复方案」。
> 由 [虚实科技](https://agent.avatargaia.top) 研发，面向康护机构、康复师、家庭照护场景。

[![MCP](https://img.shields.io/badge/MCP-streamable--http-blue)]()
[![License](https://img.shields.io/badge/license-see%20LICENSE-lightgrey)]()

---

## 这是什么

AIcare MCP 把「AIcare 智护工作台」的核心能力开放为标准 MCP 工具，任何支持 MCP 的客户端
（Claude、ChatGPT、Cursor、OpenClaw/Gaia Agent 等）都可以直接调用：

| 工具 | 作用 | 说明 |
|---|---|---|
| `assess_health_image` | 上传检测照片 → 结构化量表评估 | 舌象 / 面色 / 步态 / 指甲 / 掌纹 / 脑龄 / 骨密度 / 卒中风险 … |
| `run_health_survey` | 提交问卷/量表 → 评估结果 | 睡眠 / 呼吸 / 用药安全 / 居家照护 / 跌倒 / Braden 等 |
| `generate_care_plan` | 患者档案 + 评估结果 → **康护分析报告** | 含整体评估、风险分级、重点关注项 |
| `generate_rehab_guide` | 生成**康复指导书 / 阶段目标 / 周计划** | 长任务，异步返回 |
| `get_patient_context` | 查询关爱对象档案 | 家属 / 康护师场景 |

> ⚕️ **合规声明**：本服务提供**健康评估与康护建议**，不构成医学诊断或治疗方案。
> 所有输出需由专业人员复核。

## 服务端点

```
POST https://agent.avatargaia.top/api/mcp/aicare
Transport: streamable-http
Auth: Authorization: Bearer <API_KEY>
```

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

### 2) curl（直接调工具）

```bash
curl -X POST https://agent.avatargaia.top/api/mcp/aicare \
  -H "Authorization: Bearer $AICARE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0", "id": 1, "method": "tools/call",
    "params": {
      "name": "assess_health_image",
      "arguments": { "type": "tongue", "image_url": "https://example.com/tongue.jpg" }
    }
  }'
```

### 3) Python

```python
import requests, os
r = requests.post(
    "https://agent.avatargaia.top/api/mcp/aicare",
    headers={"Authorization": f"Bearer {os.environ['AICARE_API_KEY']}"},
    json={"jsonrpc": "2.0", "id": 1, "method": "tools/list"},
)
print(r.json())
```

## 获取 API Key

联系 `octopus@arplus.top` 开通（提供机构名称与使用场景）。

## 典型用法

```
用户：帮我看看这张舌象照片，然后给我妈出一份康护方案
Agent：① assess_health_image(type="tongue", image_url=...) → 量表评估
       ② generate_care_plan(target_id=..., assessments=[...]) → 康护报告
       ③ generate_rehab_guide(plan_id=...) → 康复指导书（长任务）
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
