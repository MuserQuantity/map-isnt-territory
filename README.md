# Map Isn't Territory

一套用于发现未知项（unknowns）、缩小 **Map–Territory gap** 的 AI Agent Skills。方法论源自 Thariq Shihipar（Anthropic）关于“地图不是疆域”的实践：用户提供给 Agent 的 prompt、spec 和 context 只是地图，真实代码库、业务约束和实现细节才是疆域；复杂任务失控，往往不是模型能力不足，而是地图中遗漏了未知项。

本仓库包含 9 个可组合的 Skill，覆盖从任务开始前的盲区扫描、方案探索和需求访谈，到实现中的偏离记录，以及完成后的讲解与理解测验。

## 核心框架

| 象限 | 含义 | 典型信号 | 推荐 Skill |
| --- | --- | --- | --- |
| Known Knowns | 已知的已知 | 用户能直接写清要求 | 直接执行，无需额外技巧 |
| Known Unknowns | 已知的未知 | “这部分我还没想好” | `interview-me`、`brainstorm-prototype` |
| Unknown Knowns | 未知的已知 | 说不出标准，但看到就知道喜不喜欢 | `brainstorm-prototype`、`reference-mining` |
| Unknown Unknowns | 未知的未知 | 进入陌生模块，连该问什么都不知道 | `blindspot-pass` |

`finding-unknowns` 是总导航：先诊断未知项属于哪个象限，再选择后续 Skill；不需要每次把整套流程全部跑一遍。

## 9 个 Skills

| Skill | 阶段 | 用途 | 主要产出 |
| --- | --- | --- | --- |
| [`finding-unknowns`](skills/finding-unknowns/SKILL.md) | 全流程导航 | 用四象限诊断未知项并路由到合适技巧 | 未知项诊断表、推荐组合、下一步行动 |
| [`blindspot-pass`](skills/blindspot-pass/SKILL.md) | 实现前 | 扫描陌生领域或代码模块中的 unknown unknowns | 盲区报告、评价标准、改写后的 prompt |
| [`brainstorm-prototype`](skills/brainstorm-prototype/SKILL.md) | 实现前 | 用多方案或一次性原型显化隐性标准 | 方案清单、临时原型、scope 声明 |
| [`interview-me`](skills/interview-me/SKILL.md) | 实现前 | 基于真实任务歧义，一次一题完成需求访谈 | 决策记录、假设、开放问题 |
| [`reference-mining`](skills/reference-mining/SKILL.md) | 实现前/中 | 从参考源码提取本质语义，再按目标栈重实现 | 语义摘要、目标实现、对照检查 |
| [`implementation-plan`](skills/implementation-plan/SKILL.md) | 实现前 | 把高变动风险决策放到计划最前供 review | 决策导向计划、Review checklist |
| [`implementation-notes`](skills/implementation-notes/SKILL.md) | 实现中 | 记录实现中的决定、偏离和意外 | Decisions / Deviations / Surprises / Next time |
| [`pitch-explainer`](skills/pitch-explainer/SKILL.md) | 实现后 | 把结果、方案和风险打包成可分享的讲解文档 | 单文件 Markdown 或 HTML |
| [`quiz-me`](skills/quiz-me/SKILL.md) | 合并前 | 通过行为测验确认维护者真正理解变更 | 变更报告、逐题测验、理解判定 |

## 推荐组合

### 进入陌生领域

```text
blindspot-pass
  → brainstorm-prototype
  → reference-mining
  → implementation-plan
  → implementation-notes
  → pitch-explainer
```

### 熟悉代码库中的新功能

```text
brainstorm-prototype
  → interview-me
  → implementation-plan
  → implementation-notes
  → quiz-me
```

### 不知道从哪里开始

```text
finding-unknowns
```

### 小改动

直接执行即可；如果 Agent 完成了较多隐含改动，在合并前补一次 `quiz-me`。

## 安装

本仓库使用与 Agent 无关的分发目录，不额外包一层工具配置：

```text
skills/<skill-name>/SKILL.md
```

先获取仓库：

```bash
git clone https://github.com/MuserQuantity/map-isnt-territory.git
cd map-isnt-territory
```

推荐安装完整的 9 个 Skill，因为 `finding-unknowns` 会路由到其余 8 个。

### 安装到 Devin 项目

把仓库中的 `skills/*` 复制到目标项目的 `.devin/skills/`。

macOS / Linux：

```bash
mkdir -p /path/to/project/.devin/skills
cp -R skills/* /path/to/project/.devin/skills/
```

Windows PowerShell：

```powershell
$Target = "D:\path\to\project\.devin\skills"
New-Item -ItemType Directory -Force $Target | Out-Null
Copy-Item -Recurse -Force .\skills\* $Target
```

### 其他 Agent

按目标 Agent 的官方技能目录约定复制 `skills/` 下的 9 个目录。不要只复制总导航，否则它无法路由到其余 Skill。

## 调用示例

支持 Skill 自动发现的 Agent 会根据 frontmatter 中的 `description` 自动触发。也可以在提示词中明确点名。

### 总体诊断

```text
请使用 finding-unknowns，先判断这个任务中的 Known Unknowns、Unknown Knowns 和 Unknown Unknowns，再推荐最小必要的 Skill 组合。不要立即实现。
```

### 陌生模块盲区扫描

```text
我要给现有系统增加一个鉴权提供方，但完全不熟悉鉴权模块。请使用 blindspot-pass 扫描代码库，告诉我应该知道什么，并帮我改写下一步 prompt。
```

### 需求访谈

```text
请使用 interview-me 扫描需求和相关代码，一次只问一个问题，优先问答案会改变架构、数据模型或用户流程的问题。
```

### 参照物挖掘

```text
请使用 reference-mining 参考 vendor/rate-limiter 的退避行为。先给我本质语义、可替换的结构选择和应丢弃的偶然细节，确认后再实现。
```

### 实现与合并前检查

```text
按已批准计划实现，并使用 implementation-notes 记录 Decisions、Deviations、Surprises 和 Next time。
```

```text
合并前使用 quiz-me 给我变更报告并逐题考核；我真正理解全部行为和边界后再建议 merge。
```

## 依赖与边界

- 9 个 Skill 均为纯 Markdown 工作流，不需要安装额外运行时或第三方包。
- 实际效果取决于宿主 Agent 是否具备读取代码库、搜索资料、创建临时原型、编辑文件和运行测试等能力。
- `brainstorm-prototype` 生成的一次性原型默认不进入生产代码。
- `implementation-notes.md` 默认是任务期临时文件；只有用户要求归档时才移入项目文档目录。
- `quiz-me` 的目标是暴露理解债，不替代测试、代码评审或 CI。

## 目录结构

```text
map-isnt-territory/
├── skills/
│   ├── finding-unknowns/
│   │   └── SKILL.md
│   ├── blindspot-pass/
│   │   └── SKILL.md
│   └── ...                      # 共 9 个 Skill
├── LICENSE
└── README.md
```

## 来源与许可证

方法论归功于 Thariq Shihipar 关于 Map–Territory gap 与 finding unknowns 的文章和实践。本仓库是面向 AI Agent 的中文工作流实现与整理。

代码与 Skill 文件按 [MIT License](LICENSE) 发布。
