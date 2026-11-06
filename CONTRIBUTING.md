# Contributing / 贡献指南

Contributions to instructions, protocols, examples and translations are welcome. 本仓库是 Agent Skill 文档包，不包含可运行的调度服务。

## Scope and source of truth / 修改范围与事实归属

- `SKILL.md`: concise agent entry point and QUICK policy.
- `references/`: modes, runtimes, worktree safety, STRICT gates and recovery.
- `protocols/`: message/verification semantics; keep consumers and examples consistent.
- `templates/`: reusable contracts/prompts with placeholders instead of private data.
- `agents/`: optional OpenCode presets and Codex display metadata.
- `README.md` / `README.zh-CN.md`: matching English/Chinese overview, quick start and navigation.
- `docs/`: installation and troubleshooting; link to canonical rules.
- `CHANGELOG.md`: significant behavior, compatibility and public documentation changes. `RC_USAGE_LOG.md` remains historical evidence.

Keep changes focused. Do not select a license, change versions, broaden permissions or claim new runtime support as an incidental documentation edit. Describe compatibility evidence and limitations explicitly.

## Workflow / 工作流程

1. Inspect status/base revision and preserve unrelated changes.
2. Use a separate branch. Concurrent workers follow [worktree safety](references/worktree-safety.md), with one owner per file.
3. Update the canonical document, its examples and affected README translations together.
4. Run the checks below; inspect the full diff. Stage explicit paths only and check staged content for private data.
5. Describe the user-visible result, actual checks and limitations in the PR. Never claim an unexecuted host test passed.

## Validation / 验证

From the repository root:

```bash
git diff --check
npx --yes skills add . --list
python -m json.tool templates/manifest-template.json
```

Discovery must list exactly one skill with the expected name. Validate relative Markdown links and YAML frontmatter when editing them. Check both READMEs for equivalent prerequisites, commands, limitations and navigation. No application test suite or CI workflow is bundled.

For workflow/permission changes, also run a bounded smoke test in a disposable repository using the actual host: verify delegation, workspace isolation, BLOCKED reporting, independent checks and owned-resource cleanup. Do not enable production access for a documentation test.

Review staged/untracked files and reachable history when preparing a public release. Use an available local secret scanner where possible, then manually classify results. A clean pattern scan cannot prove all sensitive content is absent.

## Privacy / 隐私

Use synthetic examples. Never commit private prompts, source excerpts, credentials or full runtime logs. Ignore rules do not protect already tracked content. Commit author identities are public metadata; inspect repository-scoped Git identity before publishing. Follow [SECURITY.md](SECURITY.md) for sensitive reports.
