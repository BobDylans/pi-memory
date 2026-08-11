# AI Module — Architecture, Status & Findings

**Location**: `oncall-system-backend/src/main/java/com/oncall/system/ai/`
**Stack**: Spring Boot 3.1.5 + MyBatis-Plus, JWT auth (jjwt). Vue 3/Vite frontend.
**Design reference**: `/home/ivan/Projects/nightingale` aiagent/ 包（ReAct loop, plan-react, prompt builder, MCP tools, 3-level skill system）。**Note**: DESIGN.md does NOT exist — frontend-design skill used as fallback.

## Providers
- **OpenAiChatModel**: Standard OpenAI API, supports function calling, sends stop sequences
- **SiaiChatModel**: AES encrypted login + token auto-refresh (synchronized(this) on ensureTokenCache()), non-standard API — context built as single text blob via buildContextInput(), not messages array. No native function calling → ReAct tool calls via text format (Action:/Action Input:) parsed by parseResponse(). "Observation:" stop sequence never sent in Siai HTTP body (only OpenAi). Round-robin session management (atomic sessionIndex). ForkJoinPool.commonPool() for async calls (exhaustible under high concurrency).

## Package structure
agent/ (AiAgent, AgentConfig/Request/Response, RunContext) · config/ (AiProperties, AiConfig) · react/ (ReActLoop, ReActLoopConfig) · skill/ (SkillRegistry, SkillMetadata, SkillContent) · tool/ (ToolRegistry, ToolDefinition, ToolExecutor, ToolParameter, OncallTools, ToolDeps, ExternalToolHandler) · llm/ (ChatModel, OpenAiChatModel) · dto/ (ChatRequest, ChatResponse, StreamChunk) · controller/ (AiChatController, AiChatService).

Entry flow: AiAgent.run() → selectAndLoadSkills() → assembleTools() → buildSystemPrompt() → ReActLoop.execute()

## ReAct Loop (post-commit 4449426)
**Done**: trimMessages() (70% window, drops earliest turns), validateToolArgs() (JSON validation pre-call), resolveErrorMessage() (timeout/rate-limit/content-filter), drainResponse() (prevents OkHttp leaks). Removed pushClassifiedLine → final answer batch push.

**Known Issues**:
- 🟡 onToolCall double-fire: fires inside SSE parsing callback AND after stream end via extractToolCall(). captured[0] partially mitigates but not fully resolved.
- No empty-response guard / No Plan-ReAct mode
- ForkJoinPool.commonPool() exhaustible under high concurrency (default = CPU core threads)
- **Skill 集成断裂**：SkillRegistry.selectAndLoadSkills() 有完整实现（YAML frontmatter 解析、三级加载、LRU 缓存），但 AiAgent.run() 从未调用它。技能仅通过 buildSystemPrompt() 手动字符串拼接注入 system prompt，技能选择/注入管线是死代码。

## Retry Mechanism
RetryConfig + RetryPolicy: exponential backoff (baseDelay * 2^attempt) + random jitter (0.5-1.5x). Three modes: blocking (run()), streaming (runStream()), tool execution (run()). Configurable per operation type via AiProperties.retry. Retryable: 429/5xx/IOException. Non-retryable: 4xx (except 429) skip immediately.

## TODO
1. Stop sequence transmission for Siai (prevent LLM hallucination of tool results)
2. Empty-response guard (circuit break on repeated empty responses)
3. Plan-ReAct mode support
4. Prompt builder modularization (extract buildSystemPrompt() from AiAgent, mirror nightingale's prompt_builder.go) <!-- created=2026-06-06, last=2026-08-09 -->