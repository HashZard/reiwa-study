---
name: citation
description: 引用格式与出处标注规范。任何时候需要引用课程材料、WA 法规、行为准则、REIWA 表格或政府指南来支持一个论点时，必须遵循本 skill。回答作业题、写笔记、核查答案时都会用到。
---

# 引用规范

## 格式
| 资料类型 | 格式 | 示例 |
|---------|------|------|
| 课程材料 | [Module X, "Workbook N — 标题", §编号] | [Module 8, "Workbook 1 — Legislation in Real Estate", §2.1] |
| 法规 Act | 法规全名 (WA) s 条款号 | Real Estate and Business Agents Act 1978 (WA) s 26 |
| 条例 Regulations | 全名 (WA) reg 编号 | Real Estate and Business Agents (General) Regulations 1979 (WA) reg 6 |
| 行为准则 | Code of Conduct for Agents and Sales Representatives 2016 (WA) cl 编号 | ... cl 12 |
| REIWA 表格/合同 | 表格全名 + 条款 | Joint Form of General Conditions, cl 3.1 |
| 政府指南 | 机构 + 标题 + 年份 | DEMIRS, *Real estate agents' guide*, 2024 |
| 网络来源 | 标题 + URL + 访问日期 | 仅用于补充背景 |

## 规则
1. 每条引用附**关键原句（少于 25 个英文词）** + 你自己的中文转述，
   说明这段原文为什么支持该论点
2. 每个论点至少一个出处；同时有课程材料和法规依据时两者都列
3. 引用课程材料时必须附本地文件路径，方便我核对，
   例：`materials/module-08-compliance/workbooks/08-wb1-legislation-in-real-estate.md`
4. **无法定位出处 → 明确写「未找到依据」**；不得推测条款号、
   不得从记忆中"回忆"条款内容
5. 法规引用前先核对 `legislation/` 中的官方版本；本地没有该法规时，
   先提示补齐（触发 material-audit），不要凭训练记忆引用
6. 课程材料与法规现行版本不一致时：指出差异，作业以课程材料为准，
   同时注明法规现状
7. 引用必须定位到可复查的位置：课程材料写 module/workbook/section + 本地路径；
   法规写完整名称、jurisdiction、section/subsection（或 regulation/rule）+ 本地路径
8. 引文只证明它实际表达的内容；引用前后至少阅读一个完整段落，核对主体、行为、
   条件、例外和后果，禁止用仅含相同关键词但语义不同的段落支持结论
9. `assignments/`、`notes/`、AI 摘要和搜索结果摘要不是事实来源，只可作为检索线索
10. 涉及现行法律、处罚、金额、时限或版本时，必须同时记录：
    - 本地法规的 compilation/as-at 日期
    - 官方发布页 URL
    - 本次核对日期 `checked_at: YYYY-MM-DD`
    若官方版本更新，先更新本地 Markdown/index/manifest，再引用
11. 法律后果必须精确标注性质与主体：offence / civil penalty / remedy / disciplinary
    action；individual / body corporate；maximum / fixed。不得把不同条款的处罚互相套用
12. 课程与现行法规冲突时按题目要求处理：题目明确要求 current/latest，现行法规优先；
    其他课程 Assessment 保留课程预期答案，并另列“现行法规核验”，不得静默选择其一

## 引用记录模板

```markdown
- Claim / 主张：
- Course evidence / 课程依据：<少于 25 词原句> — [Module..., §...]
  Local: `materials/...`
- Legal evidence / 法规依据：<少于 25 词原句> — Full Act name (WA/Cth) s ...
  Local: `legislation/...`
- Version: compilation/as-at ...; checked_at: YYYY-MM-DD; Official: https://...
- Support / 支持关系：说明原文如何支持该主张及适用条件
```
