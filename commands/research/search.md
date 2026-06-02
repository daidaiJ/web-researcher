---
description: Dispatch a web research task to the web-researcher sub-agent
---

Dispatch a research task to the `web-researcher` sub-agent with this query: {{args}}

Use the Agent tool with `subagent_type: "web-researcher"` and pass the query as the prompt.

**学术查询提示**：如果 `{{args}}` 涉及论文、研究、学术、科学主题，在 prompt 中明确指示 agent 使用 `academicsearch` 工具搜索学术论文。

After the sub-agent returns, use the research result directly — the summary and key facts are inline. Do NOT read the report file unless the user specifically asks for more detail.

If the user asks for more detail, use `grep_search` on the report path to locate specific sections (`FINDING-`, `DATA-`, `CONFLICT-`), then `read_file` with `offset` and `limit` to read only that section.
