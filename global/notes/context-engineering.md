---
description: "上下文工程方法论与个人实践策略"
tags:
  - "context-engineering"
  - "methodology"
  - "memory"
  - "best-practices"
created: "2026-08-11"
updated: "2026-08-11"
---

# 上下文工程方法论

## 四大策略（Anthropic 官方）
1. **Write（写入外部）**：scratchpad、git checkpoint、progress 文件
2. **Select（智能选择）**：just-in-time 加载、.claudeignore、按需加载工具
3. **Compress（压缩）**：compaction（可逆 vs 有损，逐级激进）
4. **Isolate（隔离）**：subagent 独立上下文窗口，主 agent 只收结果

## 核心认知
- 上下文是瓶颈，不是模型
- ~100万 token 后性能显著下降（"1M token wall"）
- 更多上下文 = 更差表现，无关信息加剧幻觉
- Claude Code 比 Cursor 省 5.5x token，主要靠上下文管理

## 个人实践（pi 环境）
- **memory_search 按需检索**：BM25 + jieba 分词，不存全文到上下文
- **tape_handoff 做交接**：跨会话/compact 后用 tape_read 重建状态
- **subagent 隔离**：搜索/读文件交给 subagent，主上下文只收结论
- **compact 前写 progress**：长会话压缩前记 DONE/IN PROGRESS/BLOCKED
- **AGENTS.md 写"已知坑"**：这是反馈/适应循环，引导 LLM 避开失败场景

## 投递模式
- message-append（默认）：首次 turn 注入一次，低 token 消耗
- system-prompt：每 turn 重复注入，高消耗但始终在上下文
- tape 模式：动态选择相关记忆文件，非全量注入
