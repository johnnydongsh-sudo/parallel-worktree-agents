# BLOCKED 协议（稳定契约）

Worker 遇到规格冲突、越界需求、无法完成时，禁止自行修改契约/越界文件，返回：

```json
{
  "protocol": "parallel-worktree-agents/v1",
  "type": "BLOCKED",
  "category": "SPEC_CONFLICT | SCOPE_EXPANSION | CONTEXT_MISSING | TOO_LARGE | SCORING_GAP | ADAPTER_SEMANTICS | RUNTIME_OVERLAP | OTHER",
  "taskId": "",
  "workerId": "",
  "observed": "",
  "expected": "",
  "whyBlocked": "",
  "options": [],
  "filesAffected": [],
  "noChangesMade": true
}
```

- `noChangesMade` 必须为 true（BLOCKED 前不得留半成品）
- Coordinator 决策后重新派发；决策记录入 run manifest
- 传输：经 Inbox content envelope（见 inbox-protocol.md），不扩展 upstream 枚举
