ecej-master-birdge 项目要点：

**政府对接模块**："一省一套"复制模式（贵州/新乡/瑞安/泉州/浙江），每省独立 PO+Service+Mapper+Controller，无公共抽象基类。加密各省不同：浙江 SM4/ECB/PKCS5Padding+Hex、泉州另套、贵港 AES。新增省份对接注意复用结构。

**RestTemplateConfig**（base config 包下，连接池200/读超时10s/连超时5s）：需 HTTP 调用时注入 RestTemplate Bean，不要 new RestTemplate()。浙江一体化原版 callbackToCentral 每次新建，已改为注入。

**异步回调改造**（已提交 develop，commit 48bc970c [浙江一体化]异步回调,重试,日志入库，非 feature 分支独有）：AsyncConfig 线程池（核心3/最大10/队列100/CallerRunsPolicy）+ ZjGasCallbackTask（@Async("zjCallbackExecutor") 注入连接池 RestTemplate，重试3次间隔2秒）+ Startup 加 @EnableAsync + ZjGasServiceImpl.callbackToCentral 委托给 ZjGasCallbackTask（接口方法保留因 dubbo-provider.xml 暴露 ZjGasService）。feature 分支原实现（5906748d/72b6026a/af9b4478）未合并，问题：RestTemplate 每次 new 无连接池/Thread.sleep 阻塞/无失败补偿/Startup 缺 @EnableAsync。

**浙江一体化 header 鉴权**：开关 ecej.zhejiang.header.check.enabled 在 nacos 配置，联调时改 true 生效。graphify-out/ 已加入 .gitignore。 <!-- created=2026-08-07, last=2026-08-07 -->