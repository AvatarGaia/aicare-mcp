# AIcare MCP — 调用示例

## 1. tools/list

```bash
curl -X POST https://agent.avatargaia.top/api/mcp/aicare \
  -H "Authorization: Bearer $AICARE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'
```

## 2. 看图评估（舌象）

```bash
curl -X POST https://agent.avatargaia.top/api/mcp/aicare \
  -H "Authorization: Bearer $AICARE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc":"2.0","id":2,"method":"tools/call",
    "params":{"name":"assess_health_image",
      "arguments":{"type":"tongue","image_url":"https://example.com/tongue.jpg"}}
  }'
```

## 3. 出康护报告

```bash
curl -X POST https://agent.avatargaia.top/api/mcp/aicare \
  -H "Authorization: Bearer $AICARE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc":"2.0","id":3,"method":"tools/call",
    "params":{"name":"generate_care_plan",
      "arguments":{"target_id":"10366","care_mode":2}}
  }'
```

## 4. 康复指导书（长任务）

```bash
curl -X POST https://agent.avatargaia.top/api/mcp/aicare \
  -H "Authorization: Bearer $AICARE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc":"2.0","id":4,"method":"tools/call",
    "params":{"name":"generate_rehab_guide","arguments":{"plan_id":"<PLAN_ID>"}}
  }'
# 返回 job_id，随后 poll: tools/call get_job_status
```

## 5. Node.js

```js
const res = await fetch("https://agent.avatargaia.top/api/mcp/aicare", {
  method: "POST",
  headers: {
    "Authorization": `Bearer ${process.env.AICARE_API_KEY}`,
    "Content-Type": "application/json",
  },
  body: JSON.stringify({ jsonrpc: "2.0", id: 1, method: "tools/list" }),
});
console.log(await res.json());
```
