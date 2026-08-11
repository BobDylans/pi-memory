## Nightingale architecture（oncall-system 的设计参考）

Go 实现的 AI agent 框架，位于 `/home/ivan/Projects/nightingale` aiagent/ 包。

**核心设计**：Skills keyed by SKILL.md frontmatter `name`（非目录名）。SkillRegistry 三级缓存（L1 metadata in memory / L2 content LRU / L3 tools per-skill）。Tool assembly pipeline in Agent.Run(): cfg.Tools → appendSkillTools → appendMCPTools (deduped by name)。ReAct loop via StreamChan streaming events (token/tool_call/tool_result/error/done)。Session isolation via RunContext per Run()。Pipeline: Skills → Tools → MCP。Plan-ReAct mode: first pass plans structured steps, second executes via ReAct。

**并发模式**：goroutine-based streaming, sync.Map for client cache (per config fingerprint), atomic.Int64 for lock-free TTL, http.Client.Transport-level timeout。

oncall-system 的 AI Module 是该设计的 Java 移植参考。详细状态见 oncall-system project MEMORY.md。 <!-- created=2026-06-03, last=2026-08-09 -->