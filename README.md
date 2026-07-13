# REIWA Study — AI 辅助学习项目

用 Claude Code / Codex 辅助完成西澳 REIWA 房地产资格培训的
资料管理、依据检索与作业分析。

**课程**：Unrestricted Registration Online（CPP41419），aXcelerate 平台，
共 15 个 module；学习模块内分 **Workbook（Lesson）** 与 **Assessment（Summative）**，
Assessment 基于对应 Workbook 作答。

## 快速开始

```bash
# 1. 安装 Claude Code（如未安装）并在本目录启动
cd reiwa-study
claude

# 2. 配置 Playwright MCP（用于抓取培训网站，持久化登录态）
claude mcp add playwright npx @playwright/mcp@latest -- \
  --user-data-dir ~/.claude/playwright-reiwa
# 若 mcp list 显示 Failed to connect 且报 ENOTEMPTY：
#   rm -rf ~/.npm/_npx/<损坏哈希目录> 后重连（详见 site-scraper skill）
```

## 标准工作流

```
① 抓取模块        「按 site-scraper skill 抓取 Module 8 的 Workbook 1-3 和 Assessment 3」
② 盘点资料        /audit Module 8
③ 补齐缺口        自动下载 + 按清单人工导入 → 再次 /audit 直到 ✅
④ 学习总结        /summarize Module 8
⑤ 回答作业        /answer <Assessment 题目或题目文件路径>
⑥ 核查引用        /verify assignments/assignment-01/answer-draft.md
```

## 目录结构

```
├── CLAUDE.md / AGENTS.md   全局规则（角色、优先级、红线）
├── .claude/
│   ├── skills/             site-scraper / material-audit /
│   │                       assignment-qa / citation
│   ├── commands/           /answer /audit /verify /summarize
│   └── agents/             researcher（检索子agent）
├── materials/              课程资料（manifest.md 为状态权威）
├── legislation/            WA 官方法规
├── assignments/            作业（每个一个子文件夹）
└── notes/                  模块总结与笔记
```

## 原则

- AI 负责：找依据、查法规、解释概念、检查引用、生成草稿
- 我负责：理解内容、改写答案、最终提交
- 所有抓取内容仅存本地供个人学习使用
