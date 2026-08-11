## noonDoc — 中东电商 Noon.com 商业情报系统

**路径**: `/home/ivan/Projects/noonDoc` (大写 P: **Projects**；分支 `fix/extraction-and-analysis`，commit b71b113；master 初始 0e7ced2)
**用户**: chengzihao (陈子豪 / Ivan)
**定位**: 抓取 Noon.com (uae-en/kuwait-en) 商品 → DuckDB 存储 → 分析 → 商业决策(中东市场)。下游: n8n 做行动层(邮件/联系客人)。

**技术栈**:
- 爬虫: Crawl4AI + 真实 Chrome via CDP (`--remote-debugging-port=9222 --user-data-dir=~/.chrome-crawl-profile`)。**非 headless**——Akamai 在 TLS 层拦截。
- 提取: Playwright `page.evaluate()` 读 `.ProductBoxVertical` 卡片 DOM (Noon 是 React SSR，markdown/JSON 拿不到商品)。
- 存储: DuckDB 单文件 (`data/noon.duckdb`)。表: product_snapshots / price_history / crawl_log / markets。uae→AED, kuwait→KWD。
- 分析: JupyterLab (`notebooks/analysis.ipynb`) + `scripts/analyze.py` CLI。
- 看板: **前后端分离** — 后端 `dashboard/server.py` (FastAPI, 8 端点 + Swagger, API 版本化 `/api/v1/*`)；前端 `dashboard/frontend/` (原生 HTML/CSS/JS, 零框架, Chart.js CDN, http.server :8080)。一键启动 `dashboard/start.sh` (Chrome CDP + uvicorn:8000 + http.server:8080)。server.py 读 DuckDB 需 `read_only=True` 否则与爬虫写进程冲突报 500。
- 调度: cron。

**结构**: `noon_crawler/{crawler,extractor,playwright_extractor,database,parsing}.py`, `run_pipeline.py`, `dashboard/{server.py,frontend/,templates/}`, `scripts/{check_chrome.sh,analyze.py,verify_cdp.py}`, `notebooks/analysis.ipynb`, `tests/test_parsing.py`。
- `parsing.py`: 6 纯函数 (parse_sku / parse_review_count / parse_discount / match_brand / convert_currency / normalize_title) 无副作用、无 DuckDB 依赖，提取修复线 Agent 直接调用。
- `tests/test_parsing.py`: 30 个 parametrized 测试 (`uv run pytest tests/ -v`)，TDD 模式 (先写测试 → 再写实现)，新建解析逻辑沿用此模式。

**API v1 筛选**: `/api/v1/products`、`/price_tiers`、`/top_products` 支持 `category` (模糊匹配，如 `headphones`) + `date` (`YYYY-MM-DD`) 参数；新增 `/categories`、`/dates` 返回列表+计数。前端已加品类/日期下拉筛选 UI。

**运行**: 从项目根 `uv run python run_pipeline.py` (Python 3.13 via uv, Chrome 148)，或 `start_crawl.sh`。DB_PATH 需从根运行否则找不到 data/noon.duckdb；Jupyter 从根 `uv run jupyter lab notebooks/analysis.ipynb` 启动 (cwd 问题)。
**状态**: Chrome CDP + 提取 + DuckDB 已跑通。**价格抓取仍有 bug** (部分 price=nan)；dashboard `/api/overview` 500 bug (NaN 序列化) 已修。
**计划**: `.rpiv/artifacts/plans/2026-06-23_crawl4ai-to-duckdb-noon-pipeline.md` + `2026-06-23_frontend-backend-separation.md`。

**用户意图**: 要**可视化看板**替代 CLI，且要**前后端分离架构**让多 Agent 各自独立开发前后端。JSON 是前后端唯一契约。文档优先 ("先完善一下文档")。

**多 Agent 分工** (双线并行): 一线动 `noon_crawler/` (提取修复 + analyzer + 测试)；本线只动 `dashboard/`，不碰 noon_crawler。公共基建 (pyproject 依赖) 可提前推进。

**代码坑**:
1. Playwright `page.evaluate()` 用 Python f-string 包裹时 JS 里 `{` 须写 `{{`；改用普通字符串 + `page.evaluate(js, arg_dict)` 传参。
2. DuckDB `cursor.fetchdf()` 需装 pandas (已加 pyproject.toml)。
3. Jupyter `Path.cwd()` 取启动目录非 notebook 目录；DB_PATH 自动搜 cwd 与 cwd.parent。
<!-- created=2026-06-23, last=2026-06-23 --> <!-- created=2026-06-23, last=2026-06-23 -->
§
## noonDoc 代码约定与 DuckDB 坑

新代码一律遵守：
- **时间**: Python 3.13 废弃 `datetime.utcnow()`。统一用 `datetime.now(timezone.utc)`。全 5 文件 (crawler/extractor/playwright_extractor/database/run_pipeline) 已迁移，新文件照此写。
- **DuckDB 无 SERIAL/IDENTITY**: `SERIAL` 报 NotImplementedException，`INTEGER GENERATED ALWAYS AS IDENTITY` 报 Parser Error。`product_snapshots.id`/`crawl_log.id` 用 `INTEGER PRIMARY KEY` + 手动传 UUID hex hash 迂回(不依赖自增)。新表勿用 SERIAL/IDENTITY。
- **DuckDB NaN→JSON**: 含 NaN 列 (price/rating) 触发 `ValueError: Out of range float values are not JSON compliant`。连接复用务必带 config: `duckdb.connect(str(DB_PATH), config={'replace_nan': True})` (见 `dashboard/db.py get_conn()`)。
- **Schema 迁移**: `database.py init_db()` 在 CREATE TABLE 后用 `ALTER TABLE ... ADD COLUMN IF NOT EXISTS` 向前兼容旧库 (如 discount_pct/brand/review_count)。只读连接 (read_only=True) 跑不了 ALTER，需先对可写连接跑一次 init_db() 确保新列存在。
<!-- created=2026-06-23, last=2026-06-23 --> <!-- created=2026-06-23, last=2026-06-23 -->