# REIWA Study — AI 辅助学习项目

由 AI 直接执行的西澳 REIWA 房地产资格培训学习工作区，用于获取课程内容、
建立可检索资料库、检索法规和网络资料，并起草有依据的 Assessment 答案。

**课程**：Unrestricted Registration Online（CPP41419），aXcelerate 平台，
共 15 个 module；学习模块内分 **Workbook（Lesson）** 与 **Assessment（Summative）**，
Assessment 基于对应 Workbook 作答。

## 快速开始

把本目录交给能够读取本地文件的 AI 助手即可。AI 首先读取 `CLAUDE.md`，然后读取
`materials/manifest.md` 和 `materials/index.md`，无需先运行项目程序。

获取 aXcelerate 内容时，为 AI 提供一个可复用登录态的浏览器工具即可，例如 Browser、
Playwright 或其他网页插件。公开资料可以使用 Google/网络搜索；Google Drive 等文件插件
只在资料实际位于对应服务时使用。

可以直接用自然语言下达任务：

```text
进入 aXcelerate，获取 Module 8 的 Workbook 1–3 和 Assessment 3，保存并更新资料索引。
盘点 Module 8 还缺哪些课程附件、法规和政府指南。
根据本地 Workbook、法规和必要的网络资料回答 Assessment 3 Question 2。
重新核查这份答案的每个评分点、法规条款、金额和来源。
```

## 三个核心功能

1. **进入课程网站**：读取用户已登录账号可见的 Workbook、Assessment 和附件。
2. **建立资料库**：保存为结构化 Markdown，维护 manifest/index，方便 AI 快速检索。
3. **有依据地答题**：课程材料优先，结合法规；网络内容可以补充，但必须标明来源，
   并与课程材料的说法分开。

## 标准工作流

```
① 获取模块        AI 使用当前可用的已登录浏览器读取 Workbook / Assessment / 附件
② 格式化入库      保存到 materials/，立即更新 manifest.md 和 index.md
③ 盘点缺口        AI 对照页面、课程引用和实际文件补齐资料
④ 检索依据        Workbook → resources → 本地法规 → 必要的官方/网络资料
⑤ 回答作业        题目拆解 → 证据矩阵 → 简单英文草稿 + 中文解析 + 引用
⑥ 独立复核        AI 重新打开原题和来源，核查覆盖度、语义、数字和版本
```

答题采用四道准确性质量门：来源完整 → 评分点/证据矩阵 → 受证据约束的草稿 →
独立交付前核验。资料不全时只生成 `BLOCKED` 报告，不生成可提交答案。
新答案使用 `.claude/templates/assessment-answer.md`，确保质量状态与核验记录不会遗漏。

## 目录结构

```
├── CLAUDE.md / AGENTS.md   全局规则（角色、优先级、红线）
├── .claude/
│   ├── skills/             site-scraper / material-audit /
│   │                       assignment-qa / citation
│   ├── commands/           可选快捷指令；自然语言任务同样适用
│   └── agents/             researcher（检索子agent）
├── materials/              课程资料（manifest.md 为状态权威）
├── legislation/            WA 官方法规
├── assignments/            作业（每个一个子文件夹）
└── notes/                  模块总结与笔记
```

## 原则

- AI 负责：找依据、查法规、解释概念、检查引用、生成草稿
- skills 是 AI 的操作规范，不是必须运行的程序，也不绑定特定插件
- 我负责：理解内容、改写答案、最终提交
- 所有抓取内容仅存本地供个人学习使用
