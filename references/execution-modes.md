# Execution Modes

Execution Mode、Profile 与 Runtime 相互独立。

## Mode

- `READ_ONLY_PARALLEL`：多个 Agent 并行搜索、研究、审计或诊断；只有解析后权限强制禁写时才可共享同一仓库。
- `NORMAL_IMPLEMENTATION`：一个写 Worker；适合单域或存在顺序依赖的任务。
- `PARALLEL_IMPLEMENTATION`：多个写 Worker；必须使用宿主隔离 workspace 或独立 Git worktree，且写入所有权互斥。
- `IMPORTED_EXISTING_IMPLEMENTATION`：代码已由其他 Agent 写入但未走验收；按 [imported-implementation.md](imported-implementation.md) 补审计。

## Profile

- `QUICK`：默认。任务包、并发派发、范围核对、Coordinator 验证、结果汇总。
- `STRICT`：高风险、跨组件、多个写 Worker、完整软件工程 Gate 或用户明确要求时使用。读取 [strict-worktree-flow.md](strict-worktree-flow.md)。

## 决策规则

1. 任务之间互相等待输出 → 顺序执行。
2. 只有读取或产生报告，且 Worker 的实际权限强制禁写 → `READ_ONLY_PARALLEL × QUICK`。
3. 多个独立写入域且有隔离能力 → `PARALLEL_IMPLEMENTATION × QUICK|STRICT`。
4. 无隔离能力且不能强制只读 → 顺序执行；不要依赖 Prompt 承诺避免写入。
5. 代码已经存在但验证链缺失 → `IMPORTED_EXISTING_IMPLEMENTATION × STRICT`。
