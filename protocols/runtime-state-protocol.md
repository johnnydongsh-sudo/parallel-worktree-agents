# Runtime 状态协议

## RUNTIME_STATE_MISMATCH

Policy manifest ↔ Runtime registry 一致性检查（branch/worktree/baseCommit/owner）：

- 任一 mismatch → `RUNTIME_STATE_MISMATCH` → 记录并**停止 spawn/integration**，禁止"选一个看起来新的"

## RUNTIME_FALLBACK / COMPATIBILITY_FAILURE

- fallback 必须可见：记录 `requestedRuntime / selectedRuntime / fallbackUsed / fallbackReason`
- ClawTeam outage → HOST_NATIVE 或 OPENCODE_CLI fallback：记录 `RUNTIME_FALLBACK`，不静默降级
- HOST_NATIVE delegation tool 缺失或权限禁止：记录 observed tool inventory；改为顺序执行，不伪造 Worker 状态
- CLI/参数兼容失败：`COMPATIBILITY_FAILURE`（observed command + CLI version + 期望行为），禁止静默改参重试/裸启动

## Spawn 失败现场

- main 零变化；隔离 workspace/worktree 保留供诊断（preservation 由 Runtime adapter 接管）
- manifest/runtime state 记录失败；fallback 决策可审计
