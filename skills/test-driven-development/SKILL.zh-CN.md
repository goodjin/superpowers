---
name: test-driven-development
description: 在编写实现代码之前，实现任何功能或修复bug时使用
---

# 测试驱动开发 (TDD)

## 概述

先写测试。观察它失败。编写最少的代码使其通过。

**核心原则：** 如果你没有看到测试失败，你就不知道它是否测试了正确的东西。

**违反规则的字面意思就是违反规则的精神。**

## 何时使用

**始终使用：**
- 新功能
- Bug修复
- 重构
- 行为变更

**例外情况（询问你的人类伙伴）：**
- 一次性原型
- 生成的代码
- 配置文件

想"就这一次跳过TDD"？停下。那是合理化。

## 铁律

```
没有失败的测试，就没有生产代码
```

在测试之前写代码？删除它。重新开始。

**没有例外：**
- 不要将其保留为"参考"
- 不要"改编"它来写测试
- 不要看它
- 删除就是删除

从测试开始全新实现。就是这样。

## 红-绿-重构

```dot
digraph tdd_cycle {
    rankdir=LR;
    red [label="红\n编写失败的测试", shape=box, style=filled, fillcolor="#ffcccc"];
    verify_red [label="验证失败\n正确", shape=diamond];
    green [label="绿\n最少代码", shape=box, style=filled, fillcolor="#ccffcc"];
    verify_green [label="验证通过\n全部变绿", shape=diamond];
    refactor [label="重构\n清理", shape=box, style=filled, fillcolor="#ccccff"];
    next [label="下一个", shape=ellipse];

    red -> verify_red;
    verify_red -> green [label="是"];
    verify_red -> red [label="错误\n失败"];
    green -> verify_green;
    verify_green -> refactor [label="是"];
    verify_green -> green [label="否"];
    refactor -> verify_green [label="保持\n绿色"];
    verify_green -> next;
    next -> red;
}
```

### 红 - 编写失败的测试

编写一个展示应该发生什么的简单测试。

<好>
```typescript
test('重试失败操作3次', async () => {
  let attempts = 0;
  const operation = () => {
    attempts++;
    if (attempts < 3) throw new Error('fail');
    return 'success';
  };

  const result = await retryOperation(operation);

  expect(result).toBe('success');
  expect(attempts).toBe(3);
});
```
清晰的名称，测试真实行为，一件事
</好>

<坏>
```typescript
test('重试有效', async () => {
  const mock = jest.fn()
    .mockRejectedValueOnce(new Error())
    .mockRejectedValueOnce(new Error())
    .mockResolvedValueOnce('success');
  await retryOperation(mock);
  expect(mock).toHaveBeenCalledTimes(3);
});
```
模糊的名称，测试mock而不是代码
</坏>

**要求：**
- 一个行为
- 清晰的名称
- 真实代码（除非不可避免，否则不要使用mock）

### 验证红 - 观察它失败

**强制性的。永远不要跳过。**

```bash
npm test path/to/test.test.ts
```

确认：
- 测试失败（不是错误）
- 失败消息是预期的
- 因为功能缺失而失败（不是拼写错误）

**测试通过了？** 你在测试现有行为。修复测试。

**测试出错了？** 修复错误，重新运行直到正确失败。

### 绿 - 最少代码

编写最简单的代码来通过测试。

<好>
```typescript
async function retryOperation<T>(fn: () => Promise<T>): Promise<T> {
  for (let i = 0; i < 3; i++) {
    try {
      return await fn();
    } catch (e) {
      if (i === 2) throw e;
    }
  }
  throw new Error('unreachable');
}
```
刚好够通过
</好>

<坏>
```typescript
async function retryOperation<T>(
  fn: () => Promise<T>,
  options?: {
    maxRetries?: number;
    backoff?: 'linear' | 'exponential';
    onRetry?: (attempt: number) => void;
  }
): Promise<T> {
  // YAGNI - 你不会需要它
}
```
过度设计
</坏>

不要添加功能，重构其他代码，或"改进"超出测试范围。

### 验证绿 - 观察它通过

**强制性的。**

```bash
npm test path/to/test.test.ts
```

确认：
- 测试通过
- 其他测试仍然通过
- 输出干净（没有错误、警告）

**测试失败？** 修复代码，不是测试。

**其他测试失败？** 现在修复。

### 重构 - 清理

只有在变绿之后：
- 移除重复
- 改进名称
- 提取辅助函数

保持测试绿色。不要添加行为。

### 重复

下一个功能的下一个失败测试。

## 好的测试

| 质量 | 好 | 坏 |
|---------|------|-----|
| **最小化** | 一件事。名称中有"和"？拆分它。 | `test('验证电子邮件和域名和空白字符')` |
| **清晰** | 名称描述行为 | `test('test1')` |
| **展示意图** | 演示期望的API | 掩盖代码应该做什么 |

## 为什么顺序很重要

**"我之后会写测试来验证它有效"**

之后写的测试立即通过。立即通过证明不了什么：
- 可能测试了错误的东西
- 可能测试实现，而不是行为
- 可能错过了你忘记的边缘情况
- 你从未看到它捕获bug

测试优先迫使你看到测试失败，证明它实际上测试了某些东西。

**"我已经手动测试了所有边缘情况"**

手动测试是临时的。你认为你测试了一切，但是：
- 没有测试记录
- 代码更改时无法重新运行
- 压力下容易忘记情况
- "我试的时候有效" ≠ 全面

自动化测试是系统化的。它们每次都以相同的方式运行。

**"删除X小时的工作是浪费"**

沉没成本谬误。时间已经过去了。你现在的选择：
- 删除并用TDD重写（X更多小时，高信心）
- 保留并之后添加测试（30分钟，低信心，可能有bug）

"浪费"是保留你无法信任的代码。没有真正测试的有效代码是技术债务。

**"TDD是教条的，务实意味着适应"**

TDD就是务实的：
- 在提交之前发现bug（比之后调试更快）
- 防止回归（测试立即捕获中断）
- 记录行为（测试展示如何使用代码）
- 启用重构（自由更改，测试捕获中断）

"务实"的捷径 = 在生产环境调试 = 更慢。

**"之后的测试实现相同的目标 - 重要的是精神不是仪式"**

不。之后的测试回答"这做什么？" 优先的测试回答"这应该做什么？"

之后的测试受你的实现偏见影响。你测试你构建的，而不是需要的。你验证记住的边缘情况，而不是发现的。

优先的测试在实施之前强制发现边缘情况。之后的测试验证你是否记住了所有（你没有）。

30分钟的之后测试 ≠ TDD。你得到了覆盖率，失去了测试有效的证明。

## 常见合理化

| 借口 | 现实 |
|--------|---------|
| "太简单，不需要测试" | 简单代码也会出问题。测试只需30秒。 |
| "我之后会测试" | 立即通过的测试证明不了什么。 |
| "之后的测试实现相同目标" | 之后 = "这做什么？" 优先 = "这应该做什么？" |
| "已经手动测试过了" | 临时 ≠ 系统化。没有记录，无法重新运行。 |
| "删除X小时是浪费" | 沉没成本谬误。保留未验证代码是技术债务。 |
| "保留作为参考，先写测试" | 你会改编它。那是之后测试。删除就是删除。 |
| "需要先探索" | 可以。丢弃探索，用TDD开始。 |
| "测试困难 = 设计不清晰" | 倾听测试。难测试 = 难使用。 |
| "TDD会拖慢我" | TDD比调试快。务实 = 测试优先。 |
| "手动测试更快" | 手动不能证明边缘情况。每次更改都要重新测试。 |
| "现有代码没有测试" | 你在改进它。为现有代码添加测试。 |

## 危险信号 - 停下并重新开始

- 测试之前的代码
- 实现之后测试
- 测试立即通过
- 无法解释为什么测试失败
- "稍后"添加的测试
- 合理化"就这一次"
- "我已经手动测试过了"
- "之后的测试实现相同目的"
- "重要的是精神不是仪式"
- "保留作为参考"或"改编现有代码"
| "已经花了X小时，删除是浪费" |
| "TDD是教条的，我在务实" |
| "这不同因为..." |

**所有这些都意味着：删除代码。用TDD重新开始。**

## 示例：Bug修复

**Bug：** 接受空电子邮件

**红**
```typescript
test('拒绝空电子邮件', async () => {
  const result = await submitForm({ email: '' });
  expect(result.error).toBe('需要电子邮件');
});
```

**验证红**
```bash
$ npm test
FAIL: 预期 '需要电子邮件'，得到 undefined
```

**绿**
```typescript
function submitForm(data: FormData) {
  if (!data.email?.trim()) {
    return { error: '需要电子邮件' };
  }
  // ...
}
```

**验证绿**
```bash
$ npm test
PASS
```

**重构**
如果需要，为多字段提取验证。

## 验证检查清单

在标记工作完成之前：

- [ ] 每个新函数/方法都有一个测试
- [ ] 在实施之前观察每个测试失败
- [ ] 每个测试因预期原因失败（功能缺失，不是拼写错误）
- [ ] 编写最少的代码来通过每个测试
- [ ] 所有测试通过
- [ ] 输出干净（没有错误、警告）
- [ ] 测试使用真实代码（除非不可避免，否则不使用mock）
- [ ] 涵盖边缘情况和错误

无法勾选所有框？你跳过了TDD。重新开始。

## 卡住时

| 问题 | 解决方案 |
|---------|----------|
| 不知道如何测试 | 编写期望的API。先写断言。询问你的人类伙伴。 |
| 测试太复杂 | 设计太复杂。简化接口。 |
| 必须mock一切 | 代码太耦合。使用依赖注入。 |
| 测试设置巨大 | 提取辅助函数。仍然复杂？简化设计。 |

## 调试集成

发现bug？编写失败的测试来复现它。遵循TDD循环。测试证明修复并防止回归。

永远不要在没有测试的情况下修复bug。

## 测试反模式

添加mock或测试工具时，阅读 @testing-anti-patterns.md 以避免常见陷阱：
- 测试mock行为而不是真实行为
- 向生产类添加仅测试的方法
- 不理解依赖就进行mock

## 最终规则

```
生产代码 → 测试存在且先失败
否则 → 不是TDD
```

没有例外，除非得到你的人类伙伴的许可。
