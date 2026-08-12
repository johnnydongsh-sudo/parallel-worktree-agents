# parallel-worktree-agents

> A policy-driven multi-agent software engineering skill for OpenCode with Git Worktree isolation and pluggable runtimes.

## What it is

```text
Policy Layer
├── Fact Audit
├── Contract Freeze
├── Ownership
├── Verification
├── Spec Review
├── Quality Review
└── Human Merge Gate

Runtime Layer
├── Native OpenCode
└── ClawTeam (optional)
```

**Runtime reports state; Policy decides acceptance.** The Policy layer is runtime-independent: ClawTeam and Native OpenCode are interchangeable execution backends, while contract freeze, independent verification, two-stage review and the human merge gate always stay in force.

## Execution Modes

```text
READ_ONLY_PARALLEL               # fact audit / exploration / review, no worktree needed
NORMAL_IMPLEMENTATION            # single worker
PARALLEL_IMPLEMENTATION          # multiple workers (file-level disjoint ownership)
IMPORTED_EXISTING_IMPLEMENTATION # code written by another agent without gates
```

Modes and runtimes are orthogonal (e.g. `IMPORTED × NATIVE`, `PARALLEL × CLAWTEAM`).

## Runtime

```text
AUTO     # default: ClawTeam unavailable/incompatible → NATIVE (RUNTIME_FALLBACK recorded);
         # needs DAG / multi-worker / BLOCKED communication → prefer CLAWTEAM
NATIVE   # opencode run --dir <worktree>, manifest-based
CLAWTEAM # HKUDS/ClawTeam coordination components (Registry/DAG/Inbox) — optional dependency
```

Fallback is always visible: `requestedRuntime / selectedRuntime / fallbackUsed / fallbackReason` are recorded.

## 7 Invariants

```text
INVARIANT 1  Main working tree belongs to Coordinator.
INVARIANT 2  Worker self-report is advisory only.
INVARIANT 3  Worker commits never flow directly to main.
INVARIANT 4  Unknown changes are never destroyed before ownership and preservation are proven.
INVARIANT 5  Runtime reports state; Policy decides acceptance.
INVARIANT 6  Parallelism is an optimization, not a goal.
INVARIANT 7  Default terminal state is READY_FOR_HUMAN_MERGE.
```

## Install

```bash
git clone <repository-url>
```

Then link or copy the `parallel-worktree-agents` directory into your OpenCode skill directory (project `.opencode/skills/` or global `~/.config/opencode/skills/`).

Install the required OpenCode agent definitions (`agents/`) into your OpenCode agents directory (project `.opencode/agents/` or global `~/.config/opencode/agents/`). The skill's runtime flow spawns workers via `opencode run --agent worktree-implementer` and reviewers via `--agent worktree-reviewer`; without these agent definitions the skill is not self-contained.

Finally, run compatibility checks:

```text
1. Install/symlink the skill.
2. Install/symlink the required OpenCode agent definitions.
3. Run compatibility checks (skill discovery, agent discovery, `opencode run --dir <worktree>` smoke).
```

> Verify your OpenCode version's skill and agent directories before installation — paths vary across versions.

## Usage

```text
Implement this feature using parallel-worktree-agents.
```

or:

```text
Implement this feature.
Runtime: CLAWTEAM.
```

Default behavior: contract freeze → worktrees → workers → independent verification → integration worktree → Spec/Quality review → **stops at READY_FOR_HUMAN_MERGE**. Never auto-merges main.

## Acknowledgements

- This project optionally integrates with [HKUDS/ClawTeam](https://github.com/HKUDS/ClawTeam).
- ClawTeam provides Team / Registry / DAG / Inbox / Conflict runtime capabilities.
- `parallel-worktree-agents` independently provides the software engineering Policy layer.
- These are separate projects: ClawTeam is not authored by this project, and this project is not an official ClawTeam extension, unless future official recognition is granted.

## License

Not yet selected. This is a private release candidate; a license will be chosen before any public release. Third-party notices, if any, are preserved in the repository.
