# Changelog

## v1.0.0-rc1 (2026-08-11)

- Policy / Runtime decoupling with two interchangeable runtimes (Native OpenCode, ClawTeam optional)
- Execution Mode × Runtime orthogonal model (READ_ONLY_PARALLEL / NORMAL / PARALLEL / IMPORTED_EXISTING_IMPLEMENTATION)
- 7 top-level Invariants
- 17-step gate flow (preflight → contract freeze → workers → independent verification → integration → Spec/Quality review → human merge gate → cleanup)
- Protocol layer: BLOCKED / verification / inbox / runtime-state
- Reference layer: execution modes, worktree safety, runtime adapters, review gates, imported implementation, recovery and cleanup
- Template layer: contract / ownership / implementer prompt / spec & quality reviewer prompts / manifest

Validated through real-world software engineering workflows, including parallel workers, imported implementations, stale-base revalidation, runtime fallback, state mismatch, and CLI compatibility failure paths.

### v1.0.0-rc1 (agents packaging)

- Added `agents/`: `worktree-implementer` (parallel worker with permission deny-list) and `worktree-reviewer` (read-only two-stage reviewer)
- Skill is self-contained: runtime flow spawns workers/reviewers via `--agent <name>` from this repository
