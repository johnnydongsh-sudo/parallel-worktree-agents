# Troubleshooting / 故障排查

[English README](../README.md) · [中文 README](../README.zh-CN.md)

| Symptom / 现象 | Check and action / 检查与处理 |
| --- | --- |
| `npx` missing or unsupported Node version | Check `node --version` and `npm --version`; follow [installation prerequisites](installation.md) or install manually. |
| PowerShell blocks `npx.ps1` | Use `npx.cmd` with the same arguments, without changing system execution policy. |
| Installed skill not discovered | Check host/scope, installation output and duplicate global/project copies. Preserve `SKILL.md` and sibling folders; reload the host session. |
| Tasks run sequentially | Check actual delegation tools, capacity and dependencies. Sequential fallback is expected if tools or isolation are unavailable. |
| A reviewer can write | Inspect merged tool permissions, shell commands and test side effects. Use an isolated workspace unless the host enforces read-only access across all tools. |
| Worktree setup/path proof fails | Check `git status --short`, `git worktree list`, branch and base commit. Preserve unknown changes; choose a distinct branch/path rather than cleaning another workspace. |
| Worker blocked after edits | Stop further edits; report changed paths and `noChangesMade: false`. Preserve the diff; do not erase work to claim a clean status. See [BLOCKED](../protocols/blocked-protocol.md). |
| Coordinator cannot reproduce a worker's passing tests | Record command, revision, scope, exit code and differences; follow [verification](../protocols/verification-protocol.md). Self-report is not acceptance. |
| Manifest/runtime mismatch | Stop spawning/integration and reconcile branch, worktree, base and owner under [runtime state](../protocols/runtime-state-protocol.md). |
| CLI fails or a multiline prompt is truncated | Check CLI version, agent discovery and argument handling. Record sanitized command metadata; never publish private prompts or silently remove task constraints. |
| ClawTeam unavailable | It is optional and not bundled. Record requested/selected runtime and fallback reason. Historical RC evidence is version-specific. |
| Cleanup reports success but resources remain | Independently check worktrees, branches and owned processes. Preserve failure evidence and follow [recovery](../references/recovery-and-cleanup.md). |

## Filing a useful issue / 提交有效问题

Include host/version, operating system, skill revision, profile/mode/runtime, expected behavior, observed behavior and a minimal sanitized reproduction. Distinguish orchestration defects from application bugs and external runtime compatibility issues.

Replace usernames, personal absolute paths, private repository names and private inputs with placeholders. Share a short error excerpt, not full event streams or logs. Never include credentials, private source, session tokens or full prompts. Sensitive disclosures follow [SECURITY.md](../SECURITY.md).
