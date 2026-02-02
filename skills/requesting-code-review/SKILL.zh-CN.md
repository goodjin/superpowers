---
name: requesting-code-review
description: 在完成任务、实现主要功能或合并之前验证工作是否符合要求时使用
---

# 请求代码审查

分派 superpowers:code-reviewer 子代理以在问题级联之前捕获它们。

**核心原则：** 早审查，常审查。

## 何时请求审查

**强制性的：**
- 子代理驱动开发中的每个任务之后
- 完成主要功能之后
- 合并到 main 之前

**可选但有价值的：**
- 卡住时（新鲜视角）
- 重构之前（基线检查）
- 修复复杂bug之后

## 如何请求

**1. 获取 git SHA：**
```bash
BASE_SHA=$(git rev-parse HEAD~1)  # 或 origin/main
HEAD_SHA=$(git rev-parse HEAD)
```

**2. 分派 code-reviewer 子代理：**

使用 Task 工具与 superpowers:code-reviewer 类型，填写 `code-reviewer.md` 中的模板

**占位符：**
- `{WHAT_WAS_IMPLEMENTED}` - 你刚刚构建了什么
- `{PLAN_OR_REQUIREMENTS}` - 它应该做什么
- `{BASE_SHA}` - 起始提交
- `{HEAD_SHA}` - 结束提交
- `{DESCRIPTION}` - 简要总结

**3. 根据反馈行动：**
- 立即修复严重问题
- 在继续之前修复重要问题
- 记录次要问题稍后处理
- 如果审查者错了则反驳（附理由）

## 示例

```
[刚刚完成任务 2：添加验证函数]

你：让我在继续之前请求代码审查。

BASE_SHA=$(git log --oneline | grep "Task 1" | head -1 | awk '{print $1}')
HEAD_SHA=$(git rev-parse HEAD)

[分派 superpowers:code-reviewer 子代理]
  WHAT_WAS_IMPLEMENTED: 对话索引的验证和修复函数
  PLAN_OR_REQUIREMENTS: docs/plans/deployment-plan.md 中的任务 2
  BASE_SHA: a7981ec
  HEAD_SHA: 3df7661
  DESCRIPTION: 添加了 verifyIndex() 和 repairIndex()，包含4种问题类型

[子代理返回]：
  优点：清晰的架构，真实的测试
  问题：
    重要：缺少进度指示器
    次要：报告间隔的神奇数字（100）
  评估：可以继续

你：[修复进度指示器]
[继续任务 3]
```

## 与工作流集成

**子代理驱动开发：**
- 每个任务之后审查
- 在问题复合之前捕获它们
- 在移动到下一个任务之前修复

**执行计划：**
- 每个批次（3个任务）之后审查
- 获取反馈、应用、继续

**临时开发：**
- 合并之前审查
- 卡住时审查

## 危险信号

**永远不要：**
- 因为"它很简单"而跳过审查
- 忽略严重问题
- 带着未修复的重要问题继续
- 与有效的技术反馈争论

**如果审查者错了：**
- 用技术理由反驳
- 展示证明它有效的代码/测试
- 请求澄清

模板参见：requesting-code-review/code-reviewer.md
