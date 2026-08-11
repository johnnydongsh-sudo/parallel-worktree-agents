# Execution Modes（正交参考）

四种 Execution Mode（与 Runtime 完全正交）：

- `READ_ONLY_PARALLEL`：Fact Audit / exploration / research / Spec investigation / Reviewer。无需 worktree 写隔离
- `NORMAL_IMPLEMENTATION`：单 Worker 常规实现
- `PARALLEL_IMPLEMENTATION`：多 Worker 并行（文件域互斥 + 独立 worktree）
- `IMPORTED_EXISTING_IMPLEMENTATION`：代码已被其他 Agent 写完甚至进 main，未走 Gate

正交矩阵（示例）：`NORMAL × NATIVE`、`PARALLEL × CLAWTEAM`、`IMPORTED × NATIVE`（Imported 通常跳过 Spawn）。

决策：单 Worker 简单任务 → NORMAL；需要 DAG/多 Worker/BLOCKED 通信 → 考虑 PARALLEL；发现已存在代码未走 Gate → IMPORTED（见 imported-implementation.md）。
