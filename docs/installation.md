# Installation and maintenance / 安装与维护

[English README](../README.md) · [中文 README](../README.zh-CN.md)

## Requirements / 前提

- An Agent Skills-compatible host. Parallel execution requires actual delegation tools and sufficient capacity; installation alone does not enable them.
- Git for cloning and worktree operations.
- Node.js 22.20+ for the current external Skills CLI, according to its [upstream documentation](https://github.com/vercel-labs/skills/blob/main/README.md). Manual installation does not require Node.js.
- Network access to GitHub and, for `npx`, npm. Reading this public repository needs no GitHub credential. Your host/model may have separate account requirements.

This is an instruction package. There is no application build, service, dependency lockfile or bundled ClawTeam adapter to start.

## CLI installation / CLI 安装

Global Codex installation:

```bash
npx --yes skills add https://github.com/johnnydongsh-sudo/parallel-worktree-agents --skill parallel-worktree-agents -a codex -g -y
```

Use `-a opencode` or `-a claude-code` for those hosts. For a project-local installation, run from the target project and omit `-g`. Consult the CLI's supported-agent list for other hosts.

In PowerShell, `npx.cmd` avoids the `npx.ps1` execution-policy issue without changing system policy. Both `--yes` and `-y` are intentional: the first controls npx, the second the Skills CLI.

To list the package before installing:

```bash
npx --yes skills add https://github.com/johnnydongsh-sudo/parallel-worktree-agents --list
```

Expect exactly one skill. This does not install optional OpenCode agent definitions or prove runtime compatibility.

## Manual installation / 手动安装

Clone the complete repository into your host's documented skill directory. Preserve `SKILL.md` and sibling `references/`, `protocols/`, `templates/` and `agents/` directories; copying only `SKILL.md` breaks relative links.

For hosts configured to discover global skills in `~/.agents/skills`, a POSIX example is:

```bash
mkdir -p "$HOME/.agents/skills"
git clone https://github.com/johnnydongsh-sudo/parallel-worktree-agents.git "$HOME/.agents/skills/parallel-worktree-agents"
```

PowerShell equivalent for that configured location:

```powershell
$skillParent = Join-Path $HOME '.agents/skills'
$skillTarget = Join-Path $skillParent 'parallel-worktree-agents'
New-Item -ItemType Directory -Force -Path $skillParent | Out-Null
if (Test-Path -LiteralPath $skillTarget) { throw 'Skill target already exists; inspect it before installing.' }
git clone https://github.com/johnnydongsh-sudo/parallel-worktree-agents.git $skillTarget
```

These paths illustrate a configured discovery location, not a promise that every host scans it. Prefer the CLI for host-specific placement. Preserve existing installations and customizations.

## Verify discovery and use / 验证发现与使用

1. Reload the host's skill list or start a new session if needed.
2. Confirm discovery of `parallel-worktree-agents` and its references.
3. Try two small independent tasks in a disposable project.
4. Confirm actual delegation, separate workspaces for write-capable workers, ownership checks and coordinator verification. If delegation is absent, expect an explicit sequential fallback.

See [Troubleshooting](troubleshooting.md). Package discovery is not evidence of end-to-end orchestration success.

## Optional OpenCode presets / 可选预设

The default uses host-native agents. For the optional presets, copy `agents/worktree-implementer.md` and `agents/worktree-reviewer.md` into project `.opencode/agents/` or your configured OpenCode global agents directory (commonly `~/.config/opencode/agents/`). Inspect existing files first and preserve custom versions.

Confirm discovery with your installed OpenCode version and inspect **resolved** permissions after configuration merging. The presets contain Dart/Flutter command examples; adapt them to your project. The reviewer permits shell commands that can write files, so `edit: deny` alone cannot justify a shared writable checkout. See [Security](../SECURITY.md).

## Updates and removal / 更新与卸载

For CLI installs, preserve local customizations and rerun the selected installation command to refresh this skill at the same host/scope. Avoid accidental global/project duplicates. For a manually cloned install, inspect `git status --short` in its directory before using `git pull --ff-only`; resolve divergence deliberately.

To remove a global CLI-managed Codex install:

```bash
npx --yes skills remove parallel-worktree-agents -a codex -g -y
```

Use the same host and scope as installation. For a manual install, identify the exact skill directory and remove it through the host or file manager after preserving customizations. Optional OpenCode presets are separate copies; remove them separately if no longer needed. Do not delete project worktrees or runtime evidence when uninstalling the skill.
