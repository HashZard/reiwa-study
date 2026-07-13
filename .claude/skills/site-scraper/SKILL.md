---
name: site-scraper
description: 抓取 REIWA 培训网站（aXcelerate）的课程内容并保存为本地 markdown。当用户要求"抓取网站/下载课程/保存模块或章节/导入培训内容/抓 Workbook 或 Assessment"，或提到某模块内容缺失需要从培训平台获取时，必须使用本 skill。依赖 Playwright MCP。
---

# 培训网站抓取

## 前提
- 工具：Playwright MCP，连接用户**已登录**的浏览器
- 培训平台入口：`https://reiwa.app.axcelerate.com/learner/`
  - 课程 Learning Plan 页：`.../course/class/31414/plan`
    （当前课程 CPP41419 Unrestricted Registration Online 的 classId = 31414）
  - 页面结构：15 个 module，学习模块下分 **Workbook（Lesson）** 与
    **Assessment（Summative）** 两类可点开的项目；锁定(Locked)模块需先完成前置

### 一次性配置（首次或 `claude mcp list` 显示未配置时）
1. **安装 MCP**（用户在终端运行，随后需重连会话）：
   ```bash
   claude mcp add playwright npx @playwright/mcp@latest -- \
     --user-data-dir ~/.claude/playwright-reiwa
   ```
   `--user-data-dir` 指定持久化 profile 目录：登录态（cookie）留在该目录，
   登录一次长期有效，无需每次重登。
2. **首次登录**：配置好后，我打开课程目录页；若是登录墙，**立即停下**，
   请用户在弹出的 Playwright 浏览器窗口中手动登录一次，登录成功后再继续。
   之后的会话直接复用该 profile，通常无需再登。

### 每次抓取前的检查
- 先跑 `claude mcp list` 确认 playwright 状态为已连接。
- 若显示 `✗ Failed to connect`：常见原因是 npx 缓存损坏
  （`npx @playwright/mcp@latest --version` 报 `ENOTEMPTY: rename ... playwright-core`）。
  处理：`rm -rf ~/.npm/_npx/<损坏的哈希目录>` 后重跑，再让用户 `/mcp` 重连或重启会话。
- 我自带的 WebFetch **无法**访问需登录页面，受保护内容一律走 Playwright MCP。
- 注意：默认 MCP 会启动**独立浏览器实例**，不共享日常 Chrome 的登录态，
  因此必须用上面的持久化 profile 方案，而不是假设「用户已在 Chrome 登录」。
- 实测（2026-07-11）：连接的服务是 `@playwright/mcp@latest`，其持久 profile 实际在
  `~/Library/Caches/ms-playwright-mcp/mcp-chrome-<hash>/`（默认路径，非 skill 早期设想的
  `~/.claude/playwright-reiwa`）。登录 cookie 存这里。

### 登录（关键经验）
- 若发现未登录或登录失效：**立即停下通知用户手动登录，不要重试、
  不要尝试自动填写账号密码**。
- **"Sign in with REIWA" (SSO) 可用**，实测登录后整个抓取会话 cookie 稳定不掉线。
- 登录 cookie 可能是**会话级**（存在浏览器内存、不落盘）；务必让用户勾选 **"Stay signed in"**，
  才会下发持久 cookie 写入 profile，下次免登。SSO 回来的会话未必受该勾选控制，
  但通常有 provider 层 cookie 可静默续登。
- 抓完**不要 `kill -9` 浏览器**；持久 cookie 一般已落盘，但硬杀有极端丢盘风险，尽量正常关闭。

### 故障：`Browser is already in use ... use --isolated`（本次踩坑，直接照做）
症状：`browser_navigate` 报此错，说明该 profile 被**上次会话残留的自动化 Chrome 进程**锁住。
**关浏览器窗口不够——必须杀掉那个进程**。步骤：
1. 看锁指向的 PID：`ls -l ~/Library/Caches/ms-playwright-mcp/mcp-chrome-*/SingletonLock`
   → 软链接尾部 `...-<PID>` 就是占锁进程。
2. 确认它是自动化 Chrome（`ps -p <PID> -o command` 含 `--remote-debugging-pipe --user-data-dir=...mcp-chrome`），
   **不是**用户日常 Chrome（日常 Chrome 用的是 Default profile，不含这些标志）。
3. 征得用户同意后 `kill <PID>`；确认 `SingletonLock` 消失后重试 `browser_navigate`。
   （登录态在 profile 目录里，换新窗口不丢登录。）

## 流程
1. 打开 Learning Plan 页，展开目标模块，列出其下全部 **Workbook / Assessment**
   项目及链接，先输出清单让用户确认要抓哪些
2. 逐项访问，提取正文（去除导航栏、页眉页脚、侧边栏）
3. 转为 markdown，保留标题层级、列表、表格；图片记录 alt 文本和 URL
4. Workbook（Lesson）正文保存到 `materials/module-XX/workbooks/`
5. Assessment（Summative）题目保存到 `materials/module-XX/assessments/`
6. 页面内的可下载附件（PDF/DOCX）下载到
   `materials/module-XX/resources/`
7. 完成后更新 `materials/index.md`（每个文件一句话摘要）
   和 `materials/manifest.md`（模块的 Workbook/Assessment 抓取状态）

### 抓 Lesson（Workbook）正文的实操方法（本次验证有效）
aXcelerate 的 Workbook 是「Lesson」，正文分成很多分页（含 Knowledge check）。
**已完成的 Lesson 用 `Results` 标签非破坏性抓取，不要点 "Take This Lesson Again"**
（那会开启新一次 attempt，动到平台状态）。
- URL：`.../mod/<modId>/assessment/<id>/results`。
- 左侧导航列出全部条目（Section 标题 / 子页 / Knowledge check）。折叠的 Section 点标题的
  第一个子元素展开：`li.firstElementChild.click()`（展开箭头是 CSS/SVG，不是 `<img>`，
  按 alt 找不到）。
- 用**一次 async `browser_evaluate` 循环**遍历全部条目：定位含 "Section 1." 的 `ul/ol` 为导航，
  过滤掉裸 Section 标题行，逐个 `li.click()`，轮询到 `document.querySelector('main').innerText`
  变化后收集——一次调用抓完所有分页，比逐条点侧边栏省事。用 `filename` 参数存文件避免超大返回。
- innerText **会丢链接和图片 URL**：需再跑一遍收集每页 `main` 内的
  `a[href]`（排除站内 `/mod/<modId>/` 导航）和 `img[src]`（排除 `data:`）。
- 页面大快照可能超 token 上限，会落到 tool-results 文件——用 `grep -n` 在该文件里定位模块条目，
  再用 ref 点击；或直接给 `browser_snapshot` 传 `filename`。
- 组装 markdown 时剥掉每页尾部的 `Previous / Item N` 导航文字和重复的标题行。

### 大文件（如完整法规）配套索引
抓/转出的大文件（如 ACL 全文 1 万余行）**同时生成 `<name>.index.md`**：
列 Chapter/Part/Division/Section → 行号，供 `Read offset` 精确跳转，避免整篇加载干扰上下文。
参见 `legislation/cth-australian-consumer-law.index.md`。

## 命名规范
- Workbook：`{模块号两位}-wb{序号}-{标题slug}.md`
  例：`08-wb1-legislation-in-real-estate.md`
- Assessment：`{模块号两位}-a{序号}-{标题slug}.md`
  例：`08-a3-professional-communication.md`

## 每个文件头部写入元数据
```markdown
---
source_url: <原页面URL>
module: 8
module_name: Compliance cluster
item_type: workbook   # 或 assessment
item_no: 1            # Workbook 1 / Assessment 3 等
competency: [CPPREP4002, CPPREP4003]   # 该模块能力单元代码，可选
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
