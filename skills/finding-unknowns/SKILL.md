---
name: finding-unknowns
version: 1.0.0
description: "发现未知项（unknowns）的元框架与导航：用四象限诊断当前任务中的未知项类型，推荐技巧组合并路由到 blindspot-pass / brainstorm-prototype / interview-me / reference-mining / implementation-plan / implementation-notes / pitch-explainer / quiz-me。当用户开始新项目或复杂任务想系统性澄清需求、说「帮我找 unknowns / 盲区 / 不知道从哪开始」、觉得 prompt 总写不清楚、或长任务结果不对想复盘原因时使用。"
---

# finding-unknowns（发现你的未知项 · 导航）

方法论出处：Thariq《A Field Guide to Claude Fable: Finding Your Unknowns》。

## 核心理念：地图 ≠ 疆域

- **地图** = 用户交给 agent 的东西：prompt、skill、context。
- **疆域** = 工作实际发生的地方：代码库、真实世界、实际约束。
- **未知项（unknowns）** = 地图与疆域的差距。agent 遇到未知项时只能按「对用户意图的最佳猜测」行事；做的工作越多，撞上的未知项越多。

模型越强，工作质量的瓶颈越从「模型能力」转移到「用户澄清未知项的能力」。**长任务结果不对时，第一归因不是模型不行**，而是：需要花更多时间定义未知项，或制定一个允许 agent 即兴应对的计划。

**提前规划并不总是够**：未知项会在实现深处冒出来，甚至会指向「应该换一种方式解决问题」。发现未知项是贯穿实现前、中、后的迭代过程，不是一次性的计划环节。

## 第一步：让用户披露起点

所有技巧生效的前提是 agent 知道用户的起点。开始前先弄清（缺哪个补哪个）：

1. 用户在思考过程中的什么位置？（刚有念头 / 已有倾向方案 / 已做过一版）
2. 用户对问题领域的经验？（专家 / 用过 / 完全陌生）
3. 用户对代码库相关模块的熟悉度？
4. 期望 agent 扮演什么角色？（思考伙伴 / 执行者 / 老师）

## 第二步：四象限诊断

| 象限 | 判断标志 | 应对技巧 |
|------|---------|---------|
| **Known Knowns** 已知的已知 | 用户能直接写进 prompt 的要求 | 无需技巧，写清楚即可 |
| **Known Unknowns** 已知的未知 | 用户明确说「这块我还没想好」 | `interview-me`（逐题澄清）、`brainstorm-prototype`（发散选项） |
| **Unknown Knowns** 未知的已知 | 说不出标准但「见到就认得」；涉及品味/设计/体验 | `brainstorm-prototype`（做出来让用户 react）、`reference-mining`（用参照物代替语言） |
| **Unknown Unknowns** 未知的未知 | 对领域/模块完全陌生，连该问什么都不知道；不知道「好」能有多好 | `blindspot-pass`（盲区扫描 + 教学） |

诊断口诀：说得出 → 直接写；知道没想好 → 访谈/发散；见到才认得 → 原型/参照物；连问题都提不出 → 盲区扫描。

## 第三步：按阶段路由

| 阶段 | 场景 | skill |
|------|------|-------|
| 实现前 | 进入陌生领域/模块 | `blindspot-pass` |
| 实现前 | 探索方案空间、定 scope、看设计方向 | `brainstorm-prototype` |
| 实现前 | 消除残留歧义 | `interview-me` |
| 实现前 | 说不清但有参照物 | `reference-mining` |
| 实现前 | 收敛成可 review 的计划 | `implementation-plan` |
| 实现中 | 执行计划、应对途中冒出的未知 | `implementation-notes` |
| 实现后 | 向他人解释、拿 buy-in | `pitch-explainer` |
| 实现后 | 确认自己理解了变更、merge 前把关 | `quiz-me` |

不必每次全用。典型组合：

- 熟悉代码库里的新功能：brainstorm → interview → plan → notes → quiz
- 陌生领域：blindspot → brainstorm → reference → plan → notes → pitch
- 小改动：直接干，最多收尾 quiz 一下

## 指令平衡原则（为什么值得做这些）

- 指令**太具体** → 即使转向更合适，agent 也会死板执行。
- 指令**太模糊** → agent 按行业最佳实践做假设，可能完全不适合本任务。
- 不处理未知项就会**双向失败**：既不知道路上何时有障碍，也不知道路何时通畅、但其实你想让它拐弯。

每个 explainer、brainstorm、interview、prototype、reference，都是在「修复变贵之前」发现你不知道的东西的便宜方式。

## 案例：Fable 发布视频（技巧动态切换）

作者用 Claude Code 剪辑发布视频（自己非专家）：

1. 从已知出发：知道能用代码剪视频和转写，但不确定精度 → 让 Claude 解释 Whisper 原理、判断 ffmpeg 能否剪掉「um」和长停顿（**blindspot-pass**）。
2. 不确定 UI 能否与语音逐词同步 → 用 Remotion + 转写文本做原型验证（**brainstorm-prototype**）。
3. 画面发闷，知道与 color grading 有关但不懂 → 先试「做几个变体来挑」，发现自己不知道「好」长什么样、挑选无效 → 转为让 Claude 教 color grading（从原型法**退回 blindspot-pass**）。

启示：当用户连评判标准都没有时，多方案对比是无效的——先补认知，再做选择。

## Agent 执行流程

1. 收集用户起点（见第一步）。
2. 和用户一起把任务拆进四象限，指出每个象限里具体有什么。
3. 给出建议的技巧组合与顺序，说明理由。
4. 经用户同意后，依次调用对应 skill（若已安装）或按其思路执行。

## 产出

Markdown「未知项诊断表」：四象限清单 + 建议技巧组合 + 下一步行动。用户偏好可视化时可改用单文件 HTML。
