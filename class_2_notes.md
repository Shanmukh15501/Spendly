# Class 2 Notes: Context Window Management in Claude

---

## 1. What is a Context Window?

The context window is the total amount of text (tokens) Claude can **see and reason over at one time**.

It includes everything in a conversation:
- System prompt
- User messages
- Assistant replies
- Tool results
- Injected content (memory, files, etc.)

### Token Limits by Model

| Model | Context Limit |
|---|---|
| Claude Haiku 4.5 | 200K tokens |
| Claude Sonnet 4.6 | 200K tokens |
| Claude Opus 4.7 | 200K tokens |

> **Rule of thumb:** 1 token ≈ 0.75 words → 200K tokens ≈ 150,000 words

---

## 2. Why Does Context Need to Be Managed?

Without management, these problems arise:

| Problem | Description |
|---|---|
| **Overflow** | Conversation grows too long → oldest content gets truncated or API errors |
| **Cost** | Every token sent costs money; redundant history inflates cost fast |
| **Latency** | Larger contexts take longer to process |
| **Noise** | Irrelevant old content degrades response quality |

---

## 3. How Claude Manages Context (Core Techniques)

### 3.1 Conversation Compaction (Auto-Summarization)
- When conversation approaches the limit, raw message history is **replaced with a condensed summary**
- Critical facts (code changes, decisions) are preserved
- Verbatim tool outputs and intermediate steps are dropped

### 3.2 Prompt Caching
- Static parts of the prompt (system prompt, large documents) are cached at the API level
- Cache TTL = **5 minutes**
- Cache hits save ~90% of input cost on cached tokens
- After 5 min → cache miss → full content re-sent at normal cost

### 3.3 Selective Context Injection
- Tools like `Read` (with `offset`/`limit`) and `Grep` pull only **relevant excerpts**
- Avoids dumping entire files into context

### 3.4 Tool Result Truncation
- Large tool outputs are trimmed before being added to context
- Model gets enough to reason with, not the full raw dump

### 3.5 Memory System (External Storage)
- Long-term facts are stored in **files outside the context window**
- Recalled when relevant, not loaded all at once
- Survives across conversations

---

## 4. Context Window Optimization Techniques

### 4.1 Prompt Caching
```python
client.messages.create(
    model="claude-sonnet-4-6",
    system=[{
        "type": "text",
        "text": "<large system prompt or document>",
        "cache_control": {"type": "ephemeral"}  # cached for 5 min TTL
    }],
    messages=[{"role": "user", "content": "your question"}]
)
```
> Cache breakpoints must be on the last **1024+ tokens** of a block to be eligible.

---

### 4.2 Conversation Summarization
Replace old turns with a summary, keep only recent raw messages:
```
[SUMMARY: User set up auth, added DB schema, fixed login bug]
[Turn N-2]: ...
[Turn N-1]: ...
[Turn N]: current
```

---

### 4.3 Sliding Window (Keep Last N Turns)
Drop old messages entirely, keep only the last N turns:
```python
MAX_TURNS = 10
messages = messages[-MAX_TURNS:]
```
> Best for stateless Q&A where earlier context rarely matters.

---

### 4.4 Retrieval-Augmented Context (RAG)
Store documents in a vector DB, retrieve only the **top-k relevant chunks** per query:
```
User query → embed → vector search → inject top 3 chunks → send to model
```
Tools: `pgvector`, `Pinecone`, `Chroma`, `LanceDB`

> Highest-leverage technique for large knowledge bases.

---

### 4.5 Selective Tool Result Trimming
```python
# Bad: dump entire file (~15k tokens)
content = open("large_file.py").read()

# Good: only what you need
lines = open("large_file.py").readlines()[50:80]
```
In Claude Code → use `offset`/`limit` on Read, and Grep before Read.

---

### 4.6 Compressed System Prompts
```
# Bad (verbose, wastes tokens every turn)
You are a helpful assistant that helps users with their expense tracking application.
You should always be polite and considerate...

# Good (tight, same meaning)
Expense tracker assistant. Be concise. Stack: React + Node + PostgreSQL.
```

---

### 4.7 Token Counting Before Send
```python
response = client.messages.count_tokens(
    model="claude-sonnet-4-6",
    system=system_prompt,
    messages=messages
)
print(f"Input tokens: {response.input_tokens}")
# Decide whether to trim before calling messages.create()
```

---

### 4.8 Tiered Memory Architecture

| Tier | Storage | Access |
|---|---|---|
| **Hot** | In-context (last 5 turns) | Instant, costs tokens |
| **Warm** | Summary injected at session start | Cheap, pre-cached |
| **Cold** | File-based memory / vector DB | Fetched on demand |

---

### 4.9 Autocompact Tuning (Claude Code specific)
```bash
# Force compaction now
/compact

# With custom instruction — guide what survives
/compact Focus on keeping all file paths and code decisions
```

---

## 5. Autocompaction vs `/compact`

| | Autocompaction | `/compact` (Manual) |
|---|---|---|
| **Triggered by** | Claude Code automatically | You, explicitly |
| **Timing** | ~80% context fill | Whenever you choose |
| **Summary style** | Generic | Customizable with instructions |
| **Interruption risk** | Can happen mid-task | Zero — you pick the moment |
| **Goal** | Prevent crash from overflow | Proactive, intentional cleanup |

### When to use `/compact` manually:
- Before starting a large new task
- After finishing a major feature
- When `/context` shows 60–70%+ usage
- When you want to control exactly what Claude remembers

> **One-liner:** Autocompaction saves you from crashing. `/compact` lets you compact on your terms.

---

## 6. Token Cost Reference

| Content | Approx Tokens |
|---|---|
| 1 line of code | ~10 tokens |
| 1 page of text | ~500 tokens |
| Average JS file (200 lines) | ~2,000 tokens |
| Full file read (1000 lines) | ~10,000 tokens |
| Large API response | ~5,000–20,000 tokens |

---

## 7. What Happens at the Context Limit?

| Scenario | Behavior |
|---|---|
| API called with >200K tokens | API returns an error |
| Claude Code auto-compaction | History summarized, conversation continues |
| Cache miss after 5 min | Full context re-sent, billed at normal rate |
| Memory recall | Relevant memory files injected into context on demand |

---

## 8. Key Takeaways

1. Context window = everything Claude sees at once (limited, finite, expensive)
2. Biggest token drains: tool results, full file reads, verbose system prompts
3. Best optimization wins: **RAG** (don't load what you don't need) + **prompt caching** (don't re-pay for what doesn't change)
4. `/compact` > autocompaction when you want control over what survives
5. Use `/context` command to monitor your usage live in Claude Code
