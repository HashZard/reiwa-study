---
name: material-audit
description: 章节资料盘点（pre-flight check）。在开始学习或答题某章节前，扫描该章培训内容和试题中引用的全部资料，对照本地文件生成缺口清单，能自动下载的下载，其余交人工导入。当用户说"盘点资料/检查资料/开始新章节/资料是否齐全"或使用 /audit 命令时使用。
---

# 资料盘点流程

## 目标
确保某章节被引用的所有资料在本地齐全，之后答题才有可靠依据。

## 步骤
1. **通读**该章 `content/` 全部文件 **和 `questions/` 中的全部试题**
   （试题里提到但正文没展开的资料最容易缺失、最影响答题，必须扫）
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
     存 `legislation/`；指南存该章 `resources/`；记录来源 URL
   - 培训平台内的附件 → Playwright 可达则按 site-scraper 抓取，
     否则列入人工清单
   - 无法获取的 → 列入人工清单
5. **输出报告**并更新 `manifest.md`

## 报告格式
```
### Chapter X 资料盘点报告
- ✅ 已有（N 项）：文件名 → 本地路径
- ⬇️ 已自动下载（N 项）：文件名 → 来源 URL → 保存路径
- 🙋 需人工导入（N 项）：文件名 + 课程哪一页/哪道题提到 +
  猜测的获取位置（平台下载区 / 邮件附件 / 教师提供等）
```

## 规则
- **宁可多列不可漏列**；不确定是否被引用的也标注出来让用户判断
- 自动下载前先输出清单等用户确认，不要未经确认批量下载
- 用户人工导入文件后，重新运行本流程核对，直到章节状态为 ✅
- manifest.md 是唯一的状态权威来源，每次盘点后必须更新
