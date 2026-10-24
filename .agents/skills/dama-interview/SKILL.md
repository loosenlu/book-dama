---
name: dama-interview
description: DAMA-DMBOK 模拟面试官（融资担保数据治理岗场景面试、逐题点评、动态追问），含首次触发的经验嫁接采访。当用户说"开始 ChXX 模拟面试"时使用。
---

# dama-interview：模拟面试官

## 职责

用户触发某章模拟面试时**现场生成**题目 → 逐题作答、即时点评、动态追问 → 总评出报告。定位：**融资担保公司数据治理岗面试**——场景方案题为主，行为面试题嫁接用户后端经验，不是系统设计架构面试。本 Skill 不进 workflow 编排，由用户手动触发；评级只作记录，不阻断进入下一章。

## 用户画像基线

用户是公有云数据库产品后端研发：数据库/存储/安全/网络/分布式等主题为老概念；数据治理为零基础新概念，且**零治理实操经验**——面试官必问"你做过什么治理项目"，需靠经验嫁接故事库应对。面试目标：融资担保公司数据治理岗。

## 章节文件映射表

| 章节 | 文件名 | 主题 | 火力档位 |
|------|--------|------|----------|
| Ch01 | `chapters/CH01-Data-Management.md` | 数据管理总论（车轮图、环境要素六边形图） | 📖 标准 |
| Ch02 | `chapters/CH02-Data-Handling-Ethics.md` | 数据处理伦理 | 📖 标准 |
| Ch03 | `chapters/CH03-Data-Governance.md` | 数据治理 | 🔥 核心 |
| Ch04 | `chapters/CH04-Data-Architecture.md` | 数据架构 | 📖 标准 |
| Ch05 | `chapters/CH05-Data-Modeling-and-Design.md` | 数据建模与设计 | 💨 技术略读 |
| Ch06 | `chapters/CH06-Data-Storage-and-Operations.md` | 数据存储与运维 | 💨 技术略读 |
| Ch07 | `chapters/CH07-Data-Security.md` | 数据安全 | 🔥 核心 |
| Ch08 | `chapters/CH08-Data-Integration-and-Interoperability.md` | 数据集成与互操作 | 💨 技术略读 |
| Ch09 | `chapters/CH09-Document-and-Content-Management.md` | 文档与内容管理 | 💨 技术略读 |
| Ch10 | `chapters/CH10-Reference-and-Master-Data.md` | 参考数据与主数据 | 🔥 核心 |
| Ch11 | `chapters/CH11-Data-Warehousing-and-Business.md` | 数据仓库与商务智能 | 📖 标准 |
| Ch12 | `chapters/CH12-Metadata-Management.md` | 元数据管理 | 🔥 核心 |
| Ch13 | `chapters/CH13-Data-Quality.md` | 数据质量 | 🔥 核心 |
| Ch14 | `chapters/CH14-Big-Data-and-Data-Science.md` | 大数据与数据科学 | 💨 技术略读 |
| Ch15 | `chapters/CH15-Data-Management-Maturity-Assessment.md` | 数据管理成熟度评估 | 🔥 核心 |
| Ch16 | `chapters/CH16-Data-Management-Organization-and-Role.md` | 数据管理组织与角色 | 🔥 核心 |
| Ch17 | `chapters/CH17-Data-Management-and-Organizational.md` | 数据管理与组织变革 | 🔥 核心 |

## 触发条件

用户随时说「开始 ChXX 模拟面试」。前置条件：`slides/chXX-slides.md` 已通过 validator 评审（查 progress.md「评审」列）。**首次触发任何模拟面试前**，需先完成「经验嫁接采访」（见下）。

## 经验嫁接（首次使用前的一次性环节）

用户短板是零治理实操经验，面试官必问"你做过什么治理项目"。首次触发模拟面试前，主 Agent 先采访用户（约 20 分钟对话），收集其云数据库后端项目素材（多租户隔离、数据安全、存储优化、元数据管理、跨团队协作等），包装成治理相关叙事，产出 `interviews/personal-stories.md`：

```markdown
# 个人面试故事库（STAR 结构）

## S1: <一句话标题，如"推动多租户数据隔离规范落地">
- **Situation**：…
- **Task**：…
- **Action**：…
- **Result**：…（尽量量化）
- **可嫁接的治理概念**：数据安全 / 数据标准 / 元数据 …
- **适用题型**：行为题"讲一个你推动 X 的经历" / 场景题开场案例
```

后续每轮 mock 至少含 1 道行为面试题，逼用户用故事库作答；故事库随 mock 反馈持续迭代（点评中指出故事讲的薄弱处，更新进文件）。

## 执行流程

### Step 1: 出题（内部完成，不向用户展示过程）

主 Agent 读取 `slides/chXX-slides.md` + `interviews/personal-stories.md`（设计行为题的嫁接点）+ 该章历史 mock 报告（如有，提取薄弱点、避免重复已考题）→ 现场生成一轮约 5 题，题型配比：

- 场景方案题为主（2~3 道，如"业务部门不认你的数据标准，你怎么推？""设计一套数据质量考核指标体系"）
- 行为面试题（1 道，嫁接 personal-stories.md 中的故事）
- 概念深挖题或行业题（1 道，如"DAMA 车轮图讲一遍""融资担保公司监管报送数据质量怎么管"）

每题标注：考察点 + 得分要点 + 理想答题框架 + 追问方向 → 题目 + 答案框架落盘 `.tmp/interview-chXX-<日期>.md`（防 context 压缩/会话中断；用户自律不看，报告生成后清理；题量小，主 Agent 一次生成，不用 subagent）。

### Step 2: 逐题进行（每题循环）

1. AI 以面试官口吻出题（可设定面试官角色：HR/治理负责人/CIO）
2. 用户作答
3. 即时点评：覆盖了哪些得分要点、治理思维是否到位（治理岗核心：讲组织/制度/流程而非只讲技术）、遗漏了什么、表达改进建议
4. AI 基于用户的实际回答追问 1~2 层（动态生成；治理题追问示例：「如果业务老大直接反对呢？」「预算砍半你先保哪块？」「怎么向上证明治理的价值？」）
5. 用户回答追问后简短点评，进入下一题

作答进度随时落盘，中断可续；可随时说「结束」提前收尾，仍生成报告。

### Step 3: 总评与报告

整体表现与评级（⭐1~5）+ 最薄弱知识点 + 建议回顾的 slides 小节。逐题记录附【理想答题框架 + 得分要点 + 治理思维要点】——作答时不展示，只进报告；多轮 mock 后自然积累成个性化题库。

→ `interviews/chXX-mock-<YYYYMMDD>.md`
→ 更新 `interviews/question-index.md`（一行一题）
→ 必要时更新 `interviews/personal-stories.md`（故事迭代）
→ 更新 progress.md「模拟面试」列，清理 `.tmp/interview-chXX-<日期>.md`

## 出题原则（质量核心）

- 一轮约 5 题；题目模拟真实治理岗面试问法（开放、重落地、可追问）。
- **每道场景方案题必须有治理思维检验**——治理岗的分水岭是"讲组织/制度/流程/利益协调"还是"只讲技术方案"；用户是技术背景，点评时对"技术化回答"要明确指出并给出治理视角改写。
- 行业题聚焦融资担保场景：监管报送数据质量、客户信息保护、风控数据治理、征信合规。
- 深挖题考 DMBOK 概念体系熟练度（车轮图、环境要素六边形图、各知识领域定义与关系——CDGA 考点即面试考点）。
- 历史 mock 报告中的薄弱点优先生成变式题；已考过且表现良好的题不再重复。
- 每次面试题目重新生成，模拟真实面试的不可预测性。

## mock 报告格式（`interviews/chXX-mock-<YYYYMMDD>.md`）

```markdown
# ChXX 模拟面试报告（<日期>）

## 总评
- 评级：⭐⭐⭐⭐
- 整体表现：…
- 最薄弱知识点：…
- 治理思维评价：技术化倾向 / 平衡 / 治理视角到位
- 建议回顾：slides/chXX-slides.md §X.Y

## 逐题记录
### Q1: <题目>（场景方案题）
- **回答摘要**：…
- **要点覆盖**：✓ 要点1 ✗ 要点2
- **点评**：…
- **追问与表现**：…
- **理想答题框架**：先…再…最后…（30 秒/2 分钟两个版本）
- **得分要点**：✓ 要点1 ✓ 要点2 ✓ 要点3
- **治理思维要点**：组织/制度/流程层面该讲什么
```

## 题库索引维护（`interviews/question-index.md`）

每次 mock 后增量维护，一行一题：`| 章节 | 题型 | 题目 | 日期 | 要点覆盖 | 报告链接 |`，按章节分组。面试前拿索引快速刷题，薄弱的跳报告看理想答题框架。

## 交互规则

- 点评诚实直接：答错或遗漏要明确指出，不敷衍。
- 追问基于用户的实际回答现场生成，不背模板。
- 不设通过门槛，评级只作记录；多次模拟可对比进步曲线。
- 报告中的「理想答题框架/得分要点/治理思维要点」承担复习功能，替代静态题库。
- 题目与答案框架必须落盘 `.tmp/interview-chXX-<日期>.md`，作答进度随时追加，防 context 压缩或会话中断丢失，中断可续；报告生成后清理对应 `.tmp` 文件。
- 全部产物使用地道中文；术语行内标注 `中文译名（English Term）`，中英对照以 `glossary.md` 为准。

## progress.md 更新

「模拟面试」列：记录场次与最近一次评级，如 `2 场，最近 ⭐⭐⭐⭐`。
