# STRICT Worktree Flow

仅在高风险、多个并行写 Worker、完整 Gate 或用户明确要求时加载本流程。普通任务使用 `SKILL.md` 的 QUICK 流程。

## 不变量

```text
1. Main working tree belongs to Coordinator.
2. Worker self-report is advisory only.
3. Worker commits never flow directly to main.
4. Unknown changes are preserved until ownership is resolved.
5. Runtime reports state; Policy decides acceptance.
6. Default terminal state is READY_FOR_HUMAN_MERGE.
```

## 流程

```text
0.  Preflight: inspect status, unknown changes, tools and authorization
1.  Select Execution Mode and Runtime
2.  Fact Audit: reconcile requested goal with repository reality
3.  Freeze Contract using ../templates/contract-template.md
4.  Build dependency graph and file ownership table
5.  Create isolated workspaces and record baseCommit
6.  Dispatch workers using ../templates/implementer-prompt.md
7.  Collect actual output, commits/diffs and verification evidence
8.  Coordinator independent targeted verification
9.  Ownership, runtime-state and conflict gates
10. Integrate accepted work into an integration branch when authorized
11. Spec Review using ../templates/spec-reviewer-prompt.md
12. Quality Review using ../templates/quality-reviewer-prompt.md
13. Fix loop: new change -> independent reverify -> scoped re-review
14. Full or risk-proportional independent reverification
15. Stop at READY_FOR_HUMAN_MERGE
16. After an authorized decision, clean only owned worktrees and branches
```

## Required references

- Contract: [../templates/contract-template.md](../templates/contract-template.md)
- Worker prompt: [../templates/implementer-prompt.md](../templates/implementer-prompt.md)
- Spec reviewer: [../templates/spec-reviewer-prompt.md](../templates/spec-reviewer-prompt.md)
- Quality reviewer: [../templates/quality-reviewer-prompt.md](../templates/quality-reviewer-prompt.md)
- Worktree rules: [worktree-safety.md](worktree-safety.md)
- Verification: [../protocols/verification-protocol.md](../protocols/verification-protocol.md)
- BLOCKED messages: [../protocols/blocked-protocol.md](../protocols/blocked-protocol.md)
- Runtime state: [../protocols/runtime-state-protocol.md](../protocols/runtime-state-protocol.md)
- Review gates: [review-gates.md](review-gates.md)
- Cleanup: [recovery-and-cleanup.md](recovery-and-cleanup.md)
- Existing ungated code: [imported-implementation.md](imported-implementation.md)

Any missing mandatory gate or acceptance based only on a Worker statement prevents `READY_FOR_HUMAN_MERGE`.
