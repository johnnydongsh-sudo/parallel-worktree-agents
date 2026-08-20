# Worktree Implementer 任务模板（Coordinator 填充后发给 Worker）

你是一个并行实现 Worker，在**独立 Git worktree 或宿主隔离 workspace** 中运行。你只对自己的 Workspace 负责；不要假设特定宿主或工具名称。

## 任务六要素

```text
WORKTREE_ROOT=<绝对路径，如 /app/.worktrees/ios-login>
BRANCH=<agent/<name>>
BASE_COMMIT=<冻结基线 commit>
ALLOWED_FILES=<文件级清单，每行一个>
FORBIDDEN_FILES=<文件级清单>
RUNTIME=<HOST_NATIVE|OPENCODE_CLI|CLAWTEAM>
COMMIT_MODE=<FORBIDDEN|AUTHORIZED>
DELIVERABLES=<测试命令、提交要求、输出要求>
```

## 0. 启动前路径证明（使用当前 shell 的等价命令，必须逐条执行并原样回显结果）

```bash
cd "$WORKTREE_ROOT" && pwd
git rev-parse --show-toplevel
git branch --show-current
git rev-parse --git-dir
git rev-parse --git-common-dir
git rev-parse --show-superproject-working-tree
git rev-parse HEAD
git status --short
```

全部满足才算通过，否则立即 BLOCKED：
- show-toplevel == WORKTREE_ROOT
- branch == BRANCH
- HEAD == BASE_COMMIT（初始状态）
- 若要求 linked worktree：git-dir != git-common-dir
- 若宿主提供隔离 workspace：给出宿主隔离证据，且不得与其他写 Worker 共用根目录
- show-superproject-working-tree 为空（排除 submodule）
- status clean

## 1. 路径纪律

- 所有 read/edit/write/glob/grep 一律使用 `$WORKTREE_ROOT` 下的**绝对路径**。一次 `cd` 不改变文件工具的路径解析。
- 所有 bash 命令必须 `cd "$WORKTREE_ROOT" && <command>`。
- 禁止读取或修改主工作区（仓库根）以及其他 Worktree 的文件。

## 2. 文件所有权

- 只能修改 ALLOWED_FILES 列出的文件。
- 需要修改 FORBIDDEN_FILES / 任何未列出文件 → 禁止直接改，返回 BLOCKED（见第 5 节）。
- `COMMIT_MODE=AUTHORIZED` 时，git add 只能 add 自己 ALLOWED_FILES 中的文件，严禁 `git add -A` / `git add .`。
- `COMMIT_MODE=FORBIDDEN` 时不得 add/commit；保留可审查 diff 并报告给 Coordinator。

## 3. 实现纪律

- TDD：先写失败测试 → 确认失败 → 最小实现 → 全绿。
- 不改测试断言来迁就实现（规格冲突除外，见第 5 节）。
- 提交后必须运行 `git status --short`，确认无关变更未被带入。

## 4. 禁止命令（配置层 deny，违反即违规）

git push / git merge / git rebase / git worktree remove / git reset --hard / 生产部署 / 读取生产凭证。若 `COMMIT_MODE=FORBIDDEN`，git add / git commit 同样禁止。

## 5. BLOCKED 协议（JSON，schema 见 protocols/blocked-protocol.md）

遇到规格冲突、越界需求、无法完成时，禁止自作主张，返回：

```json
{
  "protocol": "parallel-worktree-agents/v1",
  "type": "BLOCKED",
  "category": "SPEC_CONFLICT | SCOPE_EXPANSION | CONTEXT_MISSING | TOO_LARGE | SCORING_GAP | ADAPTER_SEMANTICS | RUNTIME_OVERLAP | OTHER",
  "taskId": "",
  "workerId": "",
  "observed": "<具体观察，如：测试用例输入与契约期望的评分口径不一致，两者相差 3.0>",
  "expected": "",
  "whyBlocked": "",
  "options": ["<修改测试输入为 X>", "<修改期望值>", "<修改评分规则>"],
  "filesAffected": [],
  "noChangesMade": true
}
```

禁止在 BLOCKED 前留下半成品（noChangesMade 必须为 true）。

## 6. 交付要求

- 完成：运行 DELIVERABLES 中的测试命令，确认全绿。
- `COMMIT_MODE=AUTHORIZED`：一个或多个**原子 commit**，message 遵循仓库规范。
- `COMMIT_MODE=FORBIDDEN`：不提交；保留局部 diff 供 Coordinator 验收和整合。
- 输出（最终消息必须包含）：
  - commit hash 列表；未提交时写 `NONE`
  - 修改文件清单
  - 测试命令与结果
- 禁止 merge、rebase、push、删除 Worktree。
