---
description: 按标准流程回答一道作业题（含依据检索与引用）
---

按 assignment-qa skill 回答以下作业题：

$ARGUMENTS

要求：
1. 先执行资料齐全性检查（查 manifest.md）
2. 答题全过程随时触发材料补充（assignment-qa 第 2.5 步）：
   - 发现材料缺失（引用了本地没有的资料 / 法规条款未收录 / Workbook 未入库）
     → 先停下补料，触发 material-audit（/audit），能自动下载的下载、平台内容按
       site-scraper 抓取，其余列人工清单，补齐并更新 manifest/index 后再继续；
       用户选择不补则该处标「课程材料中未找到」，不得推测
   - 出现新材料（用户给的截图/附件，或检索时发现值得入库的资料）
     → 按规范存入 materials/ 或 legislation/，**立即更新 manifest.md 与 index.md**，
       再基于新材料继续作答
3. 保留题目原文，并提供中文题意说明
3. 按题目结构、表格字段或评分点合理拆分
4. 每个作答项都使用中英文对照：
   - Question / 题目要求
   - Answer in English / 英文答案草稿
   - 中文解析
   - 依据来源
5. 展示检索到的依据原文位置，再给答案草稿
6. 默认把完整回答保存到 `assignments/` 下对应的 Markdown 文件中，
   不需要用户另行要求；若已有对应文件则更新/追加，不新建重复文件
7. 引用格式遵循 citation skill
8. 末尾提醒：这是草稿，需要我自己改写后提交，并告知保存路径
