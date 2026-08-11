---
description: "用户详细档案：偏好、简历、Java 进度、知识库（迁移自 pi-hermes-memory）"
tags: [user, profile, preferences, resume, java]
created: "2026-08-11"
updated: "2026-08-11"
---

重视诚实深入的设计/代码评价，不回避尖锐意见；项目当练习场，学习驱动。沟通：Assertive debugging——不接受表面解释，一直挖根因；说"算了别搞这个了"立即转向，不再解释当前状态。 <!-- created=2026-07-18, last=2026-08-09 -->

---

工具偏好：niri+DMS（Dank Material Shell，自带顶栏非 waybar）；Ghostty 主力终端（~/.config/ghostty/config，bg-opacity=0.9，JetBrains Maple Mono Light，cursor bar）；nvim：Ctrl+S=noautocmd write、原生 autocmd InsertLeave/BufLeave 自动保存（不用 auto-save.nvim 有 bug）、everforest 默认+catppuccin/kanagawa 懒加载、theme.lua 持久化切换主题（DMS 主题接管文件已删）、wl-clipboard 替代 win32yank、SpecialKey 高亮中文预编辑(#9fcbf8)、Ctrl+方向键窗口跳转；fish：proxyOn/proxyOff(7890，NO_PROXY 含 localhost/enncloud/cnvpn.enn.cn)、workVpnOn/workVpnOff(MotionPro: vpnd→vpn_cmdline -h cnvpn.enn.cn -u chengzihao -m ldap-sms，凭证 ~/.config/motionpro-creds，连接前清 HTTP_PROXY 防 Clash，timeout 45，断开 nmcli networking off/on)、steam 带 7890 代理（创意工坊）；git pull.rebase=true；zellij 精简 kanagawa（Ctrl+A pane/Ctrl+Z tab、方向键连续切换、Enter 回 normal、同步 nvim-dotfile 仓库）；oh-my-posh 已卸载；zoxide --cmd cd 替代 z.fish。OCR：RapidOCR 首选（/home/ivan/.local/share/pipx/venvs/rapidocr/bin/python3 调 RapidOCR().txts）→ Tesseract 备用；智谱 vision-mcp 余额不足不可用；优先本地 OCR。 <!-- created=2026-07-16, last=2026-08-07 -->

---

简历用词偏好（2026-08-01，对照 javaguide——"不会的东西不要写在简历上"）：1. 用"熟悉/掌握"不用"精通"；2. 删无项目依据的量化数字（召回25%/相关性18%/年省5万/缓存95%+等查不到源数据的删掉不估算）；3. "自研 JSON 修复状态机"改"JSON 修复兜底"（只说逐字符修复未转义引号，避免被追问实现）；4. LLM 接入与笔记一致——family-agent 的 llm/factory.py 只接单路 OpenAI 兼容(DeepSeek-v4-pro)，不写多路；5. 项目经历按"动词+技术手段+场景+效果"，精选 4 个写透不堆词；6. 技术词规范大小写(MySQL/Spring Boot/Redis)。其他：interview-guide 是个人项目，合并到 ecej-ai 系列做"个人项目沉淀"补充项；不写前端（自认不够了解）；oncall-system 标"后端"非全栈；适度包装——实际没用但属常规选型的技术写"规划中/演进方案"，不编造成已实现。简历文件在 braindump/projects/_求职材料/：60-64（63-简历修改版-对照笔记.md 最新，64-面试学习计划按 P0/P1/P2 对照 braindump 笔记）。 <!-- created=2026-07-25, last=2026-08-07 -->

---

已讲过的 Java 概念（不要重复教）：Map.computeIfAbsent、ConcurrentHashMap 缓存模式、函数式接口(Runnable/Supplier/Consumer/Function/Predicate)、Optional、CompletableFuture.supplyAsync、record 语法糖、JPA @Entity/@Table/@Index、Spring Data JPA 方法名推导 SQL+@Query、虚拟线程池 Executors.newVirtualThreadPerTaskExecutor()、Future vs Runnable/Callable、StructuredOutputInvoker 模式(LLM→JSON 解析→重试修复)、Stream API(map/filter/collect/joining/toList/reversed)、List.reversed()(Java 21 不可变视图)、Spring WebFlux Flux/Mono、Spring Bean 生命周期(实例化→属性注入→Aware→BeanPostProcessor→@PostConstruct→InitializingBean→@PreDestroy)、@Configuration(full CGLIB 代理单例) vs @Component(lite)、@Autowired(类型)/@Qualifier(名)/@Resource(名称)、Lombok @RequiredArgsConstructor(编译期构造器，有 new 等非注入逻辑时不能用)、private 字段子类不可直接访问但存在于子类对象、抽象类(不可实例化/模板方法)、Collectors.joining 三种重载。 <!-- created=2026-07-23, last=2026-08-07 -->

---

Obsidian 知识库（GitHub BobDylans/）：(A) braindump(~/Projects/braindump) 知识沉淀：编号 后端/语言核心=01-11、JVM=20-22、框架原理=30-35、IO网络=40-41、AI工程=50-65、运维=70；projects/ 根目录仅留 00-项目总览.md，其余归 8 子目录（含 _求职材料）；README 用 GitHub 表格格式（[[链接]] 需改写）。(B) obsidian-data(~/Projects/obsidian-data) 算法学习：LeetCode 每日记录(MM-DD 专题-题目.md)+统计板(Dataview)，frontmatter date/title/week/stage/topics/problem_count/review，只用规范题目不编造。写作风格：新建前看同目录已有编号风格再接续；统一 callout([!info]/[!tip]/[!important]/[!warning]/[!note])+mermaid graph+对比表格；技术文档要代码级细节（真实片段+行号引用）不泛泛而谈；项目分析结构=一句话定位→技术栈表→架构亮点(⭐)→优化空间→简历可写点→面试问答预演。frontmatter 坑：tags 块后不能空行、结束 --- 不能丢。Obsidian MCP(madebydia/obsidian-mcp) SSE localhost:3456，vault=braindump；pi mcp.json 用 transport 字段("sse")非 type。 <!-- created=2026-08-01, last=2026-08-07 -->

---

用户要求 pacman 不要使用国内镜像网站（bfsu/hit/xjtu/ustc/aliyun 等），优先使用 Arch 官方原版地址（geo.mirror.pkgbuild.com）。2026-08-07 已将 /etc/pacman.d/mirrorlist 改为官方源，原配置备份在 mirrorlist.bak。 <!-- created=2026-08-07, last=2026-08-07 -->

---

使用 Arch Linux + fish shell。目前面试求职中，找 Java 后端方向工作，偏好中文简洁交流。 <!-- created=2026-08-09, last=2026-08-09 -->