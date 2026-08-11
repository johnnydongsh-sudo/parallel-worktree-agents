# 审查 Gate 参考

- Spec Review：回答 "Did we build the right thing?" → SPEC_PASS/FAIL/PARTIAL（契约逐条、超范围、所有权、测试真实性）
- Quality Review：回答 "Is the correct thing implemented safely and well?" → QUALITY_APPROVED/ISSUES（正确性/异步副作用/资源/测试/安全/性能/可维护性）
- 审查范围：`baseCommit..workerCommits`（禁止 HEAD~1..HEAD）
- 修复闭环：Fix Worker 提交 → Coordinator 独立重验证 → Reviewer 复审（不能只信 Fix Worker）
- 审查有效性：**Review validity is conditional on semantic base stability, not merely commit age**（AUDITED_BUT_STALE_BASE → 追平+重验证 → STALE_BASE_REVALIDATED 或 REVIEW_REOPENED）
- 异步副作用专项 5 问：control flow / await-before-context / logging failure / fire-and-forget survival / FakeAsync deadlock
