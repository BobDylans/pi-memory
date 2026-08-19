---
description: "全局技术经验与教训（迁移自 pi-hermes-memory）"
tags: [memory, lessons, experience]
created: "2026-08-11"
updated: "2026-08-11"
---

pi-hermes-memory 配置位置（2026-08 确认）：配置不在 settings.json 顶层，而是在 ~/.pi/agent/hermes-memory-config.json，llmModelOverride（如 deepseek/deepseek-v4-flash）、llmThinkingOverride、reviewTransport（direct 默认走 completeSimple in-process）都写在此文件。坑：settings.json 顶层加 llmModelOverride 无效。0.9.4 有 ctx stale bug（会话 reload/切换后持有旧 ctx），auto-consolidation 会报 'This extension ctx is stale'，且 auto 触发无 directCtx 只能走 pi -p --no-session 子进程，回退路径不带 --model 时默认 provider 是 google（未配置）导致 Connection error。已改成 fifo-evict 策略（记忆满直接淘汰最旧条目不走 LLM）。 <!-- created=2026-08-09, last=2026-08-09 -->

---

fff 索引 home 目录警告：~/ 太大时 fffindex 会警告，用环境变量 FFF_ENABLE_HOME_SCAN=0（已加到 ~/.config/fish/config.fish）禁用 home 扫描，或 CLI flag --fff-enable-home-scan=false。 <!-- created=2026-08-09, last=2026-08-09 -->

---

Arch 包 tree-sitter 和 tree-sitter-cli 是不同包：tree-sitter 只提供库文件（pkgconfig+license），tree-sitter-cli 才是命令行工具（extra 仓库）。nvim treesitter 编译解析器需要 tree-sitter-cli。 <!-- created=2026-08-09, last=2026-08-09 -->

---

pi models.json deepseek 配置（2026-08 确认）：DeepSeek V4 系列支持思考模式（reasoning_effort low/high/max，xhigh→pro=max、flash=high），models.json 里 deepseek-v4-flash/pro 都设 reasoning:true + compat.supportsReasoningEffort:true 后 pi 才显示思考强度选项。provider 层 compat 需含 requiresReasoningContentOnAssistantMessages / thinkingFormat:"deepseek" / supportsDeveloperRole:false / sessionAffinityFormat:openai / supportsLongCacheRetention。 <!-- created=2026-08-09, last=2026-08-09 -->

---

MCP 配置格式：~/.pi/agent/mcp.json 的 mcpServers 字段用 transport（sse/stdio）非 type，可加 lifecycle（eager 启动即连/lazy 用时才连）。obsidian MCP 设为 lazy 模式（pi 启动不主动连，搜索时才连，服务本身仍需跑）。智谱 vision-mcp 因余额不足已卸载，obsidian-mcp（localhost:3456 SSE vault=braindump）保留但 lazy 模式。 <!-- created=2026-08-09, last=2026-08-09 -->

---

Agent 功能扩展清单（2026-08）：好 agent 应具备子 Agent 调度、规划模式、交互提问、侧问、持久记忆、MCP 接入、网络搜索、浏览器自动化、缓存优化。用户已装 pi-hermes-memory（虽有 bug）/subagents/plan-mode/ask-user/btw/ketch/mcp-extension/cache-optimizer/agent-browser-native/rpiv-web-tools。epiv-web-tools（@juicesharp/rpiv-web-tools 2.3.1）提供 web_search/web_fetch，provider 配 serper（~/.config/rpiv-web-tools/config.json，/web-tools 切换），默认 provider 错误配置为 ollama 会报 OLLAMA_HOST not valid URL。 <!-- created=2026-08-09, last=2026-08-09 -->

---

pi 系统提示词层级（2026-08 确认）：~/.pi/agent/SYSTEM.md 替换默认，~/.pi/agent/APPEND_SYSTEM.md 追加。AGENTS.md 是 context file 优先级低于系统提示词。已写 APPEND_SYSTEM.md（中文回复/audit-first/增量推进/git rebase/工具优先级/大输出摘要/安全规则）。 <!-- created=2026-08-09, last=2026-08-09 -->

---

kickstart.nvim 是单文件配置模板（~30k stars，nvim-lua/kickstart.nvim），官方推荐的独立配置起点，全注释零抽象。相比 LazyVim（全家桶 ~25k）更适合想掌控配置的用户。用户试过 NVChad/AstroLazyVim 但最终回到 LazyVim + nvim-mini(kickstart) 双配置。 <!-- created=2026-08-09, last=2026-08-09 -->

---

RapidOCR 是本地 OCR 首选（2026-08 确认）：pipx 安装路径 /home/ivan/.local/share/pipx/venvs/rapidocr/bin/python3，需 pipx inject onnxruntime。中文识别接近完美（对比 Tesseract 大量乱码）。已写入 AGENTS.md，Tesseract 备用。智谱 vision-mcp 余额不足已弃，优先本地 OCR（RapidOCR）而非视觉模型。 <!-- created=2026-08-09, last=2026-08-09 -->

---

pi-fff 索引 home 警告（2026-08）：FFF_ENABLE_HOME_SCAN=0 或 --fff-enable-home-scan=false 可禁用 home 扫描避免大 home 目录索引耗时耗资源。 <!-- created=2026-08-09, last=2026-08-09 -->

---

pi-hermes-memory 配置位置（2026-08-09 更精确）：配置不在 settings.json 顶层，而是在 ~/.pi/agent/hermes-memory-config.json，llmModelOverride（如 deepseek/deepseek-v4-flash）、llmThinkingOverride、reviewTransport（direct 默认走 completeSimple in-process）都写在此文件。坑：settings.json 顶层加 llmModelOverride 无效。0.9.4 有 ctx stale bug（会话 reload/切换后持有旧 ctx），auto-consolidation 会报 'This extension ctx is stale'，且 auto 触发无 directCtx 只能走 pi -p --no-session 子进程，回退路径不带 --model 时默认 provider 是 google（未配置）导致 Connection error。已改成 fifo-evict 策略（记忆满直接淘汰最旧条目不走 LLM）。 <!-- created=2026-08-09, last=2026-08-09 -->

---

nvim 双配置并存（2026-08 确认）：默认 ~/.config/nvim/ 用 LazyVim（用户倾向省心方案），备用 ~/.config/nvim-mini/（NVIM_APPNAME 隔离，fish 别名 nvim-mini，数据目录 ~/.local/share/nvim-mini）。nvim-mini 用 kickstart.nvim，插件数约 25-29 个。坑：kickstart 用 vim.pack.add 需显式加依赖（如 neo-tree 需要 nui.nvim/plenary.nvim/nvim-web-devicons 否则报 module 'nui.line' not found）；新版 nvim-treesitter API 是 config（单数）；which-key v3 自动拾取 vim.keymap.set 的映射（用户空格+leader 弹出菜单要用 vim.g.mapleader=' ' + 显式注册分组）。用户曾试过从 LazyVim 迁到 kickstart 但因插件/快捷键问题反复，最终倾向默认 LazyVim，nvim-mini 只是学习/轻量补充。 <!-- created=2026-08-09, last=2026-08-09 -->

---

nvim 配置教训（2026-08）：~/.config/nvim 曾被 dotfiles stow 布局污染（混入 aria2/fish/kitty 等 40+ 无关目录，还有嵌套 nvim/、colors/dms.lua 残留），是因为 dotfiles 仓库内容被误拷贝。清理只保留 init.lua/lazy-lock.json/lazyvim.json/stylua.toml/.neoconf.json + lua/。avante.nvim 从未提交 dotfiles git，符号链接缺失时直接删（用户不需要 avante）。改 LazyVim 的 dashboard header（want IVAN 文字）折腾多次未生效（ui.lua 默认优先级高），最终放弃并还原。 <!-- created=2026-08-09, last=2026-08-09 -->
---

superpowers skill 自动触发问题已修复（2026-08-13）：pi 的 skill 是渐进式披露，上下文只有名字+描述，全文需主动 read。模型不总会主动加载（pi 文档承认 "models don't always do this"）。已在 ~/.pi/agent/AGENTS.md 加 "Skill 检查强制规则" 章节：每次任务前必须扫描 skill descriptions，匹配的（哪怕 1% 可能）先 read 全文再执行，announce "Using [skill] to [purpose]"。高频映射：构建→brainstorming、bug→systematic-debugging、写功能→TDD、执行计划→subagent-driven-development、完成→verification-before-completion。流程类 skill 优先于实现类。例外：纯问答/闲聊/查资料可不强制。 <!-- created=2026-08-13, last=2026-08-13 -->
