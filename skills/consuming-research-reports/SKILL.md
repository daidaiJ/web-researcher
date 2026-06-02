---
name: consuming-research-reports
description: Use when you have received or need to read a research report from the web-researcher agent, to avoid loading the full file into context.
---

# Consuming Research Reports

Research reports are written to `.qwen/research/` (project working directory, NOT `~/.qwen/` memory directory) by the `web-researcher` sub-agent (type: `web-researcher`). They have structured headers designed for incremental reading. **Never read the full report unless absolutely necessary.**

> **路径规则**：所有 `.qwen/research/` 路径必须相对于项目工作目录（即你的 `run_shell_command` 默认工作目录），不要写到 `~/.qwen/` 记忆/配置目录下。

## Consumption Order

```
1. Return message       ← summary + quick facts are already here, use them
2. Report header        ← grep "META:" to get metadata, or read_file lines 0-15
3. Specific section     ← grep "FINDING-" / "DATA-" / "CONFLICT-" to locate, then read_file with offset/limit
4. Source link          ← report may link to sources/xxx.md for verbatim content, read_file on demand
5. Full file            ← last resort only
```

## After Dispatching web-researcher

The sub-agent's return message contains the full summary and quick facts inline. In most cases you can answer the user directly from this — **do not read the report file at all**.

## When You Need Deeper Detail

```bash
# Step 1: Find what sections exist
grep_search pattern="^(FINDING-|DATA-|CONFLICT-)" path="<report_path>"

# Step 2: Read only that section
read_file path="<report_path>" offset=<line> limit=50
```

## Following Source Links

Reports may contain markdown links to original content saved as separate files:

```markdown
> "API definition text" — [source: title](sources/k8s-gpu_1.md)
```

These are relative to the report directory (`.qwen/research/`). To read the original:

```
read_file path=".qwen/research/sources/k8s-gpu_1.md"
```

Only follow links when the report excerpt is insufficient (e.g., need full API spec, complete config, exact error message).

## Referencing in Your Response

When citing research findings to the user, use this pattern:

> According to [source title], {finding}. (Source: URL)

Do not dump raw report content into your response. Synthesize.
