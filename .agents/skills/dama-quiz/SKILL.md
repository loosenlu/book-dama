---
name: dama-quiz
description: DAMA-DMBOK 章节小测验（CDGA 风格现场出题、交互作答、统一判卷）与全书 CDGA 冲刺模式。当用户说"开始 ChXX 考试"或"开始 CDGA 冲刺"时使用。
---

# dama-quiz：章节小测验

## 职责

用户触发某章考试时**现场生成** quiz → 逐题交互作答 → 收卷后统一判卷给分。定位：课堂小测验 + CDGA 备考——检验本章核心概念掌握，同时积累认证考试题感。本 Skill 不进 workflow 编排，由用户手动触发；成绩只记录 progress.md，不阻断进入下一章。

## 用户画像基线

用户是公有云数据库产品后端研发：数据库/存储/安全/网络/分布式等主题为老概念；数据治理为零基础新概念。认证目标 CDGA（数据治理工程师，DAMA 中国认证，基于 DMBOK）。

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

- 用户随时说「开始 ChXX 考试」。前置条件：`slides/chXX-slides.md` 已通过 validator 评审（查 progress.md「评审」列）；未通过则提示先完成课件评审。
- 全书学完后（17 章评审全部通过）可触发「开始 CDGA 冲刺」（50 题单选模拟考）。

## 执行流程

### Step 1: 出题（内部完成，不向用户展示过程）

主 Agent 读取 `slides/chXX-slides.md`（重点：章首学习目标 + 核心概念节 + 误区辨析表 + CDGA 考纲关联点）+ 该章历史 attempt 报告（如有，提取错题知识点做变式复测）→ 现场生成 12~15 题 quiz：

- 8~10 道 CDGA 风格单选/判断（对齐认证考法，覆盖高频考点）
- 3~5 道理解型简答（保住深度）

每题标注：考察概念 + slides 锚点（§X.Y）+ 判卷 rubric（得分点 checklist）→ 题目 + rubric 落盘 `.tmp/quiz-chXX-<日期>.md`（防 context 压缩/会话中断丢失判卷标准；用户自律不看，报告生成后清理；题量小，主 Agent 一次生成，不用 subagent）。

### Step 2: 交互作答

- 逐题出题，一次一题。
- 作答期间**不给任何对错反馈、不点评、不提示**，确认收到后立即出下一题。
- 作答进度随时追加到 `.tmp/quiz-chXX-<日期>.md`，中断可续考。
- 用户可随时「交卷」提前结束；未答题计 0 分。

### Step 3: 统一判卷（收卷后进行）

- 选择/判断：比对标准答案，不走模型感觉。
- 简答题：按 rubric 得分点逐条判定，覆盖一条给一条分，禁止整体感觉给分；每题写扣分理由。
- 批改诚实直接，答错或遗漏明确指出。

### Step 4: 出报告

逐题反馈三段式：得分与扣分理由 → 正确答案要点 → 回链 slides 锚点。总分 + 掌握度评级 + 薄弱知识点清单（答错题按概念聚类，标注 § 锚点）→ `quizzes/chXX-attempt-<YYYYMMDD>.md` → 更新 progress.md「考试」列与「掌握度」列，清理 `.tmp/quiz-chXX-<日期>.md`。

## 出题原则（质量核心）

- 题量 12~15 题，目标 20~25 分钟答完。
- 围绕章首学习目标、本章核心概念与 CDGA 考纲高频考点出题；边缘知识点不考。
- 单选/判断题对齐 CDGA 考法：概念辨析型选项（干扰项为易混淆概念，取自 slides 误区辨析表）。
- 简答题深度来自问法：优先"为什么 X 比 Y 适合场景 Z""这个做法会在什么情况下失效""治理推不动时怎么办"这类理解型问题；避免名词解释式默写。
- 历史错题知识点优先生成**变式题**（换场景/换角度，不重复原题），强化记忆。
- 每次 quiz 题目重新生成，防止背题；分数只作纵向参考，不做严格对比。

## 掌握度判定（建议性，不阻断）

| 得分 | 掌握度 | 建议 |
|------|--------|------|
| ≥85 | ⭐⭐⭐⭐⭐ 精通 | 直接进下一章 |
| 70-84 | ⭐⭐⭐⭐ 良好 | 可进下一章，建议回顾薄弱点 |
| 60-69 | ⭐⭐⭐ 基本掌握 | 建议按薄弱清单复习 slides 后重考 |
| <60 | ⭐⭐ 未掌握 | 强烈建议重学后重考 |

## 产物

`quizzes/chXX-attempt-<YYYYMMDD>.md`，含四部分：

1. 题目与原始作答（逐题原文）
2. 逐题判卷（得分 + 扣分理由 + 解析 + slides 锚点）
3. 总分与掌握度评级
4. 薄弱知识点清单

多次考试保留多份，错题知识点供下次变式复测。

## CDGA 冲刺模式（全书学完后触发「开始 CDGA 冲刺」）

- 一次性 50 题单选，覆盖全部知识领域，题量分布对齐 CDGA 考纲权重。
- 限时节奏模拟（建议 60 分钟内答完），交互作答规则同常规 quiz（逐题、无即时反馈、进度落盘可续考、可提前交卷）。
- 报告按知识领域聚类得分，标出冲刺复习重点。
- 产物：`quizzes/cdga-sprint-<YYYYMMDD>.md`。

## progress.md 更新

- 「考试」列：记录 quiz 次数与最近一次得分，如 `2 次，最近 82 分`。
- 「掌握度」列：填本次掌握度评级（⭐1~5）。
- CDGA 冲刺完成后勾选文末收官产物 checklist 的 CDGA 冲刺项。

## 交互与落盘规则

- 作答期间零反馈：不对错、不点评、不提示。
- 题目与判卷 rubric 必须落盘 `.tmp/quiz-chXX-<日期>.md`，作答进度随时追加，防 context 压缩或会话中断丢失判卷标准，中断可续考。
- 报告生成后清理对应 `.tmp` 文件。
- 全部产物使用地道中文；术语行内标注 `中文译名（English Term）`，中英对照以 `glossary.md` 为准。
