---
description: 按标准流程回答一道作业题（含依据检索与引用）
---

按 assignment-qa skill 回答以下作业题：

$ARGUMENTS

要求：
1. 先执行资料齐全性硬门槛：查 manifest/index、核对实际文件，并确认 Assessment 原题
   位于 `materials/module-XX/assessments/`；`assignments/` 中的题干不算来源材料
   - 未通过：只输出/保存 `BLOCKED` 报告，不生成实质答案或 Suggested Submission Version
   - 通过：记录 Assessment 路径、所需 Workbook 清单和法规版本
2. 答题全过程随时触发材料补充（assignment-qa 第 2.5 步）：
   - 发现材料缺失（引用了本地没有的资料 / 法规条款未收录 / Workbook 未入库）
     → 先停下补料，触发 material-audit（/audit），能自动下载的下载、平台内容按
       site-scraper 抓取，其余列人工清单，补齐并更新 manifest/index 后再继续；
       用户选择不补则标 `BLOCKED` 和「课程材料中未找到」，停止实质作答，不得推测
   - 出现新材料（用户给的截图/附件，或检索时发现值得入库的资料）
     → 按规范存入 materials/ 或 legislation/，**立即更新 manifest.md 与 index.md**，
       再基于新材料继续作答
3. 保留题目原文，并提供中文题意说明
4. 先建立要求矩阵（action verb、数量、字段、角色、jurisdiction/版本、限定词），再建立
   逐评分点证据矩阵；只有 `SUPPORTED` 或已说明的 `CONFLICT` 才能进入答案
5. 每个作答项都使用中英文对照：
   - Question / 题目要求
   - Answer in English / 英文答案草稿
   - 中文解析
   - 依据来源
6. 展示检索到的依据原文位置，再给答案草稿
7. 默认把完整回答保存到 `assignments/` 下对应的 Markdown 文件中，
   不需要用户另行要求；若已有对应文件则更新/追加，不新建重复文件
8. 引用格式遵循 citation skill；现行法规、金额和时限必须记录官方 URL、版本和 checked_at
9. 保存为 `DRAFT-UNVERIFIED` 后，AI 重新打开原题、Workbook 和法规执行独立逐条引用、
   数字/版本与题目覆盖核验；全部通过后才能晋级为 `VERIFIED-DRAFT`
10. 只有全部通过才标 `VERIFIED-DRAFT`；否则标 `BLOCKED` 或 `DRAFT-UNVERIFIED`，不得称为
    可提交草稿
11. 末尾提醒：这是草稿，需要我自己改写后提交，并告知保存路径和质量状态
