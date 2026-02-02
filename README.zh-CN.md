# 超能力 (Superpowers)

Superpowers 是一套完整的编程代理（coding agents）软件开发工作流，构建在一组可组合的"技能（skills）"之上，并附带一些初始指令，确保你的代理会使用这些技能。

## 工作原理

从你启动编程代理的那一刻起，它就开始运作了。当它看到你正在构建某些东西时，它**不会**立刻跳入编写代码的阶段。相反，它会退一步，询问你真正想要做什么。

一旦它从对话中提取出了规格说明，它会将设计分块展示给你，每个块都足够简短，可以实际阅读和消化。

在你签字批准设计后，你的代理会制定一个实现计划，清晰到足以让一个热情高涨但品味不佳、没有判断力、没有项目背景、讨厌测试的初级工程师也能遵循。它强调真正的红/绿测试驱动开发（TDD）、YAGNI（你不需要它）原则和DRY（不要重复自己）原则。

接下来，一旦你说"开始"，它就会启动一个*子代理驱动开发（subagent-driven-development）*流程，让代理逐一处理每个工程任务，检查并审查他们的工作，然后继续前进。Claude 能够自主工作一两个小时而不偏离你共同制定的计划，这并不罕见。

还有更多内容，但这就是系统的核心。而且由于技能是自动触发的，你不需要做任何特殊的事情。你的编程代理只是拥有了超能力。


## 赞助

如果 Superpowers 帮助你完成了赚钱的工作，并且你有这个意愿，我将非常感激你考虑[赞助我的开源工作](https://github.com/sponsors/obra)。

谢谢！

- Jesse


## 安装

**注意：** 不同平台的安装方式不同。Claude Code 有内置的插件系统。Codex 和 OpenCode 需要手动设置。

### Claude Code（通过插件市场）

在 Claude Code 中，首先注册市场：

```bash
/plugin marketplace add obra/superpowers-marketplace
```

然后从这个市场安装插件：

```bash
/plugin install superpowers@superpowers-marketplace
```

### 验证安装

检查命令是否出现：

```bash
/help
```

```
# 应该看到：
# /superpowers:brainstorm - 交互式设计细化
# /superpowers:write-plan - 创建实现计划
# /superpowers:execute-plan - 批量执行计划
```

### Codex

告诉 Codex：

```
从 https://raw.githubusercontent.com/obra/superpowers/refs/heads/main/.codex/INSTALL.md 获取并遵循说明
```

**详细文档：** [docs/README.codex.md](docs/README.codex.md)

### OpenCode

告诉 OpenCode：

```
从 https://raw.githubusercontent.com/obra/superpowers/refs/heads/main/.opencode/INSTALL.md 获取并遵循说明
```

**详细文档：** [docs/README.opencode.md](docs/README.opencode.md)

## 基本工作流

1. **头脑风暴 (brainstorming)** - 在编写代码之前激活。通过提问来完善粗略的想法，探索替代方案，分块展示设计以供验证。

2. **使用 Git 工作区 (using-git-worktrees)** - 在设计批准后激活。在新的分支上创建隔离的工作区，运行项目设置，验证干净的测试基线。

3. **编写计划 (writing-plans)** - 在获得批准的设计时激活。将工作分解为小块任务（每个2-5分钟）。每个任务都有精确的文件路径、完整的代码、验证步骤。

4. **子代理驱动开发 (subagent-driven-development)** 或 **执行计划 (executing-plans)** - 在获得计划时激活。为每个任务分派新的子代理，进行两阶段审查（规格符合性，然后是代码质量），或批量执行并设置人工检查点。

5. **测试驱动开发 (test-driven-development)** - 在实现过程中激活。强制执行红-绿-重构（RED-GREEN-REFACTOR）：编写失败的测试，观察它失败，编写最少的代码，观察它通过，提交。删除测试之前编写的代码。

6. **请求代码审查 (requesting-code-review)** - 在任务之间激活。根据计划进行审查，按严重级别报告问题。严重问题会阻止进度。

7. **完成开发分支 (finishing-a-development-branch)** - 在任务完成时激活。验证测试，提供选项（合并/PR/保留/丢弃），清理工作区。

**代理在执行任何任务之前都会检查相关技能。** 这是强制性的工作流，不是建议。

## 内容概览

### 技能库

**测试**
- **test-driven-development** - 红-绿-重构循环（包括测试反模式参考）

**调试**
- **systematic-debugging** - 四阶段根本原因分析流程（包括根本原因追踪、纵深防御、基于条件的等待技术）
- **verification-before-completion** - 确保问题确实已修复

**协作**
- **brainstorming** - 苏格拉底式设计细化
- **writing-plans** - 详细的实现计划
- **executing-plans** - 批量执行并设置检查点
- **dispatching-parallel-agents** - 并发的子代理工作流
- **requesting-code-review** - 预审查检查清单
- **receiving-code-review** - 响应反馈
- **using-git-worktrees** - 并行开发分支
- **finishing-a-development-branch** - 合并/PR 决策工作流
- **subagent-driven-development** - 快速迭代并进行两阶段审查（规格符合性，然后是代码质量）

**元技能**
- **writing-skills** - 按照最佳实践创建新技能（包括测试方法）
- **using-superpowers** - 技能系统简介

## 理念

- **测试驱动开发** - 始终先写测试
- **系统化优于临时性** - 流程优于猜测
- **降低复杂度** - 简洁是首要目标
- **证据优于断言** - 在宣布成功之前先验证

阅读更多：[Claude Code 的超能力](https://blog.fsck.com/2025/10/09/superpowers/)

## 贡献

技能直接存在于这个仓库中。要贡献：

1. Fork 这个仓库
2. 为你的技能创建一个分支
3. 遵循 `writing-skills` 技能来创建和测试新技能
4. 提交 PR

完整的指南请参见 `skills/writing-skills/SKILL.md`。

## 更新

当你更新插件时，技能会自动更新：

```bash
/plugin update superpowers
```

## 许可证

MIT 许可证 - 详情请参见 LICENSE 文件

## 支持

- **问题：** https://github.com/obra/superpowers/issues
- **市场：** https://github.com/obra/superpowers-marketplace
