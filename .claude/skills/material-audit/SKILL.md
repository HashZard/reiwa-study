---
name: material-audit
description: 模块资料盘点（pre-flight check）。在开始学习或答题某模块前，扫描该模块 Workbook 与 Assessment 中引用的全部资料，对照本地文件生成缺口清单，能自动下载的下载，其余交人工导入。当用户说"盘点资料/检查资料/开始新模块或新章节/资料是否齐全"或使用 /audit 命令时使用。
---

# 资料盘点流程

## 目标
确保某模块被引用的所有资料在本地齐全，之后答题才有可靠依据。
重点：**Assessment 基于对应 Workbook 作答**，故盘点时须同时确认
该模块的 Workbook 正文与 Assessment 题目都已抓取入库。

## 步骤
0. **先做一致性检查**：对照 manifest、index 与实际文件。✅ 路径不存在、文件为空、
   Assessment 只存在于 `assignments/`、或文件有缺页/截断迹象，一律降为未完成并记录原因
1. **通读**该模块 `workbooks/` 全部文件 **和 `assessments/` 中的全部题目**
   （Assessment 里提到但 Workbook 没展开的资料最容易缺失、最影响答题，必须扫）
2. **提取**所有被引用的资料，识别特征：
   - "refer to..." / "as outlined in..." / "see [文档名]" / "download..."
   - 法规名称和条款号（Act / Regulations / Code of Conduct）
   - REIWA 表格与合同模板（如 Joint Form of General Conditions）
   - 政府指南（DEMIRS / Consumer Protection 出版物）
   - 课程附件（handout, appendix, case study, worksheet）
3. **对照** `materials/manifest.md` 检查本地是否已有
4. **分类处理**缺失项：
   - 公开资源（法规、政府指南）→ 搜索并下载官方版本：
     法规必须来自 legislation.wa.gov.au 当前版本，
     存 `legislation/`；指南存该模块 `resources/`；记录来源 URL
   - 培训平台内的 Workbook/Assessment/附件 → 使用当前可用的已登录浏览器工具，
     按 site-scraper 抓取；无法访问时列入人工清单
   - 无法获取的 → 列入人工清单
5. **输出报告**并更新 `manifest.md`
6. 对法规记录官方 URL、compilation/as-at 日期和 `checked_at`；涉及题目中的金额、时限或
   current/latest 要求时，必须检查官方发布页是否有更新版本
7. AI 最后重新核对 manifest/index 与实际文件，修复 ✅ 路径不存在、空文件、
   题目来源缺失或抓取状态不一致

## 报告格式
```
### Module X 资料盘点报告
- Workbook 抓取：WB1 ✅ / WB2 ⬜ ...
- Assessment 抓取：A1 ✅ / A3 ⬜ ...
- 来源一致性：manifest/index/实际文件 ✅ 或列出差异
- ✅ 已有（N 项）：文件名 → 本地路径
- ⬇️ 已自动下载（N 项）：文件名 → 来源 URL → 保存路径
- 🙋 需人工导入（N 项）：文件名 + 哪个 Workbook/Assessment 提到 +
  猜测的获取位置（平台下载区 / 邮件附件 / 教师提供等）
```

## 规则
- **宁可多列不可漏列**；不确定是否被引用的也标注出来让用户判断
- 自动下载前先输出清单等用户确认，不要未经确认批量下载
- 用户人工导入文件后，重新运行本流程核对，直到模块状态为 ✅
- manifest.md 是唯一的状态权威来源，每次盘点后必须更新
- `assignments/` 中的题干或答案不计入 Assessment 抓取完成度；原题必须先落到
  `materials/module-XX/assessments/` 并带来源元数据
- “页面可以打开”不等于“抓取完整”；Workbook 应核对导航条目数、最终条目、表格、
  knowledge checks 和附件，Assessment 应核对所有 question/row/input prompt
