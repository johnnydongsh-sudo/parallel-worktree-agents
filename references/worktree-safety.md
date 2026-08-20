# Worktree Safety and Workspace Ownership

- Coordinator 拥有当前主工作区；任何实际拥有编辑工具的并行 Worker 都不得共享未隔离工作区，即使其任务 Prompt 声称只读。
- 宿主若已给每个子 Agent 独立 workspace/branch，优先使用；否则每个写 Worker 使用一个 Git worktree。
- 创建 worktree 前记录 base commit、目标分支、绝对路径和文件所有权。不要在 main/master 上启动 Worker 实现。
- Skill 调用不自动授权 branch、commit、cherry-pick、merge 或 cleanup；遵守用户和宿主的 Git 授权规则。未获所需授权时，改用并行调查 + Coordinator 顺序写入。
- 未知主工作区修改：停止写入，识别所有权并证明已保存。禁止对未知改动执行 reset、checkout、clean、stash 或覆盖。
- 一个共享文件在同一批任务中只能有一个 Owner。锁文件、schema、迁移、版本号和集成入口默认由 Coordinator 或单一 Worker 负责。
- `READ_ONLY_ENFORCED` Worker 必须由权限层禁用写工具；`WRITE_ISOLATED` Worker 只读写任务包中的 workspace 和 OWNED_PATHS，不创建/删除 worktree，不 push，不合并 main。
- Coordinator 验收实际 diff、验证结果和越界情况后，才可在已获授权时整合。
- 只清理本轮明确创建且已验证无未保存改动的 worktree/分支；失败现场需要诊断时先保留。

路径证明建议：

```text
git rev-parse --show-toplevel
git branch --show-current
git rev-parse HEAD
git status --short
git rev-parse --git-dir
git rev-parse --git-common-dir
```

`show-toplevel`、branch、HEAD 和任务包一致，且 linked worktree 的 git-dir 与 git-common-dir 不同，才允许写入。
