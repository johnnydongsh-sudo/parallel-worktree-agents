---
name: parallel-worktree-agents
description: "Coordinate 2+ independent research, review, or implementation tasks with multiple subagents. Use host-native delegation by default, isolate every write-capable worker, and verify results centrally. Do not use for a single small task or tightly coupled work."
metadata:
  version: "1.1.0"
  repository: "https://github.com/johnnydongsh-sudo/parallel-worktree-agents"
---

# Parallel Worktree Agents

把一个目标拆成多个互不阻塞的工作域，交给多个子 Agent 并行完成，由当前 Agent 统一协调、验收和整合。

## 先做三个判断

1. **宿主能力**：默认选择 `HOST_NATIVE`，确认当前环境有哪些原生子 Agent / delegation 工具、并发槽位、解析后的实际读写权限和隔离能力。Agent 名称或描述不能替代权限证据。按需读取 [runtime-adapters.md](references/runtime-adapters.md)。
2. **依赖关系**：只有能够独立完成、不会等待彼此输出的任务才并行。单任务或强依赖链保持单 Agent / 顺序执行。
3. **写入隔离**：只有实际权限强制禁写的 Worker 才可共享仓库并行。任何具有编辑工具的 Worker，即使任务要求只读，也必须使用宿主隔离工作区或独立 Git worktree。无法隔离时，所有可能写入的工作顺序执行。

若宿主没有子 Agent 工具，明确说明限制并顺序完成；不要伪装已经并行。

## 默认 QUICK 流程

### 1. 划分独立工作域

按组件、问题域或交付物划分，而不是机械地按文件数量划分。为每个任务写一个自包含任务包：

```text
TASK_ID
GOAL
DELIVERABLE
CONTEXT
ACCESS_MODE          # READ_ONLY_ENFORCED | WRITE_ISOLATED
OWNED_PATHS          # 写任务必填；文件或互斥目录
FORBIDDEN_PATHS      # 写任务必填
DEPENDENCIES         # 并行任务应为空或已满足
VERIFICATION
REPORT_FORMAT
```

共享文件、根 lockfile、公共 schema、迁移和版本号默认归 Coordinator 或单一指定 Worker；禁止多个 Worker 同时拥有。

### 2. 选择执行方式

- **并行只读**：搜索、审计、研究、测试诊断、方案比较。先确认所选子 Agent 的解析后权限确实禁用 edit/write/apply-patch，再共享仓库并发派发。
- **并行写入**：优先使用宿主提供的隔离 workspace；否则由 Coordinator 创建独立 Git worktree，并把绝对路径和分支写进任务包。
- **顺序写入**：任务耦合、所有权重叠、无法隔离，或用户未授权需要的 Git 操作时使用。仍可先并行调查。

调用 Skill 本身不构成 commit、push、merge、PR、部署或其他外部写入授权。遵守宿主与用户的授权边界。

### 3. 并发派发

- 一个独立工作域对应一个子 Agent；数量不超过独立任务数、可用槽位和用户限制三者的最小值。
- 按解析后的工具权限选择 Agent；不要根据 `explore`、`reviewer` 等名称推断它一定只读。
- 在宿主支持时，在同一个协调回合发出所有可并行任务。
- Prompt 只包含该任务所需上下文、路径、约束、验证和输出契约；不要倾倒整段会话历史或秘密。
- Coordinator 保留集成、共享文件和最终验收职责。Worker 不得自行扩大范围或再委派，除非任务包明确允许且宿主支持。

### 4. 收集与处理阻塞

每个 Worker 必须返回：

```text
STATUS: DONE | BLOCKED | FAILED
SUMMARY
CHANGED_PATHS        # 只读任务写 NONE
VERIFICATION_COMMANDS_AND_RESULTS
RISKS_OR_OPEN_QUESTIONS
```

`BLOCKED` 必须说明 observed、expected、需要的决策、受影响路径以及是否留下局部修改。Coordinator 解决阻塞后再继续；不要让 Worker 静默改规格或越界。

### 5. Coordinator 验收

不要把 Worker 自报当作完成证据。Coordinator 必须：

1. 检查每份实际输出、diff 或提交范围；
2. 核对所有权与未知改动；
3. 处理冲突和共享文件；
4. 亲自运行相关测试，再按风险运行整体验证；
5. 汇总每个任务状态、整合结果、验证证据和剩余风险。

若写任务使用 worktree，Worker 不得直接写 main。只有在相应 Git 操作已获授权时，Coordinator 才可把通过验收的结果整合到 integration 分支；默认不 push、不合并 main。

## STRICT 流程

以下情况使用 STRICT：用户明确要求完整 Gate、高风险/跨组件实现、多个并行写 Worker、已有其他 Agent 代码需补审计，或错误整合代价较高。

先读取 [strict-worktree-flow.md](references/strict-worktree-flow.md)，再按需要读取其中链接的模板和协议。STRICT 保留 Contract Freeze、文件所有权、独立验证、Spec Review、Quality Review、修复复审和 Human Merge Gate。

## 关键不变量

```text
1. Parallelism is an optimization, not a goal.
2. Parallel writers never share an unisolated working tree.
3. A shared working tree is allowed only for permission-enforced read-only workers.
4. Worker self-report is advisory; Coordinator verification decides acceptance.
5. Unknown changes are preserved until ownership is resolved.
6. No Worker silently expands scope or edits shared files.
7. Skill invocation never broadens the user's authorization.
```

## 按需参考

- 执行模式选择：[execution-modes.md](references/execution-modes.md)
- 宿主工具与 Runtime 映射：[runtime-adapters.md](references/runtime-adapters.md)
- Git worktree 安全：[worktree-safety.md](references/worktree-safety.md)
- 严格审查 Gate：[review-gates.md](references/review-gates.md)
- 导入既有实现：[imported-implementation.md](references/imported-implementation.md)
- BLOCKED、验证和 Runtime 状态：[`protocols/`](protocols/)
- 严格模式模板：[`templates/`](templates/)
