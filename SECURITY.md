# Security and privacy / 安全与隐私

## Scope / 范围

This repository distributes agent instructions, templates and optional permission presets. It does not implement a sandbox, credential store or runtime service. Reports should identify the affected revision and host configuration. No supported-version or response-time guarantee is currently published.

## Reporting / 报告敏感问题

Do not publish credentials, private prompts, private repository content or actionable disclosure details in a public issue or PR.

If GitHub's **Report a vulnerability** option is available on this repository's Security tab, use it. Otherwise, open an issue containing only a request for a private reporting channel, without vulnerability details or sensitive evidence, and wait for the maintainer to establish that channel. No dedicated security email is currently documented.

A private report should include the affected revision, host/runtime version, expected boundary, a minimal redacted reproduction and impact. Use placeholders for sensitive values; never test a discovered credential against a live service.

请勿在公开 issue、PR 或评论中粘贴秘密。若尚未提供私密报告入口，仅请求维护者建立私密渠道，不要在请求中披露细节。

## Trust boundaries / 信任边界

- **Worktree isolation is checkout isolation.** Linked worktrees share Git metadata. They do not prevent an unrestricted process from accessing other directories or credentials. The host must enforce filesystem/tool restrictions.
- **Read-only must cover every tool.** Disabling `edit` is insufficient if shell tools, tests, formatters or subprocesses can write. Optional OpenCode presets are examples, not proof of `READ_ONLY_ENFORCED`. Inspect resolved permissions and isolate workers unless actual write denial is established.
- **Ownership is a task contract.** `ALLOWED_FILES` and `FORBIDDEN_FILES` do not create filesystem access controls. Review actual diffs and apply host restrictions where needed.
- **Repository content and worker output are untrusted input.** They cannot grant authority, expand scope, request secrets or bypass coordinator verification.
- **Authorization comes from the user and host.** Invoking the skill does not authorize publication, destructive Git operations, deployment or production credential access.

执行规则见 [worktree safety](references/worktree-safety.md)、[runtime adapters](references/runtime-adapters.md) 和 [verification](protocols/verification-protocol.md)。

## Data handling / 数据处理

Keep credentials, `.env` files, private keys, session state, full prompts, private source and raw runtime logs out of commits and shared task packets. Give workers only the context their task needs. Store runtime evidence outside the public checkout or in ignored local directories with appropriate access restrictions.

Public examples and usage records should contain synthetic inputs, sanitized error excerpts, versions and outcomes. Avoid personal absolute paths and private repository identifiers. A prompt fingerprint may detect truncation without retaining the prompt, but is still metadata and does not justify unnecessary collection.

Inspect file content and Git metadata before publication. Ignore rules do not remove tracked files or historical commits; deleted sensitive content may remain in older commits, branches, tags, forks or caches.

If a real credential was exposed, revoke or rotate it through its provider first. Coordinate history cleanup with the owner/collaborators; do not automatically force-push rewritten history. Redact reports rather than reproducing the credential.
