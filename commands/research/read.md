---
description: Read one or more research reports by keyword. Shows summary first, sections on demand.
---

Read research reports matching `{{args}}` from `.qwen/research/` under the **project working directory**.

> **路径规则**：所有 `.qwen/research/` 路径必须相对于项目工作目录（即 `git rev-parse --show-toplevel` 或当前 `run_shell_command` 的工作目录），不要写到 `~/.qwen/` 记忆目录下。

**Step 1 — Locate matching files:**
将 `{{args}}` 按空格拆分为独立关键词，逐个搜索后合并去重：

1. 对每个关键词，用 `grep_search` 搜索 `.qwen/research/` 目录下的文件内容
2. 同时用 glob `*{keyword}*.md` 匹配文件名
3. 合并所有结果并去重

这样可以匹配到包含任意关键词的报告，而不是要求所有关键词按顺序出现。

If no matches found, tell the user and suggest `/research:reports` to list all available reports.

**Step 2 — Show summaries (lines 0-20 of each):**
For each matched report, use `read_file` with `offset=0` and `limit=20`. Present all summaries in a table:

| # | Report | Summary (first 3 lines) |
|---|--------|------------------------|

**Step 3 — Ask what to drill into:**
Present the summaries and ask: "以上是匹配的报告摘要，需要查看哪份报告的具体发现或数据？"

**Step 4 — Drill into selected report:**
Use `grep_search` with pattern `FINDING-` or `DATA-` or `CONFLICT-` on the selected report to locate sections, then `read_file` with `offset` and `limit` to read only the requested section.

Never read full files unless explicitly asked.
