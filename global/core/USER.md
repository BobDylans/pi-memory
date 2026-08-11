---
description: "用户身份、环境与核心偏好"
tags:
  - "user"
  - "identity"
  - "environment"
  - "preferences"
created: "2026-08-11"
updated: "2026-08-11"
---

# 用户档案

## 基本环境
- 系统：WSL2 (Arch Linux) · fish 4.7.1 · `LANG=zh_CN.UTF-8` · Java 21
- 终端复用：zellij（tmux 未装，别用 `tx` 函数）
- Alias: `ll`=eza · `cat`=bat · `bb`=bat-plain · `mvn`=mvnd · `cdz`=zoxide
- 运行时: Node · Bun · pnpm · Python 3.14 (`uv`/`uvx`) · Go 1.26 · Rust 1.94
- 代理: `proxyon` / `proxyoff`（7890/7891）

## 工作风格
- **Think-in-Code**：只把答案带进上下文，原始数据留在沙箱里
- 偏好中文交流，技术术语可用英文
- 喜欢直接执行（YOLO 模式），不需要反复确认
- 改代码后告知改了什么，由用户自行 git 提交（禁止自动 git commit/push）
- **例外**：记忆仓库 `~/.pi/memory-md`（pi-memory-md 记忆系统）可自主 commit + push 管理，无需询问；项目代码仓库一律不自动 push

## Agent 使用偏好
- 主力 Agent：pi（多 provider 灵活切换）
- 备用 Agent：Codex CLI（Rust，接 DeepSeek V4 Flash）
- 不喜欢上下文被污染，偏好按需检索而非全量注入
- 技术问题优先用 Context7 MCP 查文档，而非盲目联网搜索
