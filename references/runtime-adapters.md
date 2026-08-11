# RuntimeAdapter 参考

统一语义（Native 不支持的能力必须显式标 unsupported/emulated/no-op with evidence，禁止假装支持）：

```text
createTeam / createWorkspace / createTask / addDependency / spawnWorker
sendMessage / receiveMessages / detectConflicts / snapshot / cleanupWorkspace / status()
```

实现：
- `NativeOpenCodeRuntime`：`opencode run --dir <worktree>` + manifest（execution fallback，实战验证最多）
- `ClawTeamRuntime`：team/Registry/DAG/Inbox 协调组件 + OpenCodeCompatibilityAdapter（supported，非 experimental）

Runtime 选择（AUTO）：ClawTeam 不可用/版本不兼容/健康检查 FAIL → NATIVE（记录 RUNTIME_FALLBACK）；单 Worker → NATIVE 或 CLAWTEAM 均可；需 DAG/多 Worker/BLOCKED 通信 → 优先 CLAWTEAM；用户显式指定 → 遵守（安全/preflight 失败除外）。

