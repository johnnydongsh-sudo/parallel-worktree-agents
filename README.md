# parallel-worktree-agents

A portable Agent Skill for splitting one project goal into safe, independent lanes and coordinating multiple subagents. It uses the host agent's native delegation tools by default and adds Git worktree isolation when several agents need to write code concurrently.

## Give another agent only the repository link

Send this message:

```text
Install and use this Agent Skill:
https://github.com/johnnydongsh-sudo/parallel-worktree-agents

Use it to split my goal into independent tasks, delegate them to multiple
subagents where safe, and verify the combined result.
```

A supported coding agent can install it non-interactively with:

```bash
npx --yes skills add https://github.com/johnnydongsh-sudo/parallel-worktree-agents --skill parallel-worktree-agents -g -y
```

Then invoke it with:

```text
Use $parallel-worktree-agents to implement this goal: <goal>
```

For a project-local install, omit `-g`. To target a particular host, add for example `-a opencode`, `-a codex`, or `-a claude-code`.

Private repositories require GitHub access on the recipient machine. For link-only installation without credentials, the repository must be public.

## Why installation is simple now

- The repository root is a valid Agent Skills package with one `SKILL.md`.
- `npx skills` can discover it directly from the full GitHub URL or `owner/repo` shorthand.
- The default path uses the host's existing subagent tools; separate OpenCode agent definitions are no longer required.
- Existing `agents/worktree-implementer.md` and `agents/worktree-reviewer.md` remain optional OpenCode presets for stricter permission profiles.

## What the skill does

```text
Goal
  -> dependency and ownership check
  -> independent task packets
  -> parallel host-native subagents
  -> isolated workspaces for every write-capable worker
  -> coordinator scope review and verification
  -> integrated result (no automatic push or main merge)
```

The default `QUICK` profile is suitable for ordinary research, review, diagnosis, and implementation work. The `STRICT` profile adds contract freeze, worktree manifests, independent verification, two-stage review, fix/re-review loops, and a human merge gate.

## Safety boundaries

- Tightly coupled work stays sequential.
- A shared working tree is allowed only for workers whose resolved permissions enforce read-only access.
- Any worker with edit tools uses an isolated workspace, even when its prompt says the task is read-only.
- Workers cannot silently expand file ownership.
- Worker test claims are evidence, not acceptance; the coordinator reruns verification.
- Installing or invoking the skill does not authorize commit, push, merge, PR creation, deployment, or secret access.

## Package checks

```bash
npx --yes skills add . --list
```

## Optional OpenCode presets

OpenCode provides built-in subagents, so the core Skill works without extra agent files. Do not assume a built-in Agent remains read-only after project/global permission merging; inspect its resolved tools or isolate it. If you want the repository's stricter presets, copy the two Markdown files in `agents/` to either `.opencode/agents/` for one project or `~/.config/opencode/agents/` globally, then confirm OpenCode can discover them.

## Acknowledgements

The optional advanced runtime can integrate with [HKUDS/ClawTeam](https://github.com/HKUDS/ClawTeam). ClawTeam is a separate project and is not authored by this repository.

## License

Not yet selected. This repository remains a private release candidate until a license and public-release decision are made.
