---
description: "MCP 服务器配置（Context7 + GitHub）"
tags:
  - "mcp"
  - "context7"
  - "github"
  - "config"
created: "2026-08-11"
updated: "2026-08-11"
---

# MCP 服务器配置

## 配置文件
`~/.config/mcp/mcp.json`（pi-mcp-adapter 自动读取）

## 已配置服务器
1. **github**：`@modelcontextprotocol/server-github`（需 GITHUB_PERSONAL_ACCESS_TOKEN）
2. **context7**：`@upstash/context7-mcp`（免费，无需 key）

## Context7 用法
两步查询：
1. `context7_resolve-library-id`：库名 → Context7 ID（如 `libraryName: "Next.js"`）
2. `context7_query-docs`：ID + 问题 → 最新文档（如 `libraryId: "/vercel/next.js", query: "app router"`）

## 优先级规则
- 技术问题（库/框架/SDK/API）→ 优先 Context7 MCP
- Context7 查不到或需最新资讯/新闻 → web_search（Exa 免费 MCP）
