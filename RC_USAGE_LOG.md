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
| RC Usage 02 Preflight (Windows ClawTeam Compatibility) | N/A (preflight) | CLAWTEAM / CLAWTEAM(subprocess) | false | none(upstream compat facts ×4,非 Skill defect) | N/A | adapter requirements(见下) | no |

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

## RC Usage 02 Preflight — Windows ClawTeam Compatibility(2026-08-12)

```text
RC task #:      RC Usage 02 Preflight — Windows ClawTeam Compatibility
Status:         PASS_WITH_ADAPTER_REQUIREMENTS
Coverage:       CLAWTEAM × Windows × subprocess × OpenCodeCompatibilityAdapter
ClawTeam:       HKUDS/ClawTeam main @ 0119833(pyproject 0.3.0;无 v0.3.0 tag)
                3.12 venv, default_backend=subprocess, skip_permissions=false
OpenCode CLI:   1.18.16; model pinned opencode-go/deepseek-v4-flash
执行:           team+1 临时 worktree+1 OpenCode worker(spawn subprocess,registry 恢复 prompt)
结果:           worker exit 0,输出 PREFLIGHT_OK;argv 符合契约
                (run --dir --agent --model --auto --format json + positional prompt)
Does this satisfy PARALLEL × CLAWTEAM RC coverage? NO
RC Usage 02 full parallel coverage: NOT YET COMPLETE
```

### 4 项可复现 Runtime compatibility facts(upstream,非本 Skill 编排缺陷)

```text
1. skip_permissions=true → Windows os.getuid crash(adapters.py prepare_command)
   category: COMPATIBILITY_REQUIREMENT
   severity: P2 if unguarded
   mitigation: force false + spawn --no-skip-permissions

2. multiline prompt truncation through win32 cmd wrapper(首个内嵌换行截断)
   category: COMPATIBILITY_REQUIREMENT
   severity: P1 if unmitigated(Worker 只收到 "## Identity",任务正文丢失)
   mitigation: adapter 从 spawn_registry.json 恢复/保全完整 prompt
   evidence: 受控复现;w2 恢复 2172 字符后成功

3. clawteam team cleanup --force 报告成功但残留 worktree/分支/registry
   category: CLEANUP_REQUIREMENT
   severity: P2 if cleanup reports success while residue remains
   mitigation: 显式 worktree remove --force / branch delete / prune / registry 核对

4. exit-hook checkpoint 将 .opencode runtime state 提交进 worker 分支
   category: OWNERSHIP / RUNTIME_RESIDUE_REQUIREMENT
   severity: P2 if runtime artifacts enter accepted worker commit
   mitigation: acceptance 前排除 runtime 生成物(见下 RUNTIME_ARTIFACT_SCOPE_VIOLATION)
```

### RC Usage 02 硬性 checklist(锁定)

```text
CLAWTEAM:
  commit = 0119833
  backend = subprocess
  skip_permissions = false(两层)

OpenCodeCompatibilityAdapter:
  no -p / no --yolo
  opencode run --dir <exact worktree> --agent worktree-implementer
  --model <explicit> --auto --format json <positional full prompt>
  prompt fingerprint: expectedPromptSha256 / deliveredPromptSha256 / match=true
  (只存 SHA-256,不存 prompt 原文)

Worker acceptance:
  git diff --name-only <base>..<workerCommit> 中出现
  .opencode/ .clawteam/ runtime logs goal/session/cache
  → RUNTIME_ARTIFACT_SCOPE_VIOLATION,不得直接接受
  (runtime 生成物永远不属于 Worker ownership)

Cleanup:
  upstream cleanup self-report is advisory only;
  独立复核 git worktree list / branch existence / workspace registry /
  clawteam team discover / process list / git locks
```
