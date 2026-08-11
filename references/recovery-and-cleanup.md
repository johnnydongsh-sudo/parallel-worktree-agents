# 恢复与清理

- worktree 生命周期：Coordinator 统一创建/清理；Worker 禁止 worktree remove
- 清理：确认无未提交文件 → `git worktree remove --force` → 删分支 → `git worktree prune` → 卫生检查（无孤儿 flutter_tester/无 git 锁/无引用已删 worktree 的进程）
- 孤儿进程：TERM → verify → KILL only if necessary；记录卫生日志
- ClawTeam 现场：失败时 worktree+registry 保留供诊断（preservation 由 adapter 接管；ClawTeam 默认 spawn 失败会 cleanup，需 adapter 预记录 branch/base 后重建）
- Snapshot/恢复：ClawTeam snapshot 整域恢复（仅协调状态）；Checkpoint 的 `git add -A` 仅限 worker 自身 worktree，禁止主工作区
