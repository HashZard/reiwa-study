---
name: material-audit
description: 模块资料盘点（pre-flight check）。在开始学习或答题某模块前，扫描该模块 Workbook 与 Assessment 中引用的全部资料，对照本地文件生成缺口清单，能自动下载的下载，其余交人工导入。当用户说"盘点资料/检查资料/开始新模块或新章节/资料是否齐全"或使用 /audit 命令时使用。
---

# 资料盘点流程

## 目标
确保目标 Assessment 被引用的资料在本地齐全，之后答题才有可靠依据。
重点：**Assessment 默认与同编号 Workbook 配对**（WB1 → A1、WB2 → A2）。盘点某一道
Assessment 时，先确认该题原题及同编号 Workbook；不要因同模块其他 Workbook 尚未抓取而
自动判定该题缺料。

只有在原题/Workbook 明确交叉引用其他 Workbook、附件、法规或表格，或同编号 Workbook
对一个评分点没有直接依据时，才把额外资料列为该题必需来源。用户明确要求“整模块盘点”时，
再按所有 Workbook–Assessment 配对完整扫描。

## 法规即时触发

不要等到整个 Module、全部 Workbook 或 Assessment 原题齐全才处理法规。只要已读取的
任一课程来源明确触发某部缺失法规，即刻补充该**单项**法规；这只补足法律来源，不改变
Assessment 的其他资料完整性门槛。

触发条件（任一满足即可）：

- 直接出现 Act / Regulations / Code 名称或条款号；
- 将具体义务、禁止、处罚、金额、时限或表格要求归因于可识别的法规；
- 明确写有 `refer to`、`under`、`as required by` 或下载该法规的指示；
- Assessment 明确要求识别、解释、适用或引用该法规。

不触发：仅出现 “legal”、“compliant”、“ethical” 等泛称而无法从课程来源识别法规；
此时记录为待确认，不得凭记忆或常识猜测法规名称或条款。

## 步骤
0. **先做一致性检查**：对照 manifest、index 与实际文件。✅ 路径不存在、文件为空、
   Assessment 只存在于 `assignments/`、或文件有缺页/截断迹象，一律降为未完成并记录原因
1. **按目标范围通读**：盘点单个 Assessment 时，先读该 Assessment 原题及同编号 Workbook；
   盘点整模块时，读全部 Workbook 与 Assessment。Assessment 里明确提到但同编号 Workbook
   未展开的资料最容易缺失，必须扫
2. **提取**所有被引用的资料，识别特征：
   - "refer to..." / "as outlined in..." / "see [文档名]" / "download..."
   - 法规名称和条款号（Act / Regulations / Code of Conduct）
   - REIWA 表格与合同模板（如 Joint Form of General Conditions）
   - 政府指南（DEMIRS / Consumer Protection 出版物）
   - 课程附件（handout, appendix, case study, worksheet）
3. **对照** `materials/manifest.md` 检查本地是否已有
4. **即时处理已触发的缺失法规**：每发现一项就单独执行，不等待其他章节：
   - 记录触发原文、Workbook/Assessment 路径或页面、法规全名与需要核对的主题；
   - 从对应官方立法网站取得当前官方版本，保存到 `legislation/`，保留原始文件并转换为
     Markdown；大文件同时建立定位 index；
   - 记录官方 URL、compilation/as-at 日期和 `checked_at`，立即更新 manifest/index；
   - 只补有明确触发证据的单项法规，不进行“可能相关法规”的批量下载。
5. **分类处理其余缺失项**：
   - 公开资源（法规、政府指南）→ 搜索并下载官方版本：
     法规必须来自其 jurisdiction 对应的官方立法网站当前版本（WA 通常为
     legislation.wa.gov.au；联邦通常为 legislation.gov.au），
     存 `legislation/`；指南存该模块 `resources/`；记录来源 URL
   - 培训平台内的 Workbook/Assessment/附件 → 使用当前可用的已登录浏览器工具，
     按 site-scraper 抓取；无法访问时列入人工清单
   - 无法获取的 → 列入人工清单
6. **输出报告**并更新 `manifest.md`
7. 对法规记录官方 URL、compilation/as-at 日期和 `checked_at`；涉及题目中的金额、时限或
   current/latest 要求时，必须检查官方发布页是否有更新版本
8. AI 最后重新核对 manifest/index 与实际文件，修复 ✅ 路径不存在、空文件、
   题目来源缺失或抓取状态不一致

## 报告格式
```
### Module X 资料盘点报告
- Workbook 抓取：WB1 ✅ / WB2 ⬜ ...
- Assessment 抓取：A1 ✅ / A3 ⬜ ...
- 来源一致性：manifest/index/实际文件 ✅ 或列出差异
- ✅ 已有（N 项）：文件名 → 本地路径
- ⬇️ 已自动下载（N 项）：文件名 → 来源 URL → 保存路径
- ⚖️ 即时补充法规（N 项）：触发原文/位置 → 官方 URL → 保存路径 → version / checked_at
- 🙋 需人工导入（N 项）：文件名 + 哪个 Workbook/Assessment 提到 +
  猜测的获取位置（平台下载区 / 邮件附件 / 教师提供等）
```

## 规则
- **宁可多列不可漏列**；不确定是否被引用的也标注出来让用户判断
- 对已明确触发的单项法规，立即下载和入库；不等待完整模块，也不需要把它与其他候选法规
  打包确认。对未明确触发的候选法规，先列清单等用户确认，禁止批量下载。
- 用户人工导入文件后，重新运行本流程核对，直到模块状态为 ✅
- manifest.md 是唯一的状态权威来源，每次盘点后必须更新
- `assignments/` 中的题干或答案不计入 Assessment 抓取完成度；原题必须先落到
  `materials/module-XX/assessments/` 并带来源元数据
- “页面可以打开”不等于“抓取完整”；Workbook 应核对导航条目数、最终条目、表格、
  knowledge checks 和附件，Assessment 应核对所有 question/row/input prompt
