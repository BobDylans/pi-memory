---
description: User profile, habits, and preferences
tags: [user, profile, preferences, java, rpc]
created: 2026-08-11
---

# User Profile

## Communication Style
- 简洁优先，不要废话。给结论，不要铺垫
- 中文回复，代码注释用中文
- 非本地项目代码用英文，本地项目用中文注释
- 大输出只保留摘要（>20 行截断）

## Environment
- OS: Arch Linux (rolling), kernel 7.0.10-zen, locale zh_CN.UTF-8
- Shell: fish, terminal kitty (Wayland/niri), CPU: AMD Cezanne
- Editor: nvim (LazyVim), 终端复用: zellij
- Docker 可用；本地 Docker 起 ZK (zookeeper:3.9, 端口 2181)

## Tool Preferences
- 代码搜索 rg → grep；文件查找 fd → find；查看 bat → less
- 包管理 yay → paru → pacman
- 技术文档检索: **context7 MCP 首选**（mcp_context7_query_docs）→ ketch_docs → web_search
- 开源实现对照: ketch_scrape（如抓 guide-rpc-framework 源码）
- JSON 处理 jq；目录导航 zoxide；版本控制 git（rebase 优先）

## Development Workflow
- 审查优先 (audit-first): 先读懂现有代码再动手，不猜测
- 增量推进: 小步改动，每步可验证，不大重构
- **修改代码后不要提交，保留暂存状态即可**（除非明确要求 commit）
- commit message 遵循 Conventional Commits 格式

## Code Style
- 中文 Javadoc 注释，注释"为什么"而非"是什么"
- 学习项目代码里用 {@link /路径} 关联 braindump 学习文档
- 保持与现有代码风格一致（详尽注释、包结构清晰）

## Project Context
- my-rpc: 手写 RPC 框架学习项目（Netty 4.1 + Kryo 5 + Curator/ZK + Maven 多模块）
- 对标 guide-rpc-framework；学习文档在 braindump/后端知识/中间件/（00-14 阶段路线）
- 学习进度: 阶段 9 完成（骨架/协议/序列化/服务端/客户端/端到端/ZK 注册）
- 已踩坑: 解码器 markReaderIndex 死循环、InvocationTargetException 解包、docker0 被 NetworkManager 抢 IP
