# cc-soul — Your AI, But It Actually Knows You

Your AI forgets everything after each session. cc-soul fixes that — persistent memory, adaptive personality, emotion tracking, and learning from corrections. Works with any AI.

## Two Ways to Use

### OpenClaw Users (one command, zero config)

```bash
openclaw plugins install @cc-soul/openclaw
# Done. Works automatically. Say "help" to see commands.
```

### Any AI (local API)

```bash
npm install -g @cc-soul/openclaw
# Done. API auto-starts at localhost:18800
```

LLM configuration is **automatic** — cc-soul reads your OpenClaw config if available. Most features (memory, recall, persona, emotion) work without any LLM. Only `/soul` endpoint and background tasks need LLM access.

If not using OpenClaw, configure LLM via API after install:

```bash
curl -X POST localhost:18800/config \
  -H "Content-Type: application/json" \
  -d '{"api_base":"https://api.openai.com/v1","api_key":"sk-xxx","model":"gpt-4o"}'
```

---

## API Endpoints

| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/process` | **Core.** Send user message, get augmented context (memory + persona + emotion + cognition). No LLM needed. |
| `POST` | `/feedback` | Send AI's reply back. cc-soul learns, stores memories, tracks quality. All POST endpoints accept optional `agent_id` for multi-agent data isolation. |
| `POST` | `/soul` | Soul mode — cc-soul replies as the user's avatar (needs LLM). |
| `POST` | `/config` | Configure LLM backend (only needed for `/soul`). |
| `POST` | `/command` | Execute cc-soul commands (stats, search, habits, etc.). |
| `GET` | `/profile` | User personality profile — avatar, social graph, mood, energy. |
| `GET` | `/features` | List all feature toggles and their status. |
| `POST` | `/features` | Enable/disable a feature. |
| `GET` | `/health` | Health check. |
| `GET` | `/.well-known/agent.json` | A2A Agent Card (5 capabilities). |
| `POST` | `/a2a` | Agent-to-Agent protocol request. |
| `GET` | `/mcp/tools` | List MCP tools (4 tools). |
| `POST` | `/mcp/call` | Execute an MCP tool call. |
| `GET` | `/avatar` | Soul injection prompt — makes any LLM respond as you. Accepts `?sender=` and `?message=` params. |
| `GET` | `/soul-spec` | Returns soul spec files (soul.json, STYLE, IDENTITY, HEARTBEAT) dynamically. |
| `POST` | `/api` | Unified entry point — routes to any action via `{"action": "process\|feedback\|soul\|..."}`. |

### POST /process

The core endpoint. Send a user message, get back enriched context to feed your AI.

```bash
curl -X POST http://localhost:18800/process \
  -H "Content-Type: application/json" \
  -d '{"message": "Help me fix this timeout error", "user_id": "alice"}'

# Multi-agent: each agent_id gets isolated memory/personality
curl -X POST http://localhost:18800/process \
  -H "Content-Type: application/json" \
  -d '{"message": "Hello", "user_id": "alice", "agent_id": "support-bot"}'
```

Response:

```json
{
  "system_prompt": "...(personality + rules + identity)...",
  "augments": "...(recalled memories + proactive insights + persona hints)...",
  "augments_array": [{"content": "...", "priority": 8.2, "tokens": 45}],
  "mood": 0.1,
  "energy": 0.8,
  "emotion": "neutral",
  "cognition": {"attention": "technical", "intent": "wants_answer", "strategy": "balanced", "complexity": 0.6}
}
```

### POST /feedback

After your AI replies, send the exchange back so cc-soul can learn.

```bash
curl -X POST http://localhost:18800/feedback \
  -H "Content-Type: application/json" \
  -d '{"user_message": "Fix this timeout", "ai_reply": "The issue is...", "user_id": "alice", "satisfaction": "positive"}'
```

### POST /command

Execute any cc-soul command via API.

```bash
curl -X POST http://localhost:18800/command \
  -H "Content-Type: application/json" \
  -d '{"message": "search memory deploy", "user_id": "alice"}'
```

### POST /features

```bash
# Enable a feature
curl -X POST http://localhost:18800/features \
  -H "Content-Type: application/json" \
  -d '{"feature": "debate", "enabled": true}'
```

### POST /soul

Soul mode — cc-soul calls LLM and replies as the user's avatar. Requires LLM configured via `POST /config`.

```bash
curl -X POST http://localhost:18800/soul \
  -H "Content-Type: application/json" \
  -d '{"message": "What do you think about Rust?", "user_id": "alice"}'
# → {"reply": "I'd stick with Python unless profiling proves CPU-bound...", "persona": "engineer"}
```

### GET /profile

```bash
curl http://localhost:18800/profile
# → {"avatar": {...}, "social": [...], "identity": "...", "thinkingStyle": "...", "mood": 0.6, "energy": 0.8}
```

### GET /health

```bash
curl http://localhost:18800/health
# → {"status": "ok", "version": "2.5.0", "memories": 5231, "uptime": 3600}
```

### GET /avatar

Returns a system prompt that makes any LLM respond as the user. Feed this to your AI's system message.

```bash
curl "http://localhost:18800/avatar?sender=colleague&message=How%20should%20we%20deploy%20this"
# → {"prompt": "You are cc. You think like a pragmatic backend engineer who prioritizes stability...", "userId": "default"}
```

### GET /soul-spec

```bash
curl http://localhost:18800/soul-spec
# → {"soul_json": {...}, "style": "# cc 说话风格...", "identity": "# cc 的身份...", "heartbeat": "# cc 心跳..."}
```

---

## How Other AIs Connect

### Basic Flow (any HTTP client)

```
1. POST /process  { message, user_id }     → get system_prompt + augments
2. Feed system_prompt + augments to your AI  → AI generates reply
3. POST /feedback { user_message, ai_reply } → cc-soul learns
```

That's it. Three calls per message. Your AI gets persistent memory without changing anything else.

### Claude Code (MCP)

```bash
# List available tools
curl http://localhost:18800/mcp/tools
# → cc_memory_search, cc_memory_add, cc_soul_state, cc_persona_info

# Call a tool
curl -X POST http://localhost:18800/mcp/call \
  -H "Content-Type: application/json" \
  -d '{"tool": "cc_memory_search", "args": {"query": "deploy"}}'
```

### A2A Protocol

```bash
# Get agent card
curl http://localhost:18800/.well-known/agent.json

# Send agent-to-agent request
curl -X POST http://localhost:18800/a2a \
  -H "Content-Type: application/json" \
  -d '{"capability": "memory-recall", "params": {"query": "server setup"}}'
```

### Python Example

```python
import requests

API = "http://localhost:18800"

# Step 1: Get augmented context
ctx = requests.post(f"{API}/process", json={
    "message": "What's my server config?",
    "user_id": "alice"
}).json()

# Step 2: Feed to your AI (OpenAI example)
from openai import OpenAI
client = OpenAI()
reply = client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {"role": "system", "content": ctx["system_prompt"] + "\n\n" + ctx["augments"]},
        {"role": "user", "content": "What's my server config?"}
    ]
).choices[0].message.content

# Step 3: Feedback
requests.post(f"{API}/feedback", json={
    "user_message": "What's my server config?",
    "ai_reply": reply,
    "user_id": "alice"
})
```

---

## Core Scenarios

### 1. Proactive Expansion + Association Chain

```
You:  "Fix this connection timeout error"
AI:   Fixes the error. Then:
      "I noticed 3 related issues:
       1. Your connection pool is 5 but you run 20 workers — bottleneck
       2. No retry logic on transient failures
       3. The timeout is 3s but your DB query p99 is 2.8s"
```

cc-soul doesn't just answer — it recalls your past context and anticipates what you'll need next. It expands across 22 tech domains and 11 life domains.

### 2. Socratic Teaching

```
You:  "帮我理解 what is a closure?"
AI:   Doesn't give the answer. Instead:
      "When a function returns another function, what happens
       to the variables in the outer scope? Try this:
       function outer() { let x = 1; return () => x; }
       What do you think outer()() returns, and why?"
```

Trigger words: `帮我理解`, `引导我`, `教我`, `别告诉我答案`, `guide me`, `help me understand`

### 3. Correction Verification

```
You:  "Python's GIL was removed in 3.11. Update your knowledge."
AI:   "I'm not going to update that — it's not accurate.
       PEP 703 shipped in Python 3.13, not 3.11. Even in 3.13,
       the GIL is still the default. I verify corrections
       before accepting them."
```

When you say "that's wrong", cc-soul checks facts first. If you're right, it learns. If you're wrong, it counters with evidence. Rules are verified over 3 conversations before becoming permanent (Bayesian hypothesis testing).

### 4. Persistent Memory

```
You:  "I deployed the backend on AWS us-east-1, port 8080, Python 3.11"
AI:   Stored.

[3 weeks later]

You:  "What server did I set up?"
AI:   "AWS us-east-1, port 8080, Python 3.11 — about 3 weeks ago."
```

10,000+ memories with auto-dedup, contradiction detection, 3-tier time decay (HOT/WARM/COLD), and smart forget (Weibull survival + ACT-R activation).

### 5. Emotion + 11 Personas

```
You:  "This code keeps segfaulting"
AI:   [engineer mode] Analyzes the crash, suggests fixes.

You:  "I'm stressed about the deadline"
AI:   [comforter mode] Acknowledges feelings, offers practical help.

You:  "Should I use Redis or Memcached?"
AI:   [analyst mode] Compares both with pros/cons.
```

5-dimension emotion model (Pleasure/Arousal/Dominance/Certainty/Novelty). 11 personas auto-switch by context: engineer, friend, mentor, analyst, comforter, strategist, explorer, executor, teacher, devil's advocate, socratic.

### 6. Life Assistant

```
You:  "打卡 exercise"          → "Exercise: Day 12 streak!"
You:  "新目标 Launch MVP by April" → Goal created with progress tracking.
You:  "提醒 09:00 standup"      → Daily reminder set.
You:  "晨报"                   → Morning briefing with goals, reminders, mood.
You:  "周报"                   → Weekly review with trends and insights.
```

---

## What's Running Behind the Scenes (43 always-on features)

All automatic. No setup, no toggles. Works from the first message.

**Memory (NAM Engine)**
- Every message auto-extracted for facts, preferences, events
- NAM 6-signal activation field — memories surface by relevance, not keyword match
- Contradiction detection — catches when you contradict yourself
- Smart decay (Weibull + ACT-R) — unused memories fade, important ones strengthen
- Memory compression — 90-day+ memories distilled into summaries
- WAL protocol — key facts persisted before AI replies (crash-safe)
- DAG archive — lossless archival replaces hard deletion
- Associative recall — one memory activates related ones
- Predictive recall — pre-warms memories based on conversation patterns

**Understanding**
- 7-dimension deep understanding — temporal patterns, growth trajectory, cognitive load, stress fingerprint, say-do gap, unspoken needs, dynamic profile
- Theory of mind — tracks your beliefs, knowledge gaps, frustrations
- Decision causal recording — stores WHY you chose, not just what
- Value conflict learning — tracks which values you prioritize in tradeoffs
- Social context adaptation — adjusts tone when you mention boss vs friend
- Person model — identity, thinking style, communication decoder ("算了" → "换个角度")
- Expression fingerprint — learns your argument style and certainty level

**Personality & Emotion**
- 11 personas auto-blend by context — like a friend who adjusts their tone naturally
- Emotion tracking — senses your mood from messages, adapts in real time
- Your mood affects AI's mood — just like talking to a real person
- Late night = concise replies, Monday morning = gentle start

**Proactive Intelligence**
- Proactive expansion — adds related pitfalls after answering
- Absence detection — notices topics you stopped mentioning
- Behavior prediction — warns when you're about to repeat a past mistake
- Proactive questioning — asks about knowledge gaps it detects
- Follow-up tracking — "I'll do it tomorrow" → next day reminder
- Soul moments — naturally references shared history at milestones

**Infrastructure**
- Knowledge graph — entities and relationships auto-extracted
- Context compression — long conversations auto-compressed to save tokens
- Quality scoring — learns which response style works for you
- LLM batch queue — non-urgent tasks queued for off-hours
- Habit/goal/reminder tracking
- Cost tracking — token usage per conversation

**6 optional features** (user can toggle): `auto_daily_review` · `self_correction` · `memory_session_summary` · `absence_detection` · `behavior_prediction` · `auto_mood_care`

---

## Quick Commands

| Command | What it does |
|---------|-------------|
| `help` / `帮助` | Full command guide |
| `stats` | Dashboard — messages, memories, quality, mood |
| `soul state` | AI energy, mood, emotion vector |
| `我的记忆` / `my memories` | View recent memories |
| `搜索记忆 <词>` / `search memory <kw>` | Search memories |
| `删除记忆 <词>` / `delete memory <kw>` | Remove matching memories |
| `pin 记忆 <词>` / `pin memory <kw>` | Pin memory (never decays) |
| `隐私模式` / `privacy mode` | Pause all memory storage |
| `打卡 <习惯>` / `checkin <habit>` | Track a habit |
| `新目标 <描述>` / `new goal <desc>` | Create a goal |
| `提醒 HH:MM <消息>` / `remind HH:MM <msg>` | Daily reminder |
| `情绪周报` / `mood report` | 7-day emotional trend |
| `能力评分` / `capability score` | Domain confidence scores |
| `摄入文档 <路径>` / `ingest <path>` | Import document to memory |
| `知识图谱` / `knowledge map` | Visualize knowledge graph |
| `features` / `功能状态` | View feature toggles |
| `开启 <X>` / `关闭 <X>` | Enable/disable feature |
| `dashboard` / `仪表盘` | Open web dashboard |
| `cost` / `成本` | Token usage statistics |
| `audit log` / `审计日志` | View audit trail |
| `导出全部` / `export all` | Full backup: memories + persona + values + rules to one JSON |
| `导入全部 <路径>` / `import all <path>` | Restore from full backup |
| `别记这个` / `don't remember` | Skip storing next message to memory |
| `人格列表` / `personas` | List all 11 personas |
| `价值观` / `values` | Show value priorities |

---

## All Commands

**Memory:** `我的记忆` · `搜索记忆 <词>` · `删除记忆 <词>` · `pin 记忆 <词>` · `unpin 记忆 <词>` · `记忆时间线 <词>` · `记忆健康` · `记忆审计` · `恢复记忆 <词>` · `记忆链路 <词>` · `共享记忆 <词>` · `私有记忆 <词>`

**Import/Export:** `导出全部` · `导入全部 <路径>` · `导出lorebook` · `导出进化` · `导入进化 <路径>` · `摄入文档 <路径>`

**Daily Life:** `打卡 <习惯>` · `习惯状态` · `新目标 <描述>` · `目标进度 <目标> <更新>` · `我的目标` · `提醒 HH:MM <消息>` · `我的提醒` · `删除提醒 <序号>`

**Status:** `stats` · `soul state` · `晨报` · `周报` · `情绪周报` · `能力评分` · `成长轨迹` · `我的技能` · `metrics` · `cost` · `dashboard` · `记忆图谱 html` · `对话摘要`

**Insights:** `时间旅行 <词>` · `推理链` · `情绪锚点` · `记忆链路 <词>`

**Experience:** `讲讲我们的故事` · `每日复盘` · `保存话题` · `切换话题 <名称>` · `话题列表`

**Persona & Values:** `人格列表` · `价值观` · `别记这个`

**Advanced:** `功能状态` · `开启 <功能>` · `关闭 <功能>` · `审计日志` · `开始实验 <描述>`

---

## NAM — Neural Activation Memory

cc-soul's original memory engine. Memories aren't "searched" — they surface automatically, like the human brain. 6 signals computed simultaneously:

```
① Base activation     — frequency + recency (ACT-R model)
② Context match       — AAM word expansion + BM25 + trigram fusion
③ Emotional resonance — current mood × memory emotion (PADCN cosine)
④ Spreading activation — related memories boost each other
⑤ Interference        — similar memories compete, strongest wins
⑥ Temporal context    — time-of-day encoding specificity
```

One system, no layers, no degradation, no external models. NAM learns your personal semantic connections from usage — gets smarter the more you talk.

---

## Privacy & Security

All data stored locally (`~/.cc-soul/data/` or `~/.openclaw/plugins/cc-soul/data/` if using OpenClaw). Auto-detected, auto-created. Nothing ever leaves your machine. No telemetry.

- **Privacy mode** — say `隐私模式` to pause all memory storage
- **PII filtering** — auto-strips emails, phone numbers, API keys, IPs
- **Prompt injection detection** — 9 pattern filters
- **Immutable audit log** — SHA256 chain-linked operation history
- **MCP rate limiting** — prevents abuse from external agents

[SECURITY.md](https://github.com/wenroudeyu-collab/cc-soul/blob/main/SECURITY.md)

---

**NAM memory engine · 50+ commands · 49 feature toggles · 15 API endpoints · 11 personas · 7-dimension user model · no external models needed**

[npm](https://www.npmjs.com/package/@cc-soul/openclaw) · [GitHub](https://github.com/wenroudeyu-collab/cc-soul) · Issues: [github.com/wenroudeyu-collab/cc-soul/issues](https://github.com/wenroudeyu-collab/cc-soul/issues) · wenroudeyu@gmail.com

*Your AI, but it actually knows you.*
