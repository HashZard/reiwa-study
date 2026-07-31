---
description: 盘点某模块引用的资料，生成缺口清单并下载/提示人工导入
---

对以下模块执行 material-audit skill：

$ARGUMENTS

要求：
1. 先核对 manifest/index/实际文件，再同时扫描该模块 workbooks/ 和 assessments/
   （assignments/ 中复制的题干不计为 Assessment 已抓取）
2. 先输出盘点报告，等我确认后再开始自动下载
3. 下载完成后更新 materials/manifest.md
4. 明确列出剩余需要我人工导入的文件清单及可能的获取位置
5. 核对抓取完整度（页面/题目总数、首末条目、表格、输入框、附件）；partial 不得标 ✅
6. 涉及法规时记录官方版本、URL 和 checked_at
7. 最后由 AI 重新核对 manifest/index 与实际文件，并把不一致纳入报告
