# DevelopmentStandards

面向 Unity 及相关软件开发工作的规范体系。仓库采用核心入口 + 专项规范的分层组织，适用于个人多个项目及按需扩展的项目专用规则。专项规范的 `scope` 字段用于区分跨项目/跨引擎可复用的 `General` 规则与仅适用于 Unity 工程的 `Unity` 规则；`General` 文档可以包含带有明确条件的 Unity 实现示例或技术说明，但这些内容只在 Unity 条件成立时适用，不得跨引擎照搬。两类规范仍须结合语言、框架和任务条件判断适用性。

## 仓库内容

- `AGENTS.md`：核心治理入口。Codex Home 中的用户级入口由此部署。
- `Standards/`：30 份共享专项规范，按 Governance、Architecture、Code、Design、Runtime、Unity 分类。
- `VERSION`：规范体系版本。
- `CHANGELOG.md`：正式规范变更记录。

## 权威来源与本地使用

GitHub 仓库的 `main` 是正式通用规范的权威维护来源。本地仓库用于修改、审查和版本管理；Codex Home 中的 `AGENTS.md` 与 `Standards/` 是实际使用的部署副本。部署副本不应作为长期独立维护来源。

默认本地部署目标为当前实际 Codex Home 下的：

```text
CodexHome/
├── AGENTS.md
└── Standards/
```

不要把整个 `.codex/` 目录上传到本仓库，也不要覆盖 `config.toml`、认证文件、会话数据、Skills、Hooks 或其他无关 Codex 文件。

## 首次部署（手动）

1. 在目标电脑安装并启动 Codex，确认实际 Codex Home 位置及 `CODEX_HOME` 配置。
2. 从 GitHub 克隆本仓库，选择已确认的正式版本或提交。
3. 检查 Codex Home 是否已有 `AGENTS.md`、`AGENTS.override.md` 或 `Standards/` 内容。
4. 比较现有文件与目标版本，识别用户独立修改、路径冲突及同目录 override。不要静默覆盖。
5. 在获得部署授权后，将仓库根目录 `AGENTS.md` 和 `Standards/` 部署到 Codex Home。
6. 静态核对文件数量、路径、相对链接、文档 ID 及实际部署版本。
7. 生成本地 `StandardsDeployment.json`，记录仓库地址、ref、实际 Commit SHA、版本、部署时间及受管理文件清单。该文件属于本地部署记录，不提交到本仓库。

## 更新与回退

更新前比较上次部署版本、当前本地文件及目标版本。保护本地独立修改；旧文件仅在确认属于旧部署管理范围、目标版本不再包含且获得清理授权后删除。部署失败不得标记为成功。回退是重新部署一个明确的历史版本，不自动回退项目代码或 GitHub 历史。

默认不自动联网检查、拉取或部署规范。Git 操作、部署、覆盖、删除及回退均遵循用户授权。

## 规范修改

规范修改按问题识别、调查、规则设计、修改方案、用户确认、文档实施与静态审查、变更交付进行。核心规则及多文档调整应使用独立主题分支并进行差异审查。`main` 只保留已经确认合并的正式规则。

版本采用 `Major.Minor.Patch`。Git Commit 追踪具体变更，Git Tag 标记正式版本快照，GitHub Release 按需建立。不要仅凭文件更新时间判断当前有效版本。

## 项目专用规则

项目专用约定及局部增量规则由对应项目仓库维护。简单项目约定可以放在项目级 `AGENTS.md`；有独立维护价值时建立 `Standards/Project/`。局部规则优先就近存放，不复制全部通用规范。
