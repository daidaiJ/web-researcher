---
description: Search and list research reports by keyword (optional). Shows META summary only.
---

Search research reports in `.qwen/research/` under the **project working directory** (NOT the memory/config directory).

> **路径规则**：所有 `.qwen/research/` 路径必须相对于项目工作目录（即 `git rev-parse --show-toplevel` 或当前 `run_shell_command` 的工作目录），不要写到 `~/.qwen/` 记忆目录下。

{{#if args}}
Keywords: `{{args}}`

**多关键词独立搜索**：将 `{{args}}` 按空格拆分为独立关键词，逐个搜索后合并去重。例如 `{{args}}` 为 "Kubernetes GPU" 时：

1. `grep_search` pattern="Kubernetes" path=".qwen/research/"
2. `grep_search` pattern="GPU" path=".qwen/research/"
3. 合并两次结果，去重，取并集

这样可以匹配到包含任意关键词的报告，而不是要求所有关键词按顺序出现。
{{else}}
Use glob to list all `*.md` files in `.qwen/research/`.
{{/if}}

For each matched report, use `read_file` with `offset=0` and `limit=15` to read only the META block + SUMMARY. Present as:

| # | Report | Date | Query | Sources |
|---|--------|------|-------|---------|

Do NOT read full reports. META block at the top has everything needed for listing.
