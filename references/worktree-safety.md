# Worktree 安全与 Main Workspace Ownership

- main working tree 属于 Coordinator；实现 Worker 禁止在 main 开发
- 未知 main 修改：STOP → ownership 识别（reflog/mtime/进程句柄）→ 禁 stash/reset/checkout/clean 于未解析改动 → 迁移任务分支 → 恢复 main → 重跑追平检查
- 迁移前置三查：进程句柄=0、mtime 两次采样稳定、无 git 锁
- 红线：Never reset/checkout/clean/stash/overwrite UNKNOWN changes until ownership is resolved and preservation is proven. 所有权与保存已证明后，Coordinator 可用必要 Git 操作恢复 main
- worktree 环境预热：sqlite3 dylib（复制主工作区已验证缓存，校验非零/架构/可加载）；spawn 必须钉选可用模型（`--model`，默认模型可能区域 403）
