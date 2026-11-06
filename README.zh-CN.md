# parallel-worktree-agents

[English](README.md) | **简体中文**

一个可移植的 Agent Skill：将项目目标拆成独立任务，协调多个子 Agent，并验证整合结果。默认使用宿主的原生委派工具，为有写入能力的 Worker 分配独立工作区。

本仓库提供指令、协议、模板及可选的 OpenCode 预设，不包含调度程序、可执行的 Runtime adapter 或安全沙箱。

## 快速开始

使用支持 Agent Skills 的宿主。并行执行还需要实际可用的委派工具，否则按顺序完成任务。使用 Git worktree 需要安装 Git。当前 Skills CLI 要求 Node.js 22.20 或更高版本，详见[安装指南](docs/installation.md)。

为 Codex 全局安装：

```bash
npx --yes skills add https://github.com/johnnydongsh-sudo/parallel-worktree-agents --skill parallel-worktree-agents -a codex -g -y
```

其他宿主可将 `codex` 换成 `opencode` 或 `claude-code`。省略 `-g` 即为项目内安装。`--yes` 确认 npx 的包安装提示，`-y` 确认 Skills CLI 的提示。Windows PowerShell 若阻止执行 `npx.ps1`，可改用 `npx.cmd`。

然后告诉 Agent：

```text
使用 $parallel-worktree-agents 完善这个项目的文档。
将安装指南和使用示例拆成独立任务。
为所有有写入能力的 Worker 分配独立工作区和明确的文件所有权。
验证整合后的变更，并报告尚未解决的问题。
```

也可以直接把仓库链接发给另一个 Agent：

```text
安装并使用这个 Agent Skill：
https://github.com/johnnydongsh-sudo/parallel-worktree-agents

识别当前宿主，检查安装前提和仓库访问权限。
除非我要求全局安装，否则在目标项目中非交互安装。
保留已有定制，验证安装文件、安装器登记和宿主发现状态，
说明是否需要新会话。明确报告安装失败或缺少委派工具的情况。
然后将我的目标拆成独立任务，安全地委派给子 Agent，并验证整合结果。
遵守当前宿主的权限和我的授权范围。
```

Agent 需要终端、写入及仓库访问权限。私有仓库要求目标机器已有获授权身份，仅有链接不会获得访问权。详见[交给 Agent 安装](docs/installation.md#agent-driven-installation--交给-agent-安装)。

## 工作方式

```text
目标 → 依赖与所有权 → 任务包 → 独立 Worker
     → Coordinator 验证 → 经审查的整合结果
```

| Profile | 适用场景 | 内容 |
| --- | --- | --- |
| `QUICK` | 普通的独立研究、审查或范围明确的实现 | 任务包、所有权、结果收集及 Coordinator 验证 |
| `STRICT` | 多个并行实现 Worker、高风险或跨组件修改、导入既有实现，或明确要求完整审查 | 冻结契约、worktree manifest、Spec/Quality 审查、修复复审及人工合并 Gate |

执行模式与 Runtime 分别选择。执行模式有 `READ_ONLY_PARALLEL`、`NORMAL_IMPLEMENTATION`、`PARALLEL_IMPLEMENTATION` 和 `IMPORTED_EXISTING_IMPLEMENTATION`，详见[执行模式](references/execution-modes.md)。强依赖任务按顺序执行。

## 宿主与 Runtime

| Runtime | 前提 |
| --- | --- |
| `HOST_NATIVE`（默认） | Codex、OpenCode、Claude Code 或其他宿主实际提供委派工具及可用容量 |
| `OPENCODE_CLI`（可选） | 已安装兼容 CLI、Agent 可发现，且命令和 prompt 传递已验证 |
| `CLAWTEAM`（可选） | 单独安装 Runtime，并由当前环境提供兼容集成 |

Skill 不安装这些 Runtime，也不保证宿主权限配置。没有委派工具时应说明限制并顺序执行。能力检查及显式 fallback 记录见 [Runtime adapters](references/runtime-adapters.md)。

## 安全与隐私

- 共享工作区要求 Worker 的**所有工具**都由权限层强制只读，包括 shell 命令。Agent 名称或单独的 `edit: deny` 不足以证明只读。
- 有写入能力的 Worker 需要独立工作区。Git worktree 分离工作目录状态，文件系统和凭据访问限制由宿主沙箱负责。
- 每个 Worker 都有明确文件所有权，Coordinator 检查实际 diff 并独立执行相关验证。
- 安装或调用 Skill 不会自动授权 commit、push、merge、创建 PR、部署或读取秘密。
- 不把私有 prompt、凭据和运行日志提交到 Git。敏感信息处理与报告方式见 [SECURITY.md](SECURITY.md)。

## 文档导航

| 文档 | 用途 |
| --- | --- |
| [SKILL.md](SKILL.md) | Agent 入口与 QUICK 流程 |
| [安装指南](docs/installation.md) | 前提、安装、更新、卸载及可选预设 |
| [故障排查](docs/troubleshooting.md) | 发现、权限、worktree、验证及 Runtime 故障 |
| [STRICT 流程](references/strict-worktree-flow.md) | 完整工程与审查 Gate |
| [Worktree 安全](references/worktree-safety.md) | 工作区所有权及路径检查 |
| [恢复与清理](references/recovery-and-cleanup.md) | 保留失败现场及清理本轮资源 |
| [协议](protocols/) / [模板](templates/) | 消息、验证记录与任务契约 |
| [贡献指南](CONTRIBUTING.md) | 修改范围及包验证 |
| [安全说明](SECURITY.md) | 敏感信息与信任边界 |
| [变更记录](CHANGELOG.md) | 重要变更 |
| [历史 RC 使用记录](RC_USAGE_LOG.md) | 特定版本验收证据，不代表当前兼容性保证 |

两份 README 覆盖相同的使用主题，详细工作流参考文档目前以中文为主。

## 包验证

在仓库克隆目录执行：

```bash
npx --yes skills add . --list
git diff --check
```

应发现一个名为 `parallel-worktree-agents` 的 Skill。这只能证明包可被识别，不等于完成多 Agent Runtime 测试。其他检查见[贡献指南](CONTRIBUTING.md)。

## 致谢

可选的高级 Runtime 可集成 [HKUDS/ClawTeam](https://github.com/HKUDS/ClawTeam)。ClawTeam 是独立项目，并非本仓库作者开发。

## 许可证

本项目采用 [MIT 许可证](LICENSE)。Copyright (c) 2026 johnnydongsh-sudo。
