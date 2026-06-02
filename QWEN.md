# web-researcher 扩展

## 子智能体

`web-researcher` — 网络研究子智能体，处理搜索、学术检索、网页抓取任务。

**何时派发**：任务涉及网络搜索、论文查找、网页内容获取时，使用 `subagent_type: "web-researcher"` 派发。

**学术搜索**：当任务涉及论文、研究、学术、科学相关主题时，在派发 prompt 中明确告知 agent 使用 `academicsearch` 工具。

## 子智能体完成后

子智能体返回消息包含三部分，按顺序使用：

1. **Report saved** — 报告文件绝对路径，记下来后续引用用
2. **Summary + Quick Facts** — 直接用来回答用户，**不要读报告文件**
3. **grep 提示** — 底部有 `grep "FINDING-"` 等命令，需要更多细节时参考

**提取路径**：从返回消息中提取 `Report saved:` 后的路径，存为变量备用。如果用户后续追问细节，用这个路径做 grep + 分页 read_file。

**多轮研究**：每次研究都会生成独立报告文件，路径不同。引用时用 `grep_search` 搜索 `.qwen/research/` 目录即可定位。

> **路径规则**：`.qwen/research/` 目录位于**项目工作目录**下（即你当前的 `run_shell_command` 工作目录），不要写到 `~/.qwen/` 记忆/配置目录下。

## 消费研究报告

**默认**：用返回消息中的摘要回答，0 额外读取。

**需要细节时**：
1. `grep_search pattern="FINDING-" path="<报告路径>"` → 定位发现段落
2. `grep_search pattern="DATA-" path="<报告路径>"` → 定位结构化数据
3. `read_file path="<报告路径>" offset=<行号> limit=50` → 只读目标段落

**禁止**：`read_file` 读取完整报告文件。

## 命令

- `/research:search <query>` — 派发研究任务给 web-researcher 子智能体
- `/research:reports [keyword]` — 搜索/列出已有报告（只读 META 头），支持关键词过滤
- `/research:read <keyword>` — 按关键词搜索报告并分层读取，支持多文件匹配
