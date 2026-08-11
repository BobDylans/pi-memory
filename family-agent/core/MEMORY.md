## family-agent 本地开发环境

**启动方式**：
- inmem 模式（不依赖 Postgres/Redis）：`uv run python familyagent/cli/lang-dev.py dev --port 8123 --no-browser`，.env 设 `RUN_ENV=inmem`。
- community runtime：`./scripts/dev_langgraph.sh --port 8123 --no-browser`（可选 RUN_ENV=fat 等），必须设置 DATABASE_URI 和 REDIS_URI 环境变量（shell 导出/.env/langgraph.json env 字段）。
- env 层级：.env.base 基线 + .env.<RUN_ENV> overlay → 生成 .env。.env.base 含 LLM 凭据（fastai.enncloud.cn + deepseek-v4-flash-fast，AGENT_MODEL__* 前缀：AGENT_MODEL__MODEL/BASE_URL/API_KEY）。
- langgraph.json 定义 3 个 graph：family_main、family_skill_verification、memory_profile_worker。

**.community-build/ 安装坑**：本地源码缺 `pyproject.toml`，uv sync 无法装包，报 `ModuleNotFoundError: No module named 'langgraph_runtime_community'`。解决：在 `.community-build/pyproject.toml` 建最小配置（name=langgraph-runtime-community, deps: psycopg[binary]>=3.1/psycopg-pool>=3.1/structlog>=23.1）+ `uv add langgraph-api`。 <!-- created=2026-07-02, last=2026-08-09 -->
## deepsearch-tui（独立子项目，从 family-agent fork）

路径 ~/Projects/deepsearch-tui，独立 git 仓库，GitHub BobDylans/deepsearch-tui（已推送 HTTPS，SSH key 未配）。方案文档 docs/deepsearch-tui/PLAN.md（在 family-agent 仓库内）。保留 LangGraph+LangChain 架构，去掉 need/supply 业务，加 RAG（ChromaDB）+ textual TUI。src layout（src/deepsearch/）。

启动命令：`cd ~/Projects/deepsearch-tui && uv run python -m deepsearch.cli.lang_dev dev --port 8123 --no-browser`。bash 工具工作目录固定在 family-agent，操作 deepsearch-tui 需用 --project 指向正确 venv 或绝对路径。langgraph.json 中 graph 路径需用 src/deepsearch/ 前缀。pydantic 子配置类加 extra="ignore" 防止读到其他项目 .env 报错。env 前缀 AGENT_，嵌套分隔 __。

详细项目状态见 deepsearch-tui project MEMORY.md。 <!-- created=2026-07-08, last=2026-08-09 -->
§
family-agent 项目 git remote 是内网域名 e.ennenergy.cn（CODING 私有部署）。全局 git 配置了 http.proxy=http://127.0.0.1:7890（clash/v2ray 代理），但该内网域名必须绕过代理。已配置 `http.https://e.ennenergy.cn.proxy=`（空值绕过代理）和 `credential.https://e.ennenergy.cn.helper=store`。凭证存在 ~/.git-credentials，账号 chengzihao。若 git pull 报 TLS error / unexpected eof，先检查代理是否误拦内网域名。 <!-- created=2026-07-22, last=2026-07-22 -->