# Contract Freeze 模板（Coordinator 冻结后 Worker 才可见代码）

## 任务六要素（Worker prompt 必含）

```text
WORKTREE_ROOT=<绝对路径>
BRANCH=<agent/<name>>
BASE_COMMIT=<冻结 commit>
ALLOWED_FILES=<文件级清单>
FORBIDDEN_FILES=<文件级清单>
DELIVERABLES=<测试命令、提交要求、输出要求>
```

## 契约内容

- Goal / Non-goals
- baseCommit（冻结后不得修改基线分支）
- 文件所有权（文件级，非目录级；共享热点单 owner）
- 依赖关系（任务图/blocked-by）
- 错误语义与状态转换
- 测试矩阵（至少：正常路径/边界/失败路径/确定性）
- BLOCKED 条件（哪些情况禁止自行扩展）

## 领域矩阵（按任务需要）

- Model round-trip matrix（加载/编辑/保存/清空/测试 逐字段）
- Generation decision matrix / Date semantics matrix / Integration outcome matrix
- 其他领域专属矩阵

## 冻结纪律

- Worker 见代码前必须冻结；计划文本完整 ≠ 契约完整（字段矩阵逐格核对）
- 计划与仓库现状冲突 → 先 Fact Audit 裁决，再冻结
- 冻结后变更需 Coordinator 决策记录（scope-expansion 同规则）
