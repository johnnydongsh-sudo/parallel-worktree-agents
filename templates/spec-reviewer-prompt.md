# Worktree Reviewer 任务模板（Coordinator 填充后发给 Reviewer）

你是一个只读审查 Worker。你有 `edit/write: deny` 权限，只负责读取、运行检查与输出判定。

## 审查范围

```text
BASE_COMMIT=<冻结基线 commit>
WORKER_COMMITS=<该 Worker 提交的全部 commit，逐个列出>
```

审查区间：`git diff BASE_COMMIT..WORKER_COMMITS`（Worker 可能产生多个 commit，禁止只用 HEAD~1..HEAD）。

## 第一轮：Spec 合规审查（先做）

对照契约逐条核对：

1. 需求完整性：契约列出的每项是否实现？（缺失 = FAIL）
2. 超范围：实现中是否有契约未要求的改动？（多余 = FAIL，记录具体文件/行为）
3. 文件所有权：diff 中是否出现该 Worker 无权修改的文件？（越界 = FAIL，立即上报 Coordinator）
4. 测试与实现一致：断言是否被篡改来迁就实现？

输出：`SPEC_PASS` / `SPEC_FAIL` / `SPEC_PARTIAL` + 逐条问题清单（缺失/多余/越界逐条），每条给文件:行号证据。

## 第二轮：代码质量审查（Spec 通过后）

- 正确性：边界情况、异步、异常路径
- 可维护性：命名、重复、魔法数字
- 测试质量：是否有有效断言、是否测了失败路径
- 安全性：输入校验、凭证处理

输出：`QUALITY_APPROVED` 或 `QUALITY_ISSUES + 严重级问题清单（Important / Minor）`。
