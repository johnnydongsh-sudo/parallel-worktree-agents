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
|---|---|---|---|---|---|---|---|---|
| Fresh Machine Acceptance 01 | NORMAL_IMPLEMENTATION | AUTO / NATIVE | true (CLAWTEAM_UNAVAILABLE) | none | NONE | OpenCode CLI 装入临时目录 | no |

## Fresh Machine Acceptance 01(2026-08-12)

```text
RC task #:      Fresh Machine Acceptance 01
Platform:       Windows 10 / PowerShell 5.1(fresh machine,首次使用该 Skill)
ExecutionMode:  NORMAL_IMPLEMENTATION(单域小任务;未强推 PARALLEL)
Runtime:        AUTO → NATIVE
fallbackUsed:   true
fallbackReason: CLAWTEAM_UNAVAILABLE(fallback 可见,manifest 已记录)
Result:         FRESH_MACHINE_PASS_WITH_FRICTION
Orchestration defect: NONE(severity NONE)
Installation friction:
  - OpenCode CLI missing(仅 Desktop Electron binary,不足以作为 `opencode run` 入口)
  - git user.email 未配置(仓库级 identity 绕过;doctor 检测候选,不自动填写)
Skill change required: NO
RC2 installer/doctor input: YES(候选:CLI-vs-Desktop 识别、git identity 检测、
  worker worktree 运行期缓存检测)
Evidence:       本地保存(bundle:manifest/verification/reviewer 判定/完整事件流)
```

其他要点:Windows fresh clone→安装→discovery→AUTO fallback→worktree→worker→独立验证→integration→Spec/Quality→Human Gate 全链路成立;worker commit 未流入 main;验收后 sandbox worktree/分支已清理,main 保持基线。`node_modules` 运行期缓存残留记 informational(CLEANUP_RESIDUE),非 defect。
