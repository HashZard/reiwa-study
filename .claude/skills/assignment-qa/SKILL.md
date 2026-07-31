---
name: assignment-qa
description: 回答 REIWA 培训作业题的标准分析流程。当用户提出作业题、试题、案例分析题，或使用 /answer 命令时，必须遵循本 skill。确保每个答案都有课程材料和法规出处。
---

# 作业问答流程

## 第 0 步：资料齐全性硬门槛（Gate 1）
先查 `materials/manifest.md` 和 `materials/index.md`，再核对实际文件。必须同时满足：

1. Assessment 原题已保存到对应模块的 `materials/module-XX/assessments/`
2. 对应 Assessment 所依据的全部 Workbook 正文已保存到 `workbooks/`
3. 题目明确引用的附件、表格或法规已入库
4. manifest 状态、本地路径和实际文件一致

`assignments/` 是 AI 输出目录。里面复制的题干、旧答案或免责声明不能替代正式
Assessment 原题，也不能作为证据。

任一条件不满足：
- **停止实质作答**，不得先生成答案再加“资料不完整”免责声明
- 输出缺失项和现有材料清单，质量状态写为 `BLOCKED`
- 触发 `/audit`；只允许保存 blocker report，不生成 Suggested Submission Version
- 补齐资料并更新 manifest/index 后，从第 0 步重新开始

## 第 1 步：拆解题目
判断题型：
- 概念定义题（考课程术语解释）
- 法规依据题（考具体条款）
- 流程步骤题（考操作程序，如信托账户处理流程）
- 情景判断题（给场景，判断合规/应对方式）
- 计算题（佣金、费用、信托账户金额）

标出题目的关键限定词（must / may / within X days / prior to 等），
这些往往是评分点。

先生成「要求矩阵」，至少包含：

| ID | 原题 action verb | 要回答的对象 | 数量/字段 | 角色/场景 | jurisdiction/版本 | 限定词 | 证据状态 |
|----|------------------|--------------|-----------|-----------|----------------------|--------|----------|

- `list / identify`、`explain / describe`、`compare`、`calculate` 要按不同深度回答
- 表格的每个输入框单独作为一个 ID；“two examples + section + penalty”至少拆成
  example 1/2、section 1/2、penalty 1/2，避免漏格或错配
- 有字数要求时记录上下限；没有字数要求时以覆盖评分点的最短清晰答案为准

## 第 2 步：检索
1. 查 `materials/index.md` 定位相关文件
2. **优先**读该模块 `workbooks/` 中的相关 Workbook**原文**
   （题目严格按本模块 Workbook 出，措辞相近段落大概率就是考点）
3. 查 `resources/` 中被引用的外部资料
4. 涉及法规 → 读 `legislation/` 中的条款原文
5. 旧作业只可提供关键词，禁止引用旧答案作为依据
6. 当前 AI 环境支持并行/子 agent 时，可把纯检索任务交给 researcher；否则由主 AI
   直接检索，流程和证据标准不变

### 证据矩阵（Gate 2）

起草前，为要求矩阵中的每个 ID 建立证据记录：

| ID | Workbook 原文与定位 | 法规原文与条款 | 二者关系/适用条件 | 状态 |
|----|---------------------|----------------|-------------------|------|

状态只能是：
- `SUPPORTED`：直接证据足以支持答案
- `PARTIAL`：只支持部分内容，需继续检索
- `CONFLICT`：课程材料与现行法规不同，必须双轨说明
- `UNSUPPORTED`：课程材料中未找到，不得作答或猜测

每条证据必须先看上下文，确认主体、行为、前提、例外和后果；关键词命中不等于支持。

## 第 2.5 步：检索中触发材料补充（贯穿答题全过程）
第 0 步是答题前的一次性检查；本步是**答题过程中随时触发**的补充机制。
一旦在检索或作答时遇到下面任一情况，**先处理材料、后继续答题**，不要在
不完整依据上硬答：

**A. 发现材料缺失**（题目/Workbook 引用了本地没有的资料，或某法规条款
本地 `legislation/` 未收录，或该模块 Workbook 正文其实没入库）：
- 停下当前作答，明确告诉用户缺什么、是哪道题/哪段引用暴露的
- 触发 `material-audit`（即 `/audit`）流程补齐；能自动下载的（法规、政府
  指南）按其规则下载，平台内容按 `site-scraper` 抓取，其余列人工清单
- 补齐并更新 `manifest.md` / `index.md` 后再回到本题继续
- 若用户选择先不补，将质量状态改为 `BLOCKED`，在研究记录中标
  「课程材料中未找到」；停止实质作答，不得推测填充

**B. 出现新材料**（用户在本轮提供了新的截图/附件/文档，或检索时发现了
一份值得入库、以后还会用到的资料）：
- 按 `site-scraper` / `material-audit` 的存储规范落地到对应位置
  （课程材料进 `materials/module-XX/`，法规进 `legislation/`）
- **立即更新 `manifest.md` 与 `index.md`**（状态 + 本地路径 + 一句话摘要），
  manifest 是唯一状态权威，不更新下次会重复劳动
- 然后基于新入库的材料继续作答

Assessment 截图或用户复制的原题属于来源材料：先保存到模块 `assessments/`（注明
`source_type`、来源 URL/截图文件及获取日期；平台抓取用 `axcelerate_authenticated`，
用户截图/导出用 `user_provided_screenshot` / `user_provided_export`），更新 manifest/index，
再作答；不能只把题干放进 `assignments/`。仅口头转述或不完整复制应标 `partial`。

判断原则：宁可暂停补料，也不要基于记忆或不完整依据作答（见红线）。

## 第 3 步：回答结构
默认使用「Assessment 双语解析格式」。除非用户明确要求只给简短答案，
否则所有 assessment / assignment 答案都按以下结构输出。
新文件从 `.claude/templates/assessment-answer.md` 创建，避免遗漏质量状态和核验记录。

### 3.1 总体结构
```
## Question / 题目

### English
（保留题目英文原文；如题目很长，可保留关键题干和字段）

### 中文题意
（用中文说明题目要回答什么、角色/场景是什么、评分点是什么）

## Evidence status / 依据状态
- Quality status: VERIFIED-DRAFT
- Assessment source: （本地路径）
- Required Workbooks: （逐项列路径与状态）
- Legislation checked_at: YYYY-MM-DD（如适用）
- Requirement coverage: X/X

## Answer / 作答

### 1. （按题目字段、表格行或评分点命名）

**Question / 题目要求**
（保留该字段/小题的英文要求 + 中文解释）

**Answer in English / 英文答案草稿**
（正式、简洁、可填入 assessment 的英文答案）

**中文解析**
（解释为什么这样答；指出题目考点、易错点、与场景的关系）

**依据来源**
（课程材料出处 + 法规条款 + 本地文件路径/官方链接；格式遵循 citation skill）
```

「要求矩阵」和「证据矩阵」属于核验内容，默认保存在答案文件的
`Quality record / 质量记录` 小节中，不放进用户最终粘贴的英文答案框。

### 3.2 表格类 Assessment
若题目截图或原题是表格输入框（例如每行一个问题、每格填一段话）：
- 按表格行逐项作答，保留每个 row label / prompt
- 英文答案要短到适合粘贴进输入框
- 中文解析和依据放在每个答案下面，不放进正式提交框
- 多个输入框（如 breach 1 / breach 2、penalty 1 / penalty 2）要逐项编号，保持与平台输入框顺序一致

### 3.3 答案内容要求
- **英文草稿风格（只约束英文答案，不约束中文解析）**：使用简单书面英语，
  贴近用户本人水平（雅思 6–6.5），目标是用户能直接读懂并改写、提交版不显得不自然：
  - 用短句，一句话讲一个要点，避免从句套从句
  - 用常见词，避免生僻同义词和过度正式的措辞
  - 保留必要的专业术语和法规原文措辞（must / within X days / 具体条款等），
    这些是评分点，**不简化**
  - 优先保证覆盖评分要点，其次才是语言的简单自然
  - 不在英文草稿后附中文小注（理解上的解释放在「中文解析」部分）
- 与用户交流、解释依据和推理时使用中文（中文解析不受上面简化要求约束，可正常展开）
- 每个回答项下面必须有中文解析和依据来源
- 找不到课程材料依据时必须写「课程材料中未找到」
- 法规条款号、罚款金额、时限等不得推测，必须来自本地 `legislation/` 或官方网页
- 课程材料与现行法规不一致时，分别说明「课程材料说法」和「现行法规说法」
- 涉及现行法规时记录法规 compilation/as-at 日期、官方 URL 和本次 `checked_at` 日期
- 处罚必须说明适用主体和性质；不得混淆个人/法人、最高处罚/固定处罚、offence/
  civil penalty/disciplinary action，也不得自行换算 penalty units

## 第 4 步：独立核验（Gate 3–4）

完成初稿后、向用户交付前，必须重新打开来源执行一次独立复核，不可只检查自己刚才
摘录的证据矩阵：

1. **主张核验**：逐条确认引文真实存在，且支持答案的主体、行为、条件、例外和结论
2. **数字核验**：条款号、金额、时限、数量、日期逐字符回查官方原文
3. **概念核验**：Act / Regulations / Code，law / best practice / ethics，offence / remedy /
   discipline 不得互换
4. **覆盖核验**：逐项对照要求矩阵，确认字段顺序、数量要求、action verb 和角色场景
5. **版本核验**：题目要求 current/latest 或答案含法律数字时，核对官方发布页；本地旧版
   先更新，不得只在答案备注“官网较新”
6. **来源回查**：初稿保持 `DRAFT-UNVERIFIED`，AI 不依赖先前摘录，重新打开原题、
   Workbook 和法规，逐项填写独立核验记录
7. **状态晋级**：要求矩阵、证据矩阵、数字/版本和全部引用均复核通过后改为
   `VERIFIED-DRAFT`；发现任何遗漏立即降回 `DRAFT-UNVERIFIED` 或 `BLOCKED`

只有要求矩阵全部 `SUPPORTED`（或已清楚处理 `CONFLICT`）、独立来源回查无遗漏时，
才能把质量状态设为 `VERIFIED-DRAFT`。其他状态：

- `BLOCKED`：来源/Workbook/Assessment/法规缺失，不得给实质答案
- `DRAFT-UNVERIFIED`：资料齐全但尚未完成独立核验，不得称为可提交草稿

## 第 5 步：默认保存到 assignments
每次回答 assessment / assignment 题目时，默认把完整回答同步保存到
`assignments/` 下对应的 Markdown 文件中，不需要用户单独要求。

保存规则：
- 若用户给了题目文件路径：优先更新该路径对应的答案文件，或在同目录创建清晰命名的答案文件
- 若用户只给截图或文字题目：根据模块、cluster、assessment 编号和 question 编号推断路径
- 推荐路径格式：
  `assignments/module-XX-{cluster}-assessment-Y/question-Z-{short-slug}.md`
- 若对应目录不存在，先创建目录
- 若已有同一 question 文件，更新该文件；不要为同一题重复新建多个版本
- 文件内容必须包含完整「Assessment 双语解析格式」：题目、英文答案、中文解析、依据来源、备注
- 文件必须包含 `Evidence status` 和 `Quality record`；状态不可省略
- 保存后，在最终回复中简要说明保存路径

如果题目信息不足以可靠判断模块或 assessment 编号：
- 先使用当前对话中最近的 assessment 上下文推断
- 仍无法推断时，保存到 `assignments/unsorted/`，文件名包含 question 编号和简短 slug
- 在最终回复中说明这是临时路径

## 第 6 步：输出提醒
答案末尾固定加一句：
「以上为草稿和依据整理，请自行理解、改写后提交。」
同时告知：已保存到哪个本地文件。

## 规则
- 每个论点必须有出处；找不到就写「未找到依据」
- 优先引用课程材料——题目严格按培训内容出题，
  课程原文中与题干措辞相近的段落大概率就是出题点
- 情景判断题：先列适用规则，再套用到场景，不要直接跳到结论
- 计算题：列出公式来源（哪个小节），逐步计算，标注单位
- 不能因为用户需要尽快提交而跳过质量门；准确性状态必须如实标记
