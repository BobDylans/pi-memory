---
description: "ocr 审查 session 存储位置与复查方法"
tags:
  - "ocr"
  - "session"
  - "review"
  - "config"
created: "2026-08-11"
updated: "2026-08-11"
---

# ocr Session 存储与复查

## 存储位置
```
~/.opencodereview/sessions/<项目路径hash>/
├── <session-id>.jsonl          # 每次审查一个文件
└── ...
```

项目目录名规则：路径中的 `/` 替换为 `-`，如：
- `~/Projects/ecej-master-bridge` → `home-ivan-ProjectS-ecej-master-birdge`
- `~/Projects/Yazhong` → `home-ivan-ProjectS-Yazhong`

## JSONL 文件内容
每行一个 JSON 对象，记录：
- `session_start`：会话元数据（cwd, branch, model, range）
- `llm_request`：发给 LLM 的完整 prompt（含 diff 和审查规则）
- `llm_response`：LLM 返回的审查意见
- `tool_call`：LLM 调用的工具（code_comment 等）
- `task_done`：审查完成标记

## 复查命令

```bash
# 列出当前仓库的所有审查 session
ocr session list

# 查看某次审查的元数据
ocr session show <session-id>

# 查看评论（文本格式）
ocr session comments <session-id>

# 查看评论（JSON 格式，方便程序读取）
ocr session comments --json <session-id>

# 只看 critical 和 high
ocr session comments --severity critical,high <session-id>

# 只看 security 类别
ocr session comments --category security <session-id>

# 启动 WebUI 查看器（localhost:5483）
ocr viewer
```

## 快速复查所有项目的 session
```bash
# 列出所有项目的 session 文件
find ~/.opencodereview/sessions/ -name "*.jsonl" -exec ls -la {} \;

# 统计各项目 session 数量
for dir in ~/.opencodereview/sessions/*/; do
    echo "$(basename $dir): $(ls $dir/*.jsonl 2>/dev/null | wc -l) sessions"
done
```

## 注意
- session 按项目本地存储，不跨机器同步
- JSONL 文件包含完整 LLM prompt，可用于排查误报原因
- `ocr viewer` 启动 WebUI 可在浏览器可视化查看
