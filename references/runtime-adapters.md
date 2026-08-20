# Runtime Adapters

优先使用当前宿主已经提供的原生 delegation 接口。不要仅因为 Skill 历史上使用 OpenCode CLI 或 ClawTeam，就安装额外 Runtime。

## HOST_NATIVE（默认）

在派发前检查当前工具列表和并发限制；工具名称可能随版本变化，以当前环境为准。

| Host | 常见接口 | 建议映射 |
| --- | --- | --- |
| OpenCode | Task tool；常见内置 `general` / `explore`，部分新版另有 `scout` | Agent 名称只作候选；先检查 resolved config/tools。写实现通常用 general；只有 edit/write 被实际 deny 时才把 explore/scout 用作共享仓库只读 Worker |
| Codex | spawn / wait / message 类协作工具 | 一个独立域一个 Agent；同一回合派发可并发的任务 |
| Claude Code | Agent / Task 类工具 | 按任务类型选 subagent，并把任务包作为完整 prompt |
| Other hosts | 任意原生 subagent/delegation API | 保留相同的任务包、所有权、阻塞和验收语义 |

如果宿主不提供 delegation 工具，返回清晰限制并顺序执行。不要通过虚构的工具名或未安装命令假装派发成功。

项目或全局配置可能覆盖内置 Agent 的默认权限；例如宽泛的 allow 规则会让名为 `explore` 的 Agent 仍拥有编辑工具。共享仓库前必须检查解析后的 agent config/tools，而不是只读文档描述。

宿主已为子 Agent 提供独立 workspace 时使用该隔离。若所有 Agent 共享文件系统，任何拥有编辑能力的 Worker 都需要 Coordinator 创建 Git worktree；只有权限层强制禁写的 Worker 可共享仓库。

## OPENCODE_CLI（可选）

需要进程级隔离、可审计 JSON 输出或现有自动化时，可使用：

```text
opencode run --dir <exact-worktree> --agent <agent> --model <pinned-model> --format json <prompt>
```

先验证 CLI 存在、版本兼容、Agent 可发现、prompt 未截断。仓库中的 `agents/worktree-implementer.md` 与 `agents/worktree-reviewer.md` 是可选预设，不是默认安装依赖。

## CLAWTEAM（可选高级 Runtime）

仅在已安装且确实需要 DAG、Registry、Inbox 或持久 Runtime 状态时使用。兼容或健康检查失败时回退 `HOST_NATIVE` 或 `OPENCODE_CLI`，并显式记录：

```text
requestedRuntime
selectedRuntime
fallbackUsed
fallbackReason
```

## 统一语义

无论 Runtime 如何，必须能表达：create task、delegate、wait/collect、message/block、status。Runtime 只报告状态；Coordinator 负责所有权检查、验证和接受决策。

以下能力不受支持时标记 `unsupported`、`emulated` 或 `no-op` 并给出证据，禁止假装成功：独立 workspace、冲突检测、snapshot、持久 inbox、自动 cleanup。
