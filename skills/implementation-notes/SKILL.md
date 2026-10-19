---
name: implementation-notes
version: 1.0.0
description: "实现笔记与偏离日志：执行已批准的计划时维护 implementation-notes.md（Decisions / Deviations / Surprises / Next time），遇到迫使偏离计划的 edge case 时选保守方案、记录后继续推进不中断。当用户带着 spec / 计划 / 原型开新 session 让你实现、执行长任务需要留痕、或明确要求记录实现过程中的决策与偏离时使用。"
---

# implementation-notes（实现笔记与偏离日志）

针对阶段：实现中。无论计划做得多充分，**总有 unknown unknowns 潜伏在实现里**——agent 可能因为代码里发现的 edge case 而不得不换路。这份笔记让偏离可见、可复盘，并成为下一轮 prompt 的原料。

推荐用法：计划批准后**开新 session**，把 spec / 原型 / 计划作为 artifacts 传入，同时按本 skill 维护笔记。

## 触发信号

- 用户带着计划 / spec 让你开始实现。
- 长时间执行的任务，用户希望事后能看懂「路上发生了什么」。
- 用户字面说「记一份 implementation notes / 偏离了要记下来」。

## 工作流

1. **开工即建文件**：在任务目录创建 `implementation-notes.md`（临时文件，默认不提交进仓库；用户要求归档时移入 docs/ 并说明）。
2. **随做随记**，固定四节：
   - **Decisions**：计划未覆盖、由 agent 自行拍板的选择及理由；
   - **Deviations**：与计划不一致之处——触发原因（哪个 edge case / 哪段代码）、原计划、实际做法、影响；
   - **Surprises**：疆域与地图不符的发现（过时注释、隐藏耦合、性能意外），即使没导致偏离也记；
   - **Next time**：给下一轮迭代 / 下一次 prompt 的改进建议。
3. **偏离协议**（默认值；若计划的「即兴空间」另有约定，以计划为准）：
   - 遇到迫使偏离计划的 edge case → **选保守选项 → 记入 Deviations → 继续干**，不中断等用户；
   - 例外必须停下来确认：破坏性操作；触及计划标注的高风险决策（对外契约、数据模型）；偏离会改变任务目标本身。
4. **收尾提炼**：任务结束时把笔记浓缩成 3~5 条要点放进总结；若后接 `pitch-explainer` / `quiz-me`，本笔记是它们的直接输入。

## 记录格式

```markdown
# Implementation Notes：<任务>（<日期>）

## Decisions
- <决定>：<理由>

## Deviations
- <偏离>：触发=<edge case> | 原计划=<...> | 实际=<...> | 影响=<...>

## Surprises
- <发现>

## Next time
- <建议>
```

## 反模式

- 记成流水账（「改了 a.ts」「跑了测试」）：只记**决策、偏离、意外**——diff 能看出来的不记。
- 遇到偏离就停下来问：小偏离按协议保守处理并记录即可，频繁打断会毁掉长任务的价值。
- 偏离了不记，事后用户从 diff 里自己发现：信任损失最大的情形。
- 笔记写完就扔：Next time 一节正是下轮 prompt 质量提升的来源。

## 示例 prompt（用户视角）

> 「保持一份 implementation-notes.md。如果遇到迫使你偏离计划的 edge case：选保守的做法，记在 Deviations 下面，然后继续。」
