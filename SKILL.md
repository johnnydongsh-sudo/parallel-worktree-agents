---
name: parallel-worktree-agents
description: 多 Worker 并行开发的编排 Skill v1。Policy 层(契约冻结/所有权/BLOCKED/独立验证/两阶段审查/人工合并门)与 Runtime 层(NATIVE/CLAWTEAM)正交。用于需要 2+ 独立任务域并行、或须经完整软件工程 Gate 的实现工作。含 IMPORTED_EXISTING_IMPLEMENTATION 审计模式。Runtime 可插拔,默认停在 READY_FOR_HUMAN_MERGE。
---

# parallel-worktree-agents v1

## 不可破坏的 Invariants

```text
INVARIANT 1  Main working tree belongs to Coordinator.
INVARIANT 2  Worker self-report is advisory only.
INVARIANT 3  Worker commits never flow directly to main.
INVARIANT 4  Unknown changes are never destroyed before ownership and preservation are proven.
INVARIANT 5  Runtime reports state; Policy decides acceptance.
INVARIANT 6  Parallelism is an optimization, not a goal.
INVARIANT 7  Default terminal state is READY_FOR_HUMAN_MERGE.
```

## 触发

存在 2+ 个文件级互斥的独立任务域需并行实现；或任务须走完整 Gate；或发现"代码已被其他 Agent 写入但未走 Gate"。

## 决策模型

### 1. Execution Mode（4 种，与 Runtime 正交，详见 references/execution-modes.md）

```text
READ_ONLY_PARALLEL          → Fact Audit/exploration/review,无需 worktree
NORMAL_IMPLEMENTATION       → 单 Worker
PARALLEL_IMPLEMENTATION     → 多 Worker(文件域互斥)
IMPORTED_EXISTING_IMPLEMENTATION → 既有代码审计(references/imported-implementation.md)
```

### 2. Runtime（AUTO 决策，详见 references/runtime-adapters.md）

```text
ClawTeam unavailable / version incompatible / health FAIL → NATIVE(+RUNTIME_FALLBACK 记录)
CompatibilityAdapter FAIL → NATIVE(+COMPATIBILITY_FAILURE 证据)
简单单 Worker → NATIVE 或 CLAWTEAM 均可
需要 DAG/多 Worker/BLOCKED 通信 → 优先 CLAWTEAM
用户显式指定 → 遵守(安全/preflight 失败除外)
```

fallback 必须可见：记录 `requestedRuntime / selectedRuntime / fallbackUsed / fallbackReason`。

## 主流程（NORMAL / PARALLEL）

```text
0.  Preflight:main 干净、无未知改动(INVARIANT 1/4)
1.  Select ExecutionMode
2.  Fact Audit(计划意图 vs 仓库现状;计划过期先纠正契约)
3.  Freeze Contract(templates/contract-template.md;含所有权、测试矩阵、领域矩阵)
4.  Build Task Graph + Ownership(文件级;共享热点单 owner)
5.  Select Runtime(AUTO)
6.  Create Workspaces(Coordinator 统一;记录 baseCommit)
7.  Spawn / Execute(templates/implementer-prompt.md;路径证明协议)
8.  Collect Worker Evidence(commit hash、文件清单、command+scope)
9.  Coordinator Independent Verification(protocols/verification-protocol.md;targeted+full+挂起检测;Worker 自报 vs 独立结果不一致 → VERIFICATION_MISMATCH 记录)
10. Runtime Conflict / Ownership Gate(越界、RUNTIME_STATE_MISMATCH——与验证产出的 VERIFICATION_MISMATCH 是两类信号,均须记录)
11. Integration Worktree(cherry-pick;禁止 Worker→main 直连)
12. Spec Review(templates/spec-reviewer-prompt.md)
13. Quality Review(templates/quality-reviewer-prompt.md;异步副作用专项)
14. Fix Loop(新 commit → 独立重验证 → 复审)
15. Independent Reverification
16. Human Merge Gate(READY_FOR_HUMAN_MERGE;不自动 merge)
17. Cleanup(worktree/分支/registry;卫生检查)
```

## IMPORTED 模式流程

```text
Import Detection → Fact Audit → Contract Reconstruction → Independent Verification
→ Spec → Quality → Remediation(审计 worktree)→ Reverify → re-review → Human Gate
```

## 必须遵守的 Gate（任何一环缺失或仅依赖 Agent 自报，不得进入下一环）

- Contract Freeze：Worker 见代码前
- Independent Verification：Coordinator 亲跑；`PASS/FAIL/TIMEOUT/HANG_SUSPECTED`；`VERIFICATION_MISMATCH` 记录
- 所有权核对 + scope-expansion 决策记录
- Integration Worktree：Worker → integration → Gate → Human → main
- 两阶段审查：Spec 先于 Quality；修复后重验证+复审
- Human Merge Gate：默认终点

## BLOCKED

统一 BLOCKED 协议（type 固定 `BLOCKED`、category 独立传递，如 type="BLOCKED", category="SPEC_CONFLICT"；protocols/blocked-protocol.md）：observed/expected/options/filesAffected/noChangesMade=true。传输经 Inbox envelope（protocols/inbox-protocol.md）。

## 停止条件

- 多任务改同一核心文件 / 契约未冻结 / 迁移有先后依赖 / 任务间代码依赖 / 所有权不明 → 禁止并行
- 出现 RUNTIME_STATE_MISMATCH / COMPATIBILITY_FAILURE / 挂起 → 停止并调查，禁止静默重试
- 默认停止于 READY_FOR_HUMAN_MERGE，不自动 merge main

## 模板 / 协议 / 参考

- templates/：contract / ownership / implementer-prompt / spec-reviewer-prompt / quality-reviewer-prompt / manifest
- protocols/：blocked / verification / inbox / runtime-state
- references/：execution-modes / worktree-safety / runtime-adapters / review-gates / imported-implementation / recovery-and-cleanup
- Runtime 证据链:Phase 0-3A 完整验证记录由各仓库自行维护,本 Skill 自包含运行
