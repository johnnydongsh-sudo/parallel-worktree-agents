# 独立验证协议（Mandatory Gate）

**Worker-generated verification is advisory evidence only. Completion claims are invalid until Coordinator independently executes the required verification commands.**

每次验证记录：

```json
{ "sha": "<commit>", "command": "...", "scope": "full|targeted",
  "exitCode": 0, "observedTests": 0, "durationSec": 0,
  "status": "PASS|FAIL|TIMEOUT|HANG_SUSPECTED", "artifacts": "<path>" }
```

规则：

1. 数字必须来自 Coordinator 自己执行的输出，不取自 Agent 文本
2. Worker 报告必须带 command+scope；与独立结果不一致 → `VERIFICATION_MISMATCH`
3. 挂起检测：配置超时内未完成 → `HANG_SUSPECTED` → 捕获最后输出 → 先调查，禁止杀掉静默重试
4. 修复后仍须：新 commit → Coordinator 独立重验证 → Reviewer 复审
