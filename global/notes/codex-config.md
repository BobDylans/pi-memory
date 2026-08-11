---
description: "Codex CLI 配置记录"
tags:
  - "codex"
  - "config"
  - "deepseek"
created: "2026-08-11"
updated: "2026-08-11"
---

# Codex CLI 配置

## 基本信息
- 版本：codex-cli 0.146.0（Rust）
- 配置目录：`~/.codex/`
- 模型：deepseek-v4-flash（reasoning_effort: high）
- provider: deepseek（base_url: https://api.deepseek.com/, wire_api: responses）

## 配置文件
- `~/.codex/config.toml`：主配置
- `~/.codex/models.json`：model build（含 base_instructions，官方脚本写入）
- `~/.codex/deepseek-model-build.json`：备用 model catalog（缺 base_instructions，未启用）
- 原配置备份：`~/.codex/backup-deepseek/`

## 官方安装脚本
```bash
bash -c 'bash <(curl -fsSL https://cdn.deepseek.com/api-docs/codex-deepseek-setup.sh)'
# fish 不支持 <()，用这个：
bash /tmp/codex-deepseek-setup.sh  # 先 curl 到本地
# 输入顺序：先菜单(1=flash) 再 API key
```

## 已知问题
- 退出时卡顿：会话 flush + token 统计（[analytics] 可关部分）
- token usage 显示无法完全关闭
- Codex 是开源的（github.com/openai/codex，Apache 2.0）
