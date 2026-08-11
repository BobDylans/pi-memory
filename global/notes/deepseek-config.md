---
description: "DeepSeek V4 Flash 配置与已知坑（pi + Codex 双端）"
tags:
  - "deepseek"
  - "config"
  - "pi"
  - "codex"
  - "v4-flash"
created: "2026-08-11"
updated: "2026-08-11"
---

# DeepSeek V4 Flash 配置

## 模型信息
- 模型：`deepseek-v4-flash`（正式版 0731）
- 上下文窗口：1048576（1M token）
- 支持 Responses API（base_url: `https://api.deepseek.com`）
- reasoning_effort: low / high / max
- 8月初支持 deepseek-v4-pro

## pi 配置（~/.pi/agent/models.json）
```json
"deepseek": {
  "baseUrl": "https://api.deepseek.com",
  "api": "openai-responses",
  "compat": {
    "supportsLongCacheRetention": true,
    "sendSessionAffinityHeaders": true,
    "requiresReasoningContentOnAssistantMessages": true,
    "thinkingFormat": "deepseek",
    "supportsReasoningEffort": true
  }
}
```

## Codex 配置（~/.codex/config.toml）
- 用官方脚本 `bash <(curl -fsSL https://cdn.deepseek.com/api-docs/codex-deepseek-setup.sh)` 配置
- 会写入 `~/.codex/models.json`（含 base_instructions 的完整 model build）
- config.toml 里 `model_provider = "deepseek"`, `wire_api = "responses"`

## 已知坑
1. **上下文虽 1M，但 ~100万 token 后性能下降**（"1M token wall"）
2. **reasoning_content 不要在后续 turn 重复引用**（API 会忽略）
3. **长会话超过 80% 上下文时，先 /compact 再继续**
4. **Codex 退出时卡顿**：来自会话 flush + token 统计，可通过 `[analytics]` 关闭部分上报
5. **fish shell 不支持 `bash <(...)` 进程替换**，需用 `bash -c 'bash <(...)'` 或先 curl 到本地
