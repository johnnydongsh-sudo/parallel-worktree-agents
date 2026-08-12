# RC Usage Log(v1.0.0-rc.1 使用期记录)

> 约定:RC 使用期**停止设计,只记录编排缺陷**。业务 bug、模型偶发失误、项目自身问题**不**计为 Skill defect。值得推动 RC2/v1.0.0 的是 Runtime/Policy 层问题。

## 每次任务记录(轻量)

```text
RC task #
ExecutionMode:
Runtime requested/selected:
fallbackUsed:
orchestration defect:
severity:            # P1 / P2 / P3(仅编排层)
workaround:
Skill change needed: # yes/no + 一句描述
```

## Orchestration defect 分类(计入升级证据)

- Runtime 选择错误(requested/selected 偏差)
- Worktree 污染或未隔离
- BLOCKED 丢失 / 未正确路由
- fallback 不透明(未记录 requested/selected/used/reason)
- 验证状态不一致(VERIFICATION_MISMATCH / 挂起未检测)
- 清理残留(worktree/分支/registry/孤儿进程)
- Reviewer Gate 绕过(审查被跳过或弱化)

## 升级 v1.0.0 门槛

- 3–5 个真实任务正常使用
- ≥1 次 PARALLEL_IMPLEMENTATION
- ≥1 次 CLAWTEAM Runtime
- ≥1 次 NATIVE fallback 或异常路径
- 无 P1/P2 orchestration defect

## 记录

| RC task # | ExecutionMode | Runtime req/selected | fallback | defect | severity | workaround | Skill change needed |
|---|---|---|---|---|---|---|---|
| (待填写) | | | | | | | |
