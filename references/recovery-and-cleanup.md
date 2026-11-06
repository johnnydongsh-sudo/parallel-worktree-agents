# 恢复与清理

- worktree 生命周期：Coordinator 统一创建/清理；Worker 禁止 worktree remove
- 清理前确认目标属于本轮、用户/宿主已授权、变更已保存且路径已解析。优先普通 `git worktree remove`；只有明确确认可丢弃残留时才使用 `--force`
- 删除本轮分支和执行 `git worktree prune` 前再次核对 worktree/branch 清单；不得清理未知 workspace
- 卫生检查：无孤儿测试进程、无 Git 锁、无引用已删 worktree 的进程
- 孤儿进程：TERM → verify → KILL only if necessary；记录卫生日志
- ClawTeam 现场：失败时 worktree+registry 保留供诊断（preservation 由 adapter 接管；ClawTeam 默认 spawn 失败会 cleanup，需 adapter 预记录 branch/base 后重建）
- Snapshot/恢复：ClawTeam snapshot 整域恢复（仅协调状态）。Checkpoint 仅在提交已获授权时进行，并只暂存 Worker 的 ALLOWED_FILES 明确列出的已核查文件；禁止 `git add -A` / `git add .`，不得混入 runtime 生成物、秘密或未知改动。未经授权时保留局部 diff，不暂存、不提交。
