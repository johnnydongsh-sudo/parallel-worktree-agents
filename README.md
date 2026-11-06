# parallel-worktree-agents

**English** | [简体中文](README.zh-CN.md)

A portable Agent Skill for dividing a project goal into independent tasks, coordinating subagents, and verifying their combined work. It uses the host's native delegation tools and separate workspaces for workers that can write.

This repository contains instructions, protocols, templates and optional OpenCode presets. It does not ship a scheduler, executable runtime adapter or security sandbox.

## Quick start

Use an Agent Skills-compatible host. Parallel execution also requires actual delegation tools; otherwise the skill falls back to sequential work. Git is required for Git worktrees. The current Skills CLI requires Node.js 22.20 or later; see the [installation guide](docs/installation.md).

Install globally for Codex:

```bash
npx --yes skills add https://github.com/johnnydongsh-sudo/parallel-worktree-agents --skill parallel-worktree-agents -a codex -g -y
```

Replace `codex` with `opencode` or `claude-code` for another host. Omit `-g` for a project-local install. `--yes` accepts the npx package prompt; `-y` accepts the Skills CLI prompts. On Windows PowerShell, use `npx.cmd` if execution policy blocks `npx.ps1`.

Then ask your agent:

```text
Use $parallel-worktree-agents to improve this project's documentation.
Split installation guidance and usage examples into independent tasks.
Give every write-capable worker a separate workspace and explicit file ownership.
Verify the combined changes and report anything that remains unresolved.
```

Or send another agent just the repository link:

```text
Install and use this Agent Skill:
https://github.com/johnnydongsh-sudo/parallel-worktree-agents

Identify this host and check installation prerequisites and repository access.
Install non-interactively into this project unless I request global installation.
Preserve existing customizations. Verify installed files, installer registration
and host discovery; report whether a new session is required.
Report failures and missing delegation tools explicitly.
Then split my goal into independent tasks, delegate safely, and verify the
combined result. Follow my host's permissions and authorization.
```

The agent needs terminal/write access and access to the repository. Private repositories require an authorized identity on that machine; a link alone does not grant access. See [Agent-driven installation](docs/installation.md#agent-driven-installation--交给-agent-安装).

## How it works

```text
Goal → dependencies and ownership → task packets → isolated workers
     → coordinator verification → reviewed combined result
```

| Profile | Use it for | What it adds |
| --- | --- | --- |
| `QUICK` | Ordinary independent research, review or a bounded implementation | Task packets, ownership, result collection and coordinator verification |
| `STRICT` | Multiple parallel implementation workers, high-risk or cross-component changes, imported implementations, or requested review gates | Frozen contract, worktree manifest, Spec/Quality review, repair and re-review, human merge gate |

Execution mode and runtime are separate choices. The modes are `READ_ONLY_PARALLEL`, `NORMAL_IMPLEMENTATION`, `PARALLEL_IMPLEMENTATION` and `IMPORTED_EXISTING_IMPLEMENTATION`; see [execution modes](references/execution-modes.md). Tightly coupled tasks stay sequential.

## Host and runtime support

| Runtime | Requirements |
| --- | --- |
| `HOST_NATIVE` (default) | Actual delegation tools and capacity exposed by Codex, OpenCode, Claude Code or another host |
| `OPENCODE_CLI` (optional) | An installed compatible CLI, discoverable agents and verified command/prompt handling |
| `CLAWTEAM` (optional) | A separately installed runtime and a compatible integration supplied by your environment |

The skill does not install these runtimes or guarantee host permissions. If delegation is unavailable, report the limitation and work sequentially. See [runtime adapters](references/runtime-adapters.md) for capability checks and explicit fallback reporting.

## Safety and privacy

- A shared checkout requires permission-enforced read-only workers across **all tools**, including shell commands. An agent name or `edit: deny` alone is insufficient.
- Write-capable workers need separate workspaces. Git worktrees separate checkout state; host sandboxing is responsible for filesystem and credential access restrictions.
- Workers have explicit file ownership. The coordinator checks actual diffs and independently runs relevant verification.
- Installing or invoking the skill does not authorize commit, push, merge, PR creation, deployment or secret access.
- Keep private prompts, credentials and runtime logs out of Git. Follow [SECURITY.md](SECURITY.md) for reporting and data handling.

## Documentation

| Document | Purpose |
| --- | --- |
| [SKILL.md](SKILL.md) | Agent entry point and QUICK workflow |
| [Installation](docs/installation.md) | Prerequisites, installation, updates, removal and optional presets |
| [Troubleshooting](docs/troubleshooting.md) | Discovery, permissions, worktree, verification and runtime failures |
| [STRICT workflow](references/strict-worktree-flow.md) | Complete engineering and review gates |
| [Worktree safety](references/worktree-safety.md) | Workspace ownership and path checks |
| [Recovery and cleanup](references/recovery-and-cleanup.md) | Preserve failed runs and clean owned resources |
| [Protocols](protocols/) / [Templates](templates/) | Messages, verification records and task contracts |
| [Contributing](CONTRIBUTING.md) | Change scope and package validation |
| [Security](SECURITY.md) | Sensitive information and trust boundaries |
| [Changelog](CHANGELOG.md) | Significant changes |
| [Historical RC usage log](RC_USAGE_LOG.md) | Version-specific evidence, not current compatibility guarantees |

Both READMEs cover the same user-facing topics. Detailed workflow references are currently primarily Chinese.

## Package validation

From a clone of this repository:

```bash
npx --yes skills add . --list
git diff --check
```

Discovery should list one skill named `parallel-worktree-agents`. This confirms packaging, not a complete multi-agent runtime test. See [Contributing](CONTRIBUTING.md) for other checks.

## Acknowledgements

The optional advanced runtime can integrate with [HKUDS/ClawTeam](https://github.com/HKUDS/ClawTeam), a separate project not authored by this repository.

## License

Licensed under the [MIT License](LICENSE). Copyright (c) 2026 johnnydongsh-sudo.
