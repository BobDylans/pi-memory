# RPC 学习进度与踩坑记录

> 更新: 2026-08-11 · 当前进度: 阶段 9 完成

## 进度总览

| 阶段 | 内容 | 状态 |
|---|---|---|
| 3-5 | 骨架/协议编解码/序列化 | ✅ 完成 |
| 6 | 服务端 (Netty + 反射调用) | ✅ 完成 |
| 7-8 | 客户端 (动态代理 + Future 异步) / 端到端 | ✅ 完成 |
| 9 | ZK 集成与服务注册 | ✅ 完成 |
| 10 | 服务发现与客户端缓存 | ⬜ 下一步 |
| 11-14 | 负载均衡 / 注解驱动 / 心跳 / 总结 | ⬜ |

## 代码结构

- `rpc-core/.../protocol/` — 协议头 (11字节) + RpcMessage
- `rpc-core/.../codec/` — RpcMessageDecoder/Encoder
- `rpc-core/.../serialization/` — JDK + Kryo (ThreadLocal)
- `rpc-core/.../server/` — ServiceRegistry(本地表) + RpcServer + RpcRequestHandler
- `rpc-core/.../client/` — UnprocessedRequests + RpcResponseHandler + RpcClient
- `rpc-core/.../proxy/` — RpcClientProxy (JDK 动态代理)
- `rpc-core/.../registry/` — ServiceRegistry(接口) + CuratorUtils + ZkServiceRegistry

## 踩坑记录（重要）

1. **解码器死循环**：`resetReaderIndex` 必须配 `markReaderIndex`，否则粘包+半包场景重复解码死循环
2. **InvocationTargetException**：`Method.invoke` 包装目标异常，`getCause()` 解包才能拿到真实业务异常
3. **docker0 被 NetworkManager 抢 IP**：NM 接管 docker0 清掉 172.17.0.1/16 → 容器端口映射失效。修复: `/etc/NetworkManager/conf.d/docker.conf` 设 `unmanaged-devices=interface-name:docker0;interface-name:veth*`
4. **Curator 连接**：`client.start()` 是异步的，需 `blockUntilConnected(5s)` 否则 create 无限重试

## 关键设计决策

- `UnprocessedRequests`: requestId → CompletableFuture 配对表（响应乱序匹配）
- `RpcMessage.data` 用 Object 而非泛型（运行期分发）
- Kryo 用 ThreadLocal（与 Dubbo 默认一致）；Dubbo 3.x 已移除 Kryo
- 测试端口: 18090-18092；ZK 单节点 Docker: `docker run -d -p 2181:2181 zookeeper:3.9`

## 环境备忘

- 本地 ZK 容器名 `zookeeper`，若 docker0 丢 IP 需补 `sudo ip addr add 172.17.0.1/16 dev docker0`
- 文档在 braindump/后端知识/中间件/（00-14 阶段文档，含 04/05/06/07 已更新结合代码）
- ConcurrentHashMap 深入笔记: braindump/后端知识/语言核心/12
