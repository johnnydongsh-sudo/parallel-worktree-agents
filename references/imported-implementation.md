# IMPORTED_EXISTING_IMPLEMENTATION 流程

发现代码已被其他 Agent 写完甚至进 main 但未走 Gate：

```text
Import Detection（git log 溯源/提交身份）
→ 标记 IMPORTED_UNVERIFIED（代码可存在于 main，不等于 SPEC_PASS/QUALITY_APPROVED）
→ Fact Audit（逐逻辑身份重构 Contract，禁止合并成大包）
→ Contract Reconstruction（计划缺陷裁决写入权威）
→ Coordinator independent verification
→ Spec Review per logical identity
→ Quality Review
→ Remediation（专用审计 worktree，文件域互斥 Worker）
→ independent reverify → re-review
→ READY_FOR_HUMAN_MERGE
```

要点：不 reset main、不改写历史；审计期冻结相关业务文件；导入会话声明不采信；遗留调查项（如被 catch 吞掉的异常日志）显式归因。
