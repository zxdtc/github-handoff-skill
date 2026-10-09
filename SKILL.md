---
name: github-handoff
description: 将当前会话/工作区的工作状态整理成交接文档（HANDOFF.md）并推送到 GitHub 仓库，或作为 issue 创建。触发词：github handoff、hand off、工作交接、交接一下、生成 handoff 文档、把进度推到 GitHub、HANDOFF.md、session handoff。适用于用户暂停任务、换会话/换人继续、或需要把当前进度结构化留档到 GitHub 时。
---

# GitHub Handoff

将当前工作状态生成交接文档并写入 GitHub 仓库，让下一个会话/协作者可以快速接手。

## 工作流

### 1. 收集交接信息

从当前会话与工作区收集（缺失项直接跳过，不要编造）：

- **已完成**：本次任务实际做了什么（对照对话中的工具调用与产物文件）
- **进行中 / 未完成**：未做完的步骤、卡在哪里、下一步建议
- **关键文件**：工作区内新生成/修改的文件绝对路径
- **关键决策与约定**：用户明确要求、被拒绝的方案、命名/格式偏好
- **环境备忘**：用到的 API、密钥名（不写值）、特殊命令

工作区文件用 Glob/Read 快速核对，不要通读无关文件。

### 2. 确认目标

向用户确认（若未给出）：
- 目标仓库（`owner/repo`）
- 交付形式：**HANDOFF.md 文件**（默认，分支 `docs/handoff`，PR 或直接提交 main）还是 **issue**（适合任务级交接）
- 标题/日期（默认当天日期）

### 3. 生成交接文档

按下方模板渲染 Markdown。保持紧凑：只写对接手者有用的信息，不贴大段代码或日志。

模板：

```markdown
# Handoff — {日期} — {主题}

## 已完成
- …

## 未完成 / 下一步
- [ ] …（附具体命令或文件位置）

## 关键文件
| 路径 | 说明 |
|---|---|
| `…` | … |

## 关键决策
- …

## 环境备忘
- …
```

### 4. 推送到 GitHub

使用 github 插件 MCP 工具（均已注册，直接调用，无需 select_tools）：

**HANDOFF.md 形式（默认）：**
1. `mcp__plugin-github_github__create_branch`：在默认分支上创建 `docs/handoff-{YYYYMMDD}` 分支（若已存在则复用）
2. `mcp__plugin-github_github__create_or_update_file`：在分支上写入 `HANDOFF.md`（message: `docs: add handoff {YYYYMMDD}`）
3. 向用户报告分支名与文件路径；用户要求 PR 时再调 `mcp__plugin-github_github__create_pull_request`

**Issue 形式：**
- `mcp__plugin-github_github__issue_write`：title `Handoff: {主题} ({日期})`，body 用同一模板内容

### 5. 汇报

给出仓库内的最终位置（分支 + 文件路径或 issue 编号/url），以及文档要点一句话总结。

## 注意

- 不要泄露任何密钥/令牌的值，只写变量名或来源。
- 用户没指定仓库时先问，不要猜。
- 同一仓库已有 HANDOFF.md 时，新交接写在顶部、旧内容归档到文末 `## 历史交接` 折叠段，而非覆盖。
