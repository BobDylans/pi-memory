---
description: "pi 扩展配置记录与删除决策"
tags:
  - "pi"
  - "extensions"
  - "config"
created: "2026-08-11"
updated: "2026-08-11"
---

# pi 扩展管理

## 已安装扩展（20个）
- pi-subagents（subagent 工具）
- pi-web-access（已删，改用 rpiv-web-tools 后搜索需 key，目前免费搜索失效）
- pi-ask-user（ask_user 工具）
- pi-mcp-adapter（MCP 网关，自动读 ~/.config/mcp/mcp.json）
- pi-cache-optimizer（缓存命中优化，DeepSeek compat）
- pi-ultra-compact（上下文压缩）
- pi-add-dir（add_directory 工具）
- @devkade/pi-plan（/plan 模式）
- pi-prompt-template-model
- @juanibiapina/pi-extension-settings
- pi-memory-md（git:VandeeFeng/pi-memory-md，记忆系统）
- obra/superpowers（skills 生态）
- @ff-labs/pi-fff（FFF 模糊搜索，home 目录会报错）
- pi-rtk-optimizer（bash→rtk 重写，rtk 0.42.0 已装）
- pi-btw（/btw 并行侧边对话）
- @tmustier/pi-raw-paste（/paste 原生粘贴）
- pi-simplify（代码清晰度审查）
- pi-zentui（statusline 美化）
- pi-intercom（多会话通信）
- @juicesharp/rpiv-web-tools（web_search，需 API key）

## 已删除扩展
- pi-web-access（和 rpiv-web-tools 的 web_search 冲突）
- pi-tool-display（工具显示优化，已删）
- pi-cc-header（启动 banner，已删）
- @tmustier/pi-ralph-wiggum（长循环，已删）

## 待解决
- **免费搜索**：删了 pi-web-access 后，rpiv-web-tools 需要 API key，免费 Exa 搜索失效。考虑重新装回 pi-web-access 并删 rpiv-web-tools
- **pi-fff 在 home 目录报错**：FFF 不能在 home 根目录运行，需 cd 到项目目录或删除该扩展
