# Changelog

## Unreleased

- Adopted the MIT License and linked its full text from both READMEs.
- Added matching English and Simplified Chinese READMEs, installation and maintenance guidance, troubleshooting, contributing and security documentation.
- Removed the outdated private-release description and clarified that optional runtimes and a filesystem sandbox are not bundled.
- Clarified read-only permission evidence, explicit checkpoint staging and truthful reporting of partial changes when a worker becomes blocked.

## v1.1.0 (2026-08-20)

- Added a simple QUICK profile: split independent domains, dispatch with host-native subagent tools, and verify centrally.
- Made custom OpenCode agent definitions optional instead of a hard installation dependency.
- Added direct GitHub URL installation via the standard `npx --yes skills ... -y` path, disabling both npx and Skill CLI prompts.
- Added explicit runtime mappings for OpenCode, Codex, Claude Code, and generic delegation hosts.
- Required isolated workspaces for every Worker with edit tools; only permission-enforced read-only Workers can share a repository.
- Required resolved permission evidence before treating a Worker as read-only; Agent names such as `explore` are not trusted as permission proof.
- Moved the 17-step worktree and review pipeline into an on-demand STRICT reference.
- Added Agent Skills compatibility metadata and `agents/openai.yaml` UI metadata.

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
