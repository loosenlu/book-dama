---
name: dama-validator
description: 评审 DAMA-DMBOK 章节课件 slides（五维度课程评委制）。当编排流程（dama-workflow）要求评审某章课件，或课件修改后需要重验时使用。
---

# dama-validator：课程评审器

## 职责

以"课程评委"视角评审 slides——讲得对不对（忠实性）、主干全不全（主干完整性）、好不好学（教学质量）、监管映射准不准（监管映射准确性）。不再逐条点数覆盖率；允许甚至鼓励 teacher 舍弃边缘内容，评审的是"主要思想是否讲全、取舍是否合理"。评审为**硬门槛**：适用维度全部通过才能继续后续环节。

## 用户画像基线

用户是公有云数据库产品后端研发：数据库/存储/安全/网络/分布式等主题为老概念，数据治理为零基础新概念。评审"主干完整性"时须以该画像判断：老概念"略讲"是否真的会造成后端背景读者的理解断层。

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

## 输入

- `chapters/CHXX-*.md`（原书章节）
- `slides/chXX-slides.md`（被评审课件）
- `.tmp/teacher-chXX/outline.md`（教学大纲，含舍弃清单）
- `glossary.md`（全局术语表）

## 输出

`reports/chXX-review.md`（评审报告）。

## 评审维度

| 维度 | 内容 | 性质 |
|------|------|------|
| 忠实性 | slides 陈述是否准确，有无曲解/编造原书（抽查式对照原书关键段落，非逐条比对）；勘误标注核验 | 阻断 |
| 主干完整性 | 主要思想与论证主线是否讲全、有无理解断层；大纲舍弃清单是否合理（核心不许舍弃，边缘舍弃允许）；老概念"略讲"是否真的会造成后端背景读者的理解断层 | 阻断 |
| 教学质量 | 详略与新老概念匹配；结构与层次（列表/表格、无大段叙述）；表达风格（结论先行、无长句/元话语、数字进表格）；教学手法落实（问题场景、例子、每节 takeaway）；「落地视角」节是否可操作（不是空话）；节间衔接与连贯性 | 阻断 |
| 监管映射准确性 | 「金融行业映射」节的监管条款引用是否真实有效（FetchURL 抽查 2~3 条来源核实，限监管机构官网、权威媒体、官方文件）；条款时效性（是否已被新规替代）；**仅 🔥 核心档章节评审此维度** | 阻断（核心档）/ N/A（其他档） |
| 机械规范 | 编号层级（`1/1.1/1.1.1`，一~三级进标题、四级加粗列表项、禁止五级）、术语中英对照、图片文件存在、监管映射附来源、🎤 面试考点标注克制（≤5 处/章） | 阻断（修复快） |

## 执行流程（Subagent 分治）

四个 subagent 并行评审，各写独立临时文件（禁止并发写同一文件）：

1. 忠实性 subagent
2. 主干完整性 subagent
3. 教学质量 subagent
4. 监管映射准确性 + 机械规范 subagent

主 Agent 汇总生成评审报告。

### 监管来源抽查方法

对「金融行业映射」节：从中随机抽 2~3 条监管条款引用，用 FetchURL 打开 slides 所附来源链接逐条核实——链接可达、确为监管机构官网/官方文件/权威媒体、条款内容与 slides 陈述一致、条款仍现行有效（未被新规替代）。抽查发现问题即判定监管映射准确性维度不通过。需要联网时可在 shell 中调用 `proxy_on` 启用代理。

## 报告格式

`reports/chXX-review.md` 结构：

1. **总体结论**：✅ 通过 / ❌ 打回第 N 轮
2. **五维度分节**：每维度一节，写"通过"或问题列表（位置 | 问题 | 修改建议）
3. **修改意见清单**（打回时必填）：按优先级排列的可执行修改项，供 teacher 按单局部修改

## 判定规则（硬门槛）

- 适用维度全部通过 → 通过，可继续后续环节。
- 任一维度有问题 → 打回，将修改意见清单交回 dama-teacher 按单局部修改（非全文重写）后重验。
- 最多 2 轮打回；第 3 次仍不通过则停止循环，把评审报告呈现给用户人工裁决。
