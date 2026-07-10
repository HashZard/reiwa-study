---
name: site-scraper
description: 抓取 REIWA 培训网站的课程内容并保存为本地 markdown。当用户要求"抓取网站/下载课程/保存章节/导入培训内容"，或提到某章节内容缺失需要从培训平台获取时，必须使用本 skill。依赖 Playwright MCP。
---

# 培训网站抓取

## 前提
- 工具：Playwright MCP，连接用户**已登录**的浏览器
- 若未配置：提示用户运行
  `claude mcp add playwright npx @playwright/mcp@latest`
- 若发现未登录或登录失效：**立即停下通知用户手动登录，不要重试、
  不要尝试自动填写账号密码**

## 流程
1. 打开课程目录页，提取该章节全部小节链接，先输出链接清单让用户确认
2. 逐页访问，提取正文（去除导航栏、页眉页脚、侧边栏）
3. 转为 markdown，保留标题层级、列表、表格；图片记录 alt 文本和 URL
4. 保存到 `materials/chapter-XX/content/`
5. 试题页面保存到 `materials/chapter-XX/questions/`
6. 页面内的可下载附件（PDF/DOCX）下载到
   `materials/chapter-XX/resources/`
7. 完成后更新 `materials/index.md`（每个文件一句话摘要）
   和 `materials/manifest.md`

## 命名规范
`{章节号两位数}-{小节序号两位数}-{标题slug}.md`
例：`03-02-trust-account-requirements.md`

## 每个文件头部写入元数据
```markdown
---
source_url: <原页面URL>
chapter: 3
section: 3.2
title: <原标题>
scraped_at: <日期>
---
```

## 规则
- 每页间隔 3-5 秒，模拟正常浏览速度
- 遇到反复加载失败的页面：跳过并记入日志 `notes/scrape-log.md`，
  最后汇总报告，不要卡死在单页
- 只抓取用户账号可见的内容；结果仅存本地供个人学习
- 抓取结束输出统计：成功 X 页 / 跳过 X 页 / 下载附件 X 个
