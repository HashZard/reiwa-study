---
name: site-scraper
description: 进入 REIWA aXcelerate 获取课程目录、Workbook、Assessment 和附件，并保存为可检索的本地 Markdown。使用当前 AI 可用且能复用用户登录态的浏览器工具，不绑定特定插件。
---

# 培训网站获取与入库

## 目标

让 AI 自主完成：进入课程网站 → 找到目标 Module → 获取 Workbook/Assessment/附件 →
格式化入库 → 更新 manifest/index。工具只是实现手段，最终结果和完整性记录才是标准。

## 工具选择

按当前环境选择最合适的方式：

1. **已连接且有登录态的浏览器插件**：优先，例如 in-app Browser
2. **浏览器自动化工具**：例如 Playwright MCP，可复用持久化 profile 时使用
3. **Google/网络搜索**：只用于寻找公开页面、官方附件或背景资料；不能绕过登录读取
   aXcelerate 受保护内容
4. **用户提供的截图或导出文件**：浏览器不可用时可以入库，但必须标明来源和完整度

如果页面出现登录墙，立即让用户在浏览器中手动登录。不得索要、保存或自动填写账号密码。
如果当前工具不能访问登录后的页面，换用其他可复用登录态的浏览器工具；确实无可用工具时，
明确列出需要用户导出的内容。

## 平台事实

- Learning Plan：`https://reiwa.app.axcelerate.com/learner/course/class/31414/plan`
- 课程：CPP41419 Unrestricted Registration Online，共 15 个 Module
- Workbook 在平台中显示为 `Lesson`
- Assessment 在平台中显示为 `Summative Assessment`
- 锁定模块不得尝试绕过；只读取用户账号当前有权访问的内容

## 标准流程

### 1. 定位并确认范围

- 打开 Learning Plan，读取目标 Module 下所有 Workbook、Assessment 和状态
- 先查 `materials/manifest.md`：已有 ✅ 内容不重复抓取
- 把准备获取的项目名称、类型、编号和链接列成简短清单；用户已明确范围时直接执行

### 2. 非破坏读取

- 已完成的 Lesson 优先进入 `Results` 视图，不点击 `Take This Lesson Again`，避免新建 attempt
- 按页面导航逐项读取正文，包括 section、子页、knowledge check、表格、链接和图片说明
- Assessment 必须保留 instruction、question、row label、input prompt、数量要求和附件
- 每页操作保持正常速度；反复失败的页面记录到 `notes/scrape-log.md` 后继续其他项目

### 3. 格式化与保存

- Workbook → `materials/module-XX/workbooks/`
- Assessment → `materials/module-XX/assessments/`
- 附件和外部资料 → `materials/module-XX/resources/`；Module 4 Phantom Realty 例外使用 `materials/module-04-phantom-realty/attachments/raw/`（原始文件）和 `attachments/md/`（转换文本），由 source-aligned 章节索引。
- 保留标题层级、列表、表格、关键链接、图片 alt/说明；去除导航、页眉页脚和重复按钮文字
- 大文件同时创建 `<name>.index.md`，记录章节/条款到行号的定位

命名：

- Workbook：`{模块号2位}-wb{序号}-{标题slug}.md`
- Assessment：`{模块号2位}-a{序号}-{标题slug}.md`

### 4. 来源元数据

每个 Workbook/Assessment 文件开头写：

```yaml
---
source_url: <原页面URL或用户附件说明>
source_type: axcelerate_authenticated  # 或 user_provided_screenshot / user_provided_export
module: 8
module_name: Compliance cluster
item_type: workbook                    # 或 assessment
item_no: 3
competency: [CPPREP4002, CPPREP4003]
title: <原标题>
scraped_at: YYYY-MM-DD
capture_status: complete               # complete / partial
captured_items: <实际获取条目数>
expected_items: <页面导航或题目总数>
---
```

仅有口头转述、不完整复制、缺页截图或无法确认总数时，必须使用 `capture_status: partial`。

### 5. AI 完整性复核

保存后由 AI 重新打开本地文件，与页面结构逐项对照：

- Workbook：首条/末条、导航总数、section、表格、knowledge checks、链接和附件
- Assessment：question 总数、所有小题、row/input prompt、instruction 和附件
- 本地文件：非空、无工具错误文本、元数据齐全、Markdown 可搜索

`captured_items` 与 `expected_items` 不一致，或任何内容无法确认时标 `partial`，不得标 ✅。

### 6. 更新项目索引

获取任何新资料后立即更新：

- `materials/manifest.md`：状态、路径、来源和缺失项
- `materials/index.md`：每个文件的一句话内容摘要

完成报告包括：成功项目数、partial/跳过项目数、附件数、剩余缺口和本地路径。

## Playwright 兼容提示（仅在实际使用时）

- Playwright 独立 profile 不一定共享日常 Chrome 登录态；让用户在其浏览器窗口手动登录
- `Browser is already in use` 通常表示自动化 profile 被旧进程占锁；确认目标是自动化浏览器
  后再征得用户同意处理，不能误关用户日常浏览器
- aXcelerate 的已完成 Lesson 可从 `/results` 页面遍历左侧导航；正文抽取后仍需单独保留
  `a[href]` 和 `img[src]`
- 工具返回超大页面时分 section 保存或使用快照文件，不要因上下文截断把不完整内容标为 complete

## 红线

- 只读取用户账号有权访问的内容，仅存本地供个人学习
- 不绕过登录、权限或 Locked 状态
- 不自动填写或保存用户凭据
- 不重复抓取 manifest 已为 ✅ 的资料
- 不把搜索摘要、旧作业答案或 AI 记忆当作课程原文
