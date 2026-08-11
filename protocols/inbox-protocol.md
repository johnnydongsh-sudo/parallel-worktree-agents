# Inbox 协议（消息通道）

统一消息类型（content envelope，不扩展底层枚举）：

- `WORKER_READY`：spawn 确认后
- `WORKER_COMPLETED`：commit 检出后（含 commit hash）
- `BLOCKED`：见 blocked-protocol.md。注意：**type 字段固定为 `BLOCKED`，category 独立传递**（如 type="BLOCKED", category="SPEC_CONFLICT"），不将 category 拼入 type
- `DECISION`：Coordinator 对 BLOCKED 的裁决
- `CONTEXT_UPDATE`：跨 Worker 上下文更新（v2）
- `REVIEW_RESULT`：Reviewer 判定

envelope 规范：

```json
{ "protocol": "parallel-worktree-agents/v1", "type": "...", ... }
```

- 语义放 content，不修改底层 MessageType 枚举（ClawTeam 枚举不可任意扩展）
- 传输失败：不静默；记录 RUNTIME 审计条目
