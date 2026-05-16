# 03 — Degradation Awareness

## The Principle

**Silent degradation is worse than no degradation.**

When a system falls back from a better mode to a worse mode without telling the user, the user experiences the failure as *unreliability* — "the AI is broken today" — rather than understanding what happened and adjusting.

## The Pattern

Every fallback path must satisfy three conditions:

1. **Signal:** The system sets a machine-readable flag indicating degradation occurred
2. **Display:** The UI renders a visible, non-intrusive indicator explaining what degraded and why
3. **Log:** The degradation is logged at WARN level for monitoring and debugging

## Real Examples from the AI Library Project

### Example 1: Agent → Chat Fallback

**Scenario:** A user asks the AI assistant to search for books. The Function Calling API fails.

**Before fix:** The gateway caught the exception and silently called the plain chat API. The user got a text response that *looked* like a normal AI reply, but had no tool capabilities. The user had no way to know their request wasn't processed with full capability.

**After fix:**
```java
// Backend: set degraded flag
AIResponseDTO degraded = aiService.processTextRequest(request);
degraded.setDegraded(true);
return degraded;

// Frontend: show amber banner
v-if="message.degraded"
  → "智能模式暂时不可用，已自动切换为对话模式回复"
```

### Example 2: Vector Search → Keyword Search

**Scenario:** The embedding API fails during semantic search.

**Before fix:** `VectorSearchStrategy` returned an empty list when the embedding returned `float[0]`. The `SemanticSearchService` silently dropped the vector channel and returned keyword-only results. The user saw fewer, less relevant results with no explanation.

**After fix:**
```java
// Backend: throw instead of silently returning empty
if (queryEmbedding.length == 0) {
    throw new AIExecutionException("向量生成失败");
}

// In SemanticSearchService catch block:
vectorSearchDegraded[0] = true;

// Response includes:
response.put("vectorSearchDegraded", true);

// Frontend: search info bar shows
"语义搜索降级" tag
```

### Example 3: LLM Failure → Fake Assistant Message

**Scenario:** The DashScope API call fails entirely.

**Before fix:** `AIServiceImpl` caught the exception and returned the string `"抱歉，AI 服务暂时不可用，请稍后重试"` — then stored it as a normal ASSISTANT message in the conversation history. The fake message polluted the LLM context for all subsequent turns.

**After fix:**
```java
response = "> ⚠️ AI 服务暂时不可用，以下为系统兜底回复\n\n"
         + "抱歉，AI 服务暂时不可用，请稍后重试";
degraded = true;
// Stored with degraded prefix so future context shows it was a fallback
```

## Taxonomy of Degradation

| Type | Example | User Impact | Fix Pattern |
|------|---------|-------------|-------------|
| Mode switch | Agent → Chat | Lost tool capability | Set `degraded` flag + frontend banner |
| Channel loss | Vector → Keyword only | Reduced result quality | Add `*Degraded` field to response |
| Fake success | LLM error stored as reply | Context pollution | Prefix message + don't store as normal |
| Stale data | Redis → DB fallback | Slower, possibly stale | Set `source` field in response |
| Rate limit | Full → throttled | Delayed or denied | HTTP 429 + Retry-After header |

## Integration with the Review Pipeline

During `/module-audit`, one of the Phase 3 checks is: "For every `catch(Exception e)` that returns a default value instead of propagating, is the user informed?"

This is now a standard part of Evan's code review checklist.
