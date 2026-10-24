---
name: dama-workflow
description: 编排 DAMA-DMBOK 章节学习流程（Teacher→Validator→更新进度），并负责两个收官产物。当用户说开始/继续某章学习（如"开始 Ch03"）、或触发收官动作（"生成公司治理方案"）时使用。
---

# dama-workflow：章节编排者

## 职责

协调整个章节学习流程，按顺序驱动各 Skill，追踪进度。本 Skill 只编排与追踪，不亲自写课件、不亲自评审。

## 用户画像基线

用户是公有云数据库产品后端研发：数据库内核、存储、安全机制、分布式系统、网络等主题按**老概念**处理；数据治理知识体系为**零基础新概念**。双目标：主线为通过融资担保公司数据治理岗面试；辅线为在自己公司推动数据治理落地。认证目标 CDGA。

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

火力档位处理规则：
- 🔥 核心档（8 章）：重点展开，slides 含「金融行业映射」节 +「落地视角」节。
- 📖 标准档（4 章）：正常讲解；技术细节用户是熟手，但治理视角是新内容，正常展开。
- 💨 技术略读档（5 章）：技术细节按老概念略过，只讲治理视角下这章管什么；仍保留「落地视角」节。

## 输入

用户指定目标章节（如 "Ch03"）。只需章节号，本映射表负责定位源文件与档位。

## 输出

更新 `progress.md`，触发下游 Skill。

## 执行流程

```
Step 1: 查章节文件映射表，定位 chapters/ 中的源文件，确定火力档位
Step 2: 调用 dama-teacher 生成 slides/chXX-slides.md，并增量更新 glossary.md
Step 3: 调用 dama-validator 评审 slides（课程评委制）
        ├── 五维度全部通过 → 通过，继续
        └── 打回 → 将修改意见清单交回 dama-teacher 按单局部修改后重验
            （最多 2 轮打回，第 3 次仍不通过则停止循环，
              把评审报告呈现给用户人工裁决）
Step 4: 更新 progress.md，标记当前章节状态
```

## 编排规则

- 按 Ch01 → Ch02 → … → Ch17 顺序学习。
- 每章内部流程：Teacher → Validator。Validator 打回时，把修改意见清单交回 dama-teacher **按单局部修改**（非全文重写），修改后重验；最多 2 轮打回，第 3 次仍不通过则停止循环，把评审报告呈现给用户人工裁决。
- **考核类环节全部不进编排流程**：不预生成考题/面试题，由用户随时手动发起（「开始 ChXX 考试」→ dama-quiz；「开始 ChXX 模拟面试」→ dama-interview），触发时现场出题；成绩记入 progress.md 供回顾，不阻断进入下一章。
- 每完成一步自动更新 progress.md。

## 收官动作（编排内，两个特殊触发点）

1. **CH17 评审通过后**：汇总各核心章「金融行业映射」节 + WebSearch 补充调研（来源限监管机构官网、官方文件、权威媒体，附链接并标注信息截至时间），生成《融资担保行业数据治理监管速查手册》→ `deliverables/financing-guarantee-regulation-handbook.md`。
2. **CH15/CH16/CH17 全部评审通过后**（用户随时可触发「生成公司治理方案」）：生成前先采访用户收集公司现状素材（数据团队规模、现有系统、痛点，约 15~20 分钟对话），然后输出《你公司的数据治理方案框架》→ `deliverables/company-governance-blueprint.md`（结构：成熟度自评 → 目标态 → 路线图 → 组织设计 → 制度清单）。

## progress.md 更新规则

每完成一步更新 `progress.md`：

- 「课件」列：teacher 产出 slides 后标 ✅。
- 「评审」列：validator 通过后标 ✅；打回中可标注轮次。
- 「考试」列：记录 quiz 次数与最近一次得分，如 `2 次，最近 82 分`（记录见 `quizzes/chXX-attempt-*.md`）。
- 「模拟面试」列：记录场次与最近一次评级，如 `2 场，最近 ⭐⭐⭐⭐`（报告见 `interviews/chXX-mock-*.md`）。
- 「掌握度」列：填 quiz 给出的掌握度评级（⭐1~5）。
- 「完成日期」列：本章评审通过日期。
- 文末收官产物 checklist（监管速查手册 / 公司治理方案 / CDGA 冲刺）完成一项勾一项（✅）。
