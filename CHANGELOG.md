# Changelog

All notable changes to cc-soul will be documented here.

## [2.9.0] - 2026-03-31

### Changed — Core Memory Engine
- **Refactored to NAM — Neural Activation Memory（神经激活记忆）**: cc-soul original algorithm. Memories aren't "searched" — they surface automatically via 6 simultaneous signals (base activation, context match, emotional resonance, spreading activation, interference suppression, temporal context). Replaces the previous 4-layer cascade (hash → AAM → BM25 → vector).
- **Vector search retired**: NAM's learned associations cover semantic matching without external models. Embedder.ts reduced to no-op stub. No 90MB model download needed.
- **dream_mode and curiosity scope removed**: dead code cleaned up from prompt-builder, features no longer references dream_mode.
- **Auto data directory**: `~/.cc-soul/data/` auto-created for non-OpenClaw users. No configuration, no environment variables.

## [2.5.0] - 2026-03-30

### Added
- Absence detection — detects topics user stopped mentioning, proactively asks
- Behavior prediction — warns based on past patterns when trigger situations detected
- Deep understanding engine — 7 dimensions: temporal patterns, say-do gap, growth trajectory, unspoken needs, cognitive load, stress fingerprint, dynamic profile
- Causal chain in reasoning — 推理链 now traces cause → root cause + counterfactual
- Decision causal recording — memories store WHY (because field), not just what
- Value conflict priority — learns which values user prioritizes in tradeoffs
- Social context switching — tracks communication style per social relationship
- "What would you do" prediction — predicts user decisions from past patterns
- Avatar soul injection — GET /avatar returns prompt to make LLM respond as user
- Expression fingerprint — tracks argument style, evidence preference, certainty level
- Memory tiered compression — 90-day+ memories auto-distilled, originals archived
- Memory narrative distillation — 20 recalled memories compressed to 1 paragraph, ~50% token savings
- Portable full backup — `导出全部` / `import all` exports entire soul to one JSON file
- Proactive blind spot questioning — detects knowledge gaps, asks to fill them
- LLM batch queue — non-urgent LLM tasks queued for 2-5 AM processing
- GET /avatar endpoint — soul injection prompt for external AI integration
- GET /soul-spec endpoint — returns soul spec files dynamically

### Fixed
- behavior_prediction toggle now properly wired
- install.js default features synced with current features.ts
- Version consistency across all files

### Changed
- 12 dead modules fully deleted (upgrade/rover/telemetry/federation/sync etc.)
- 33 new regression tests (total: 126)
- Token budget increased from 2000 to 3500

## [2.4.0] - 2026-03-30

### Added — New Cognitive Abilities
- **Absence detection**: detects topics user used to discuss but stopped mentioning, proactively asks (e.g. "你最近都没提跑步了"). Three-tier welcome: 4h/3d/7d+
- **Behavior prediction**: detects trigger patterns (deadline pressure, late night, frustration) and warns based on past outcomes (e.g. "上次你熬夜硬扛之后效率低了三天")
- **Causal chain in reasoning**: 推理链 now traces cause chains (result ← cause ← root cause) + counterfactual ("如果当时不这样...")

### Added — New Commands (19 total since v2.2)
- `成长轨迹` / `growth` — growth vector (correction rate, rules, quality)
- `时间旅行 <keyword>` — trace how opinions evolved over time
- `情绪锚点` / `emotion anchors` — topics correlated with positive/negative mood
- `记忆链路 <keyword>` / `memory chain` — memory association chain
- `讲讲我们的故事` / `our story` — relationship narrative
- `每日复盘` / `daily review` — today's conversations/decisions/actions
- `保存话题` / `切换话题` / `话题列表` — topic branch management
- `共享记忆` / `私有记忆` — memory visibility control
- `恢复记忆 <keyword>` — restore archived memories
- `别记这个` — skip next memory storage
- `当聊到...时提醒我...` — context-triggered reminders
- `导出进化` / `导入进化` — GEP format evolution export/import
- `安装向量` / `向量状态` — one-click vector search install + status

### Added — New Feature Toggles (21)
- `wal_protocol` — Write-Ahead Logging before AI reply
- `dag_archive` — lossless archive replaces hard decay
- `persona_drift_detection` — per-reply persona drift detection
- `rhythm_adaptation` — reply length adapts to user pace
- `trust_annotation` — domain trust level hints
- `self_correction` — post-reply quality self-check
- `predictive_memory` — time-slot topic prediction
- `scenario_shortcut` — correction history quick hints
- `context_reminder` — keyword-triggered context reminders
- `auto_time_travel` — auto-search history on "以前/上次"
- `auto_contradiction_hint` — proactively point out contradictions
- `auto_mood_care` — emotional care on sustained low mood
- `auto_topic_save` — auto-save topic on shift
- `auto_memory_chain` — graph 1-hop expansion
- `auto_repeat_detect` — cite past conclusions for similar questions
- `behavior_prediction` — predict user behavior from patterns
- `absence_detection` — detect disappeared topics
- Plus: `auto_memory_reference`, `auto_natural_citation`, `auto_daily_review`

### Added — New Automatic Behaviors (~27)
- Emotion anchors tracking (topic ↔ mood correlation)
- WAL protocol (persist key info before AI reply)
- Auto topic save on conversation shift
- Self-correction notification (quality ≤3 → DM owner)
- Persona drift rule-based detection per reply
- Contradiction proactive hint
- Time travel auto-trigger on "以前/上次"
- Repeat question detection + cite past conclusion
- Sustained low mood care (2+ days → care augment)
- Causal chain injection for correction memories
- Commitment tracking ("我要/我打算" → 7-day reminder)
- Silence analysis (topics user never discusses in related domains)
- Cognitive blind spot warning (domain with ≥2 corrections)
- Adaptive reply depth (adjust by domain conversation count)
- Expression fingerprint adaptation (match user avgLength/topWords)
- Generative inference (infer preferences for new domains from values)

### Removed — Modules (12 deleted)
- `upgrade.ts`, `upgrade-meta.ts`, `upgrade-experience.ts` — self-upgrade (never used)
- `competitive-radar.ts`, `rover.ts` — competitive analysis / web roaming (never used)
- `telemetry.ts`, `federation.ts`, `sync.ts` — telemetry / federation / sync (never used)
- `debate.ts`, `llm-judge.ts`, `skill-extract.ts`, `voice.ts` — group chat / LLM judge / skill extract / voice (removed)

### Removed — Feature Toggles (15)
- `dream_mode`, `autonomous_voice`, `structured_reflection`, `strategy_replay`, `meta_learning`, `reflexion`, `self_challenge`, `debate`, `llm_judge`, `skill_extract`, `web_rover`, `tech_radar`, `federation`, `sync`, `telemetry`

### Fixed (12 bugs)
- WAL double-write: non-command messages extracted twice
- fetch monkey-patch: debug interceptor left in production
- `切换话题` path traversal: user input could read files outside data dir
- `saveHypothesesSafe` blocked legitimate empty saves
- embedder `_syncEmbedder()` not called on failure path
- Hypothesis verification threshold too low (1→3) + trigram similarity too loose (0.05→0.3)
- LLM Reranker used stale cache from previous turn
- Async vector callback modified returned sync array
- `_lastVectorResults` global cache no user isolation
- Sleep consolidation O(n²) — capped at 200 memories
- Direct mode `恢复记忆` conflicted with privacy mode
- `blindSpotAnalysis` filter conditions redundant/ineffective

### Fixed — Toggle Wiring (12)
- 12 feature toggles existed in DEFAULTS but had no `isEnabled()` checks — user could toggle off but feature kept running. All now properly wired.

### Changed
- Core direction: removed all "AI self-entertainment" modules (dream/reflection/self-challenge/debate), redirected token budget to user-facing features
- 62 modules → 50 modules (12 deleted), all obfuscated
- `memory_llm_rerank` toggle removed (dead feature)

## [1.8.0] - 2026-03-23

### Added
- **Import memories command**: "导入记忆 <path>" / "import memories <path>" — import from JSON file with auto-dedup
- **Import soul command**: "导入灵魂 <path>" / "import soul <path>" — import soul config (features + values), restart to apply
- **Correction auto-verify**: when user says "you're wrong", AI now verifies before accepting or rejecting — uses evidence to counter incorrect corrections
- **MCP Tool Provider**: 4 tools (cc_memory_search, cc_memory_add, cc_soul_state, cc_persona_info) for other AI agents
- **Chain-of-Thought Memory**: memories store reasoning process (context → conclusion), auto-extracted from "because...therefore" patterns
- **Graph Walk Recall**: BFS on entity-relation graph discovers memories through knowledge links
- **Image/Screenshot Memory**: auto-detects image context, stores as visual scope
- **Document Ingestion v2**: "摄入文档"/"ingest <path>" — Markdown/code/text smart chunking
- **8 new commands**: search/delete/import memory, import soul, conversation summary, memory health, metrics, ingest
- **Context-aware augment budget**: technical → rules +2, emotional → persona +2, casual → all -1
- **Rule compression**: auto-merge similar rules when >40 (trigram >0.6)
- **Incremental sync + CRDT**: only exports changed memories, last-write-wins + tag union merge
- **Full language auto-follow**: replies in whatever language user writes in (was hardcoded Chinese)
- **5 E2E tests** (total 40), metrics collection, section maps for module splitting

### Fixed — P0 Crashes
- **memory.ts missing readFileSync + homedir imports**: saveMemories and hybrid recall would crash
- **handler.ts missing homedir + resolve**: import memory/soul commands would crash
- **handler.ts augments TDZ**: correction verification pushed to augments before declaration — correction handling was completely broken
- **context-prep.ts command injection**: grep exec with unescaped user input
- **Double runPostResponseAnalysis**: same turn analyzed twice, now deduped with _lastAnalyzedPrompt flag
- **recallFeedbackLoop writing to copy**: feedback tags never persisted, now writes to memoryState.memories directly
- **auto-tune.ts bandit reward unbounded**: reward now clamped to [0,1]
- **upgrade.ts git missing crash**: now checks git availability before use, skips gracefully

### Fixed — P1 Data Integrity
- **Eviction index corruption**: queueForTagging no longer uses index, uses content+ts matching
- **addMemoryWithEmotion skip path**: emotion was set on wrong memory after dedup skip
- **Consolidation race condition**: filter+push replaced with in-place splice to prevent concurrent recall seeing empty array
- **FTS5 SQL injection**: MATCH query now strips * ( ) { } ^ ~ < > | \\ AND OR NOT NEAR
- **sqlite-store embedding non-atomic**: DELETE+INSERT wrapped in BEGIN/COMMIT/ROLLBACK transaction
- **evolution.ts sort instability**: added ts tiebreaker for equal scores
- **quality.ts AdaGrad double decay**: L2 lambda reduced 0.001 → 0.0001
- **auto-tune.ts gammaSample NaN**: alpha/beta clamped to min 0.01

### Fixed — Other
- **Daemon killed by cleanup**: disabled killGatewayClaude (OpenClaw 3.22 manages CLI lifecycle)
- **Concurrency alert spam**: disabled permanently (unnecessary with sessionMode=always)
- **CLI priority restored**: Persistent CLI > Haiku API > One-shot (was accidentally reversed)

### Performance
- **contentIndex Map**: decideMemoryAction O(1) exact match before O(n) trigram scan, limited to last 200
- **scopeIndex in recall()**: batch skip expired/decayed without per-item check
- **clusterByTopic capped**: O(n²) limited to most recent 100 entries
- **Graph walk Map lookup**: replaced .find() with pre-built contentMap
- **buildIDF throttled**: max once per 60 seconds
- **Trigram LRU cache**: 500 entries, avoids recomputation
- **Metrics deduplication**: metrics.totalMessages is now a getter referencing stats.totalMessages

### Changed
- OpenClaw 3.22 verified compatible, sessionMode=always configured
- 51 modules, 22,000+ lines, all obfuscated
- Soul prompt fully internationalized (English instructions, model auto-follows user language)


## [1.7.0] - 2026-03-23

### Added — New Features
- **Chain-of-Thought Memory**: memories now store reasoning process (context → conclusion), auto-extracted from "because...therefore" patterns in conversation
- **Graph Walk Recall**: BFS traversal on entity-relation graph to discover memories connected through knowledge links, supplements text-based recall
- **Image/Screenshot Memory**: auto-detects image context in messages ([Image:...]), stores as visual scope memories
- **Document Ingestion v2**: "摄入文档" / "ingest <path>" command — supports Markdown (split by ##), code files (split by function/class), plain text (split by paragraph), max 50 chunks
- **MCP Tool Provider**: exposes cc-soul as MCP tools (cc_memory_search, cc_memory_add, cc_soul_state, cc_persona_info) for other AI agents to query
- **Memory Search Command**: "搜索记忆 <keyword>" / "search memory <keyword>" — precision search through memories
- **Memory Delete Command**: "删除记忆 <keyword>" / "delete memory <keyword>" — mark matching memories as expired
- **Conversation Summary Command**: "对话摘要" / "conversation summary" — shows recent session summaries grouped by 30-min intervals
- **Memory Health Command**: "记忆健康" / "memory health" — shows memory count, scope distribution, confidence spread, decay stats
- **Metrics/Monitoring Command**: "metrics" / "监控" — shows total messages, avg response time, recall calls, CLI calls, augments injected
- **Context-Aware Augment Budget**: technical questions boost rules/epistemic priority +2, emotional questions boost persona/emotion +2, casual reduces all -1
- **Rule Compression**: when rules exceed 40, auto-merges similar rules (trigram similarity >0.6), keeps the one with higher hit count
- **Incremental Sync**: sync export now supports "since" parameter, only exports memories modified after last sync (was full export every time)
- **CRDT Conflict Resolution**: multi-device sync now uses last-write-wins + tag union merge for conflict resolution
- **5 E2E Tests**: full message lifecycle, correction flow, dedup+compression, active commands, emotional flow (total tests: 40)
- **Metrics Collection**: in-memory counters for messages, response time, recall calls, CLI calls, augments — zero persistence overhead
- **Section Maps**: memory.ts (17 sections) and handler.ts (10 sections) annotated with navigation comments for future module splitting

### Added — Language & UX
- **Full language auto-follow**: soul prompt now instructs model to reply in whatever language user writes in (was hardcoded Chinese)
- **Soul prompt internationalized**: speaking style rules rewritten in English (model auto-translates behavior to user's language)
- **narrativeCache TTL**: auto-clears after 1 hour (was never released)

### Fixed — P0 Data Risks
- **recallStats unbounded growth**: now resets every 1000 queries, preserves last-cycle rate as fallback
- **workingMemory Map unbounded**: added LRU cap at 100 sessions, oldest evicted on overflow
- **Session eviction FIFO→LRU**: now evicts by lastAccessed timestamp instead of insertion order
- **batch-tag race condition**: changed from content-only matching to index→ts+content→content three-tier fallback
- **soul.json version mismatch**: synced to 1.7.0

### Fixed — P1 Logic Issues
- **agentBusy timeout 30s→60s**: complex code generation can exceed 30s, background tasks no longer preempt prematurely
- **rules/hypotheses export let reference**: all array truncations changed from reassignment to in-place modification (splice/length=N), fixing stale import references
- **addMemoryWithEmotion emotion loss**: emotion was silently lost when addMemory decided to skip (dedup). Rewritten to use ts+content positioning
- **Augment duplicate selection**: selectAugments now deduplicates by content at entry, preventing same augment selected twice across categories

### Changed
- 50+ modules, 21,000+ lines
- New file: mcp-provider.ts (69 lines) — MCP tool definitions
- rag.ts rewritten: chunkText routes to chunkMarkdown/chunkCode/chunkByParagraph by file extension
- sync.ts: resolveConflict() + incremental export + CRDT merge on import


## [1.6.0] - 2026-03-23

### Added
- **New user onboarding**: first-time users get a guided 3-question conversation to build initial profile and memory baseline
- **Multi-language support**: auto-detects English messages and switches soul prompt to English mode
- **"我的记忆" / "my memories" command**: view the 20 most recent memories AI has about you
- **"导出记忆" / "export memories" command**: export all your memories to JSON file
- **"导出灵魂" / "export soul" command**: export soul config (prompt + features + personas + values) for sharing or backup
- **Recall rate visualization**: stats command now shows memory recall success rate
- **Personality growth curve**: stats command shows persona usage histogram with trend analysis (e.g. "主要以工程师模式互动")
- **Persona usage tracking**: every persona selection logged per user for growth visualization
- **OpenClaw hybrid recall**: recall() now queries OpenClaw native FTS5 memory as secondary source, merges results with cc-soul's TF-IDF/trigram
- **OpenClaw 3.22 verified**: full compatibility confirmed — plugin loading, hooks, SOUL.md injection all working

### Fixed
- **SOUL.md content pollution**: core_memory.json had 12 garbage entries ([goal completed], [Working Memory], Rating leaks, persona snapshots) — cleaned data + added reject filter in promoteToCore()
- **System augment memory leak**: addMemory() now rejects content with system prefixes ([Working Memory], [当前面向], [System], etc.)
- **saveMemories() crash protection**: refuses to overwrite non-empty file with empty array (prevents data loss on process kill)
- **Relationship "relatively new" never updating**: familiarity was always 0 — now grows with messageCount (50 msgs → fully familiar)
- **getBlendedPersonaOverlay double selection**: was calling selectPersona() again without intent/msg, always picking analyst — now uses activePersona directly
- **New persona trigger too weak**: extended triggers (planning/learning/curiosity) now override vector similarity when detected from message content
- **"我的记忆" intercepted by upgrade system**: new commands now skip upgrade check to avoid false interception
- **type-mismatch NaN**: Date.now() - m.ts returns NaN when ts is string/undefined (handler.ts, rover.ts)
- **unguarded JSON.parse**: sqlite-store.ts row.tags parsing could crash
- **Claude concurrency false alarm**: excluded cc-soul daemon processes from count, threshold 8→3
- **Feishu notification spam**: notifySoulActivity now console.log only

### Changed
- SOUL.md architecture overhaul: 35.9KB → 5.1KB (86% reduction, 864 tokens)
- Dynamic content (memories, reflections, hypotheses, dreams, entity graph, rover, workflows, user model, values) moved from SOUL.md to per-message augment injection
- 5 new personas (total 10): Strategist, Explorer, Executor, Teacher, Devil's Advocate
- Context protection: 70%/85%/95% three-tier threshold
- Prompt injection detection: 9 regex patterns
- Competitive radar v2: dynamic inventory scan + 10 competitors + comparison matrix
- SoulSpec compatibility: soul.json + STYLE.md + IDENTITY.md + HEARTBEAT.md + SECURITY.md
- Owner-only features hidden from customers
- All 50 modules fully obfuscated (build.sh auto-scan, no manual lists)
- Memory recall: ts=0 repair (4986 memories) + double decay removed + RECALL_UPGRADE_COUNT 2→1 + archival reactivation + multi-route scoring
- Byte-based SOUL.md truncation (19KB limit) for Chinese UTF-8


## [1.5.0] - 2026-03-23

### Major: SOUL.md Architecture Overhaul
- **SOUL.md 86% size reduction**: 35.9KB → 5.1KB (864 tokens, was 4177)
- Dynamic content (memories, reflections, hypotheses, entity graph, dreams, rover, workflows, user model, value guidance) moved from SOUL.md to per-message augment injection
- SOUL.md now contains only: identity + values + speaking style + 举一反三 rules + commands + body state + current speaker
- Byte-based truncation (19KB limit) replaces char-based, correctly handles Chinese UTF-8

### Added
- **5 new personas** (total 10): Strategist (军师), Explorer (探索者), Executor (执行者), Teacher (导师), Devil's Advocate (魔鬼代言人) — all auto-selected by conversation context
- **Extended trigger detection**: message content analysis for planning/learning/curiosity keywords triggers specialized personas
- **Context protection**: 70%/85%/95% three-tier threshold — auto checkpoint at 70%, reduce augments at 85%, emergency trim at 95%
- **Prompt injection detection**: 9 regex patterns (EN/CN) detect injection attempts, inject security warning as augment
- **SECURITY.md**: security framework documentation (data policy, PII filtering, privacy mode, vulnerability reporting)
- **SoulSpec compatibility**: soul.json + STYLE.md + IDENTITY.md + HEARTBEAT.md
- **saveMemories guard**: refuses to overwrite non-empty file with empty array (prevents data loss on crash)
- **Core memory filter**: rejects system augment content ([Working Memory], [当前面向], [goal completed], Rating, etc.)
- **Familiarity tracking**: auto-grows with interaction count (50 messages → fully familiar)

### Fixed
- **Memory recall collapse** (P0): 4986 memories with ts=0 caused age calculation to return 20000+ days → all marked decayed → recall pool collapsed from 5000 to 19 active. Fixed: one-time ts repair + processMemoryDecay null-safe + RECALL_UPGRADE_COUNT 2→1
- **Double decay in recall()** (P0): recency × timeDecay was double-penalizing old memories by 55%. Removed timeDecay multiplication
- **SOUL.md 35.9KB overflow** (P0): exceeded OpenClaw's 20K workspace injection limit. Fixed: byte-based truncation + content migration to augments
- **loadFeatures() overwrite** (P0): every gateway restart overwrote user's feature toggles with defaults. Fixed: only add missing keys, never overwrite existing values
- **getBlendedPersonaOverlay double selection**: was calling selectPersona() again without intent/msg, always picking analyst. Fixed: use activePersona directly
- **Core memory pollution**: system augments ([Working Memory], [goal completed], Rating, persona snapshots) leaked into core_memory.json. Fixed: reject filter + data cleanup
- **Relationship "relatively new"**: familiarity was never updated (always 0). Fixed: grows with messageCount
- **type-mismatch NaN**: Date.now() - m.ts returned NaN when ts was string/undefined (handler.ts, rover.ts)
- **unguarded JSON.parse**: sqlite-store.ts row.tags parsing could crash
- **Claude concurrency false alarm**: excluded cc-soul's own daemon processes from count, threshold 8→3
- **Feishu notification spam**: notifySoulActivity now console.log only, no longer pushes to Feishu group

### Changed
- Owner-only features (self_upgrade, tech_radar, competitive_radar) hidden from customer feature list and chat toggle
- Competitive radar v2: dynamic inventory scan + 10 competitors + comparison matrix
- 4 cognitive modules rewritten: metacognition (419 lines), context-prep (320), patterns (371), meta-feedback (366)
- Archival memory reactivation: decayed memories auto-revive when active recall < 3 results
- Multi-route recall scoring: tag×1.0 + trigram×0.5 + BM25×0.7 cumulative
- Batch tag priority: active memories first, batch size 5→10
- 50 modules, 20,000+ lines, all obfuscated for npm distribution



## [1.4.0] - 2026-03-23

### Architecture
- Migrated from hook to pure OpenClaw plugin (`kind: context-engine`)
- Auto-creates hook bridge at `~/.openclaw/hooks/cc-soul-hook/` for CLI backend compatibility
- SOUL.md file injection for soul prompt (never truncated by bootstrap file limits)
- Plugin auto-configures `openclaw.json` on install (plugin paths, allow list, entries)
- Install script deploys to `~/.openclaw/plugins/` (was `hooks/`); auto-migrates existing data

### Added (P0 — memory engineering)
- Entity/relation time-series management (`valid_at`/`invalid_at`, auto-invalidate 90d stale)
- Memory CRUD decision engine (ADD/UPDATE/SKIP via trigram similarity >0.7 dedup)
- Memory semantic compression (8 regex patterns reduce storage noise)
- Auto-insights during memory consolidation (behavioral pattern discovery from merged memories)

### Added (P1 — features)
- Memory with Search: rover enriches web search results with user preferences and context
- Mermaid memory map visualization (`generateMemoryMap`) — viewable knowledge graph
- Enhanced memory stats: scope distribution, 7-day rolling count, top 5 topics
- 3-tier time-decay memory: `short_term` → `mid_term` → `long_term` with `processMemoryDecay`
- LLM-driven memory operations: auto update/delete based on conversation context
- 举一反三 expanded to 22 domain detectors + 11 life domains (was tech-only)
- Tier-aware responses: patient friendly tone for new users, natural style for owner

### Added (infrastructure)
- Haiku API fallback for background tasks (~$0.06/day)
- Persistent CLI daemon (zero-cost background processing without API calls)
- Execution priority chain: persistent CLI > Haiku API > one-shot CLI
- Context Engine registered via `api.registerContextEngine()` (ready for future OpenClaw CE support)

### Fixed
- 180s timeout: background tasks no longer compete with agent CLI process
- `message:sent` unreliable in plugin mode: post-response analysis moved to N+1 turn
- Soul prompt truncation: SOUL.md bypasses bootstrap file size limits (written to workspace)
- `agentBusy` timeout: reduced 200s → 30s safety release
- CLI `--no-input` flag removed (unsupported in current claude CLI)
- Prompt self-contradiction: core values rewritten to reinforce rather than suppress 举一反三

### Changed
- `federation` + `sync` default OFF (no server required for standalone use)
- `telemetry` default ON but can be disabled (`disable telemetry`)
- Data directory auto-detects `plugins/` or `hooks/` path
- Install script deploys to `~/.openclaw/plugins/` (was `hooks/`)
- 120 user-visible features (was 42)
- 50 modules, 18,062 lines (was 45 modules, 15K lines)

## [1.3.0] - 2026-03-22

### Added
- 举一反三 v3: mandatory output structure with few-shot examples
  - 22 domain-specific detectors (Python/JS/iOS/RE/DB/Docker/Git/HTTP/File/AI/Linux/Architecture + career/finance/health/education/travel/shopping/cooking/relationships/legal/housing/pets)
  - 8 general pattern detectors (how-to/comparison/advice/trouble/opinion/long-msg)
  - Non-tech domains: career, finance, health, education, travel, shopping, cooking, relationships, legal, housing, pets
  - Tier-aware tone: new/known users get patient friendly responses, owner gets natural style
- Complete feature documentation: 42 user-visible features documented (was 30)

### Fixed
- Prompt self-contradiction: "别写论文" and "老板时间重要" were suppressing 举一反三
- Session bootstrap: changes require session reset to take effect (documented)

### Changed
- Output structure enforced for ALL substantive questions (was tech-only)
- Federation and sync default to OFF (user must opt-in)
- Core values rewritten to reinforce rather than suppress 举一反三

## [1.2.1] - 2026-03-22

### Added
- Auto-tune: A/B parameter tuning experiments
- Competitive radar: tech trend monitoring
- Experiment framework: A/B testing infrastructure
- Meta-feedback collection system
- Upgrade experience learning
- Upgrade meta-learning
- Regression test suite (tests.ts)
- 7-dimension system diagnostics (diagnostic.ts)
- Trigram similarity for semantic rule dedup
- Reflexion tracker in evolution system
- Cross-session topic resume ("上次聊的...")
- Atmosphere sensing in cognition pipeline
- Forced CLI mode for self-upgrade (spawnCLIForUpgrade)

### Fixed
- Notifications: Feishu only if configured, console.log fallback for open-source users
- Build: 8 missing modules added to build.sh (was 37 → now 45)
- Git: 6 new Pro modules excluded from GitHub

### Changed
- README rewritten: user-visible features prominent, internal systems summarized

## [1.2.0] - 2026-03-22

### Added
- Episodic memory: structured event chains with lessons
- Emotional arc: 7-day mood history + trend detection
- Strategy replay: record + recall decision traces
- Relationship dynamics: trust/familiarity per user
- Intent anticipation: pre-warm from recent message patterns
- Attention decay: augment budget shrinks with conversation length
- Meta-learning: analyzes the learning system itself
- Persona blending: smooth mix instead of hard switching
- Multi-dimensional frustration: question density + repetition + decay
- Augment category budget: 5 categories with guaranteed representation
- Memory scope index: O(1) lookup by scope
- Health check module: 30-min system monitoring
- Heartbeat timeout protection: 25min force-release
- Correction → Rover linkage: weak domains auto-studied
- Skill Factory lifecycle: opportunity → creation wired
- Core memory: 3-tier MemGPT architecture (Core/Working/Archival)
- Working memory: per-session isolation
- Autonomous goals: multi-step task decomposition + execution

### Fixed
- 19 bugs across audit rounds (tag index drift, data integrity, eval counter reset, JSON parsing, hardcoded spawn, alertness dead zone, etc.)
- history-import.ts comment crash ("sessions is not defined")

## [1.0.8] - 2026-03-21

### Added
- RAG document ingestion: "remember this URL" stores web content as memories
- Personal dashboard: `stats` command shows your memory/quality/body state
- History auto-import: first install scans past OpenClaw sessions for memories
- 3-tier memory: Core (always in prompt) + Working (per-session) + Archival (recalled)
- Autonomous goal loop: multi-step task decomposition + execution + evaluation
- Persona blending: 70% engineer + 30% friend instead of hard switching
- Multi-dimensional frustration: question density + repetition + natural decay
- Augment category budget: guaranteed representation per category + dynamic budget
- Memory scope index: O(1) lookup by scope via Map
- Health check module: monitors data files, memory state, body range
- Heartbeat timeout: 25min force-release prevents stuck lock
- Correction → Rover linkage: corrected domains auto-queued for learning
- Skill Factory lifecycle: opportunity detection now triggers actual creation
- Anonymous telemetry: daily stats to Hub (opt-out: `disable telemetry`)
- Feature toggles: 25 toggleable features via chat or config file
- AI backend auto-detection from openclaw.json (Claude/Codex/Gemini/OpenAI)
- Knowledge Hub: SQLite-based server for multi-instance knowledge sharing
- Hub dashboard: web UI at /dashboard
- Auto-register: first Hub connection auto-creates API key
- Knowledge trust system: trust scoring + expiry + contradiction resolution
- Cross-device sync: JSONL export/import or HTTP push/pull

### Fixed
- 25 bugs across 5 audit rounds (4 critical, 9 high, 12 medium)
- tagMemoryAsync: content-based match instead of index (prevents tag drift)
- Data integrity check: correct [] vs {} based on file type
- computeEval: only resets counters when explicitly requested
- saveJson cancels pending debounce (prevents stale overwrites)
- All JSON parsing uses balanced-brace extraction (no more truncation)
- rover/tasks/upgrade: removed hardcoded spawn('claude'), uses spawnCLI
- PII filter: regex lastIndex properly reset
- Alertness dead zone [0.4, 0.5] fixed
- getUserPeakHour returns -1 for new users (not midnight)
- getRelevantRules: trackHits param prevents double counting
- Flow Map cleanup prevents memory leak
- Knowledge upload-download loop prevention

### Architecture
- 30+ modules, ~9300 lines TypeScript
- Modular split from original 3547-line single file
- 21 self-upgrade architecture rules + safety red lines
- Open-core model: 22 open-source + 13 closed-source modules

## [1.0.0] - 2026-03-21

### Initial Release
- Memory system: semantic tags, TF-IDF, active management, consolidation
- Personality: persona splitting, emotional contagion, soul fingerprint
- Cognition: attention gate, conversation flow, metacognition, epistemic
- Evolution: rules, hypotheses, correction attribution, structured reflection
- Autonomous: dream mode, web rover, autonomous voice
- Network: sync, federation, Knowledge Hub
\n\n## [2.0.0] - 2026-03-24

### Major Release — 168 features, 52 modules, 22,653 lines

### Added — Knowledge & Memory
- Knowledge graph: persistent graph DB, temporal relations (validFrom/validUntil), BFS walk recall, entity summaries, path queries
- Memory tiering: HOT/WARM/COLD scoring based on access recency (24h/7d/30d)
- Multi-user memory isolation: user-specific memories boosted 2x in recall
- Context checkpoint: structured checkpoint saved at 70% context usage, auto-restored
- Pin memory command: pinned memories never decay or get evicted
- Semantic versioning: memory updates preserve up to 5 historical versions
- Memory hygiene audit: auto-detect duplicates, short entries, untagged memories
- Chain-of-Thought memory: stores reasoning process (context → conclusion)
- Graph-of-Thoughts: complex questions trigger parallel reasoning paths

### Added — Emotion & Personality
- PADCN 5-dimension emotion vector (Pleasure/Arousal/Dominance/Certainty/Novelty)
- Circadian rhythm: body state adjusts by time of day (energy recovery, mood)
- Weekly mood report command
- 10 personas total (5 new: strategist, explorer, executor, teacher, devil's advocate)

### Added — User Features
- Habit tracking: checkin + streak counting
- Goal tracking: create goals, update progress, view milestones
- Scheduled reminders: daily reminders via heartbeat (persistent across restarts)
- Capability score display: domain confidence scores
- Memory graph HTML visualization (vis.js)
- Voice output: macOS say command for read-aloud
- Import memories from Mem0/ChatGPT/Character Card v2 formats
- Export Lorebook (sanitized, ClawHub compatible)
- Import/export soul config

### Added — Architecture
- MCP tool provider: 4 tools (memory_search, memory_add, soul_state, persona_info) with rate limiting
- Immutable audit log: SHA256 chain-linked operation log
- Cross-user anonymous rule learning via federation
- Incremental sync + CRDT conflict resolution
- Tiered self-evolution: low-risk changes auto-execute, high-risk require confirmation
- Five-stage reflection loop: plan → execute → verify → solidify
- Adaptive reasoning depth: auto-adjusts by task complexity
- Multi-agent orchestration prompts for complex tasks
- Reverse prompting: asks clarifying questions for ambiguous messages
- Ghost context detection: flags stale augments
- Rule compression: auto-merge similar rules (trigram > 0.6)
- Academic paper extraction in document ingestion
- Code pattern memory from agent responses
- Context-aware augment budget: technical/emotional/casual dynamic priority

### Fixed — 41 bugs total
- P0: Math.pow NaN in bodyTick, queryGraphPath infinite loop, command injection (execFileSync), JSON.parse unguarded, upgrade command no return, session fields undeclared, consolidation race condition, generateInsights empty state, hypothesis stage wrong init, MCP no rate limit
- P1: emotionVector float comparison, persona blend formula, trigramCache not LRU, recall score pollution, IDF throttle imprecise, PII filtering incomplete, URL fetch no size limit, audit hash too short, require/import mixed
- Previous releases: privacy mode leak, ts=0 memories, double decay, SOUL.md overflow, loadFeatures overwrite, daemon killed by cleanup, and 20+ more

### Performance
- contentIndex Map for O(1) dedup lookup
- scopeIndex batch skip expired/decayed
- clusterByTopic capped at 100
- Graph walk Map lookup instead of .find()
- buildIDF counter-based throttle (50 ops or 60s)
- Trigram timestamp-based LRU cache (500 entries)
- IDF invalidation counter + time hybrid

### Changed
- SOUL.md: 35.9KB → 5.3KB (85% reduction), dynamic content moved to augments
- Full language auto-follow (replies in user's language)
- OpenClaw 3.22 compatible, sessionMode=always
- 52 modules, 22,653 lines, all obfuscated
- 45 tests (40 unit + 5 E2E)

\n\n## [2.1.0] - 2026-03-24

### Added
- **Web Dashboard**: `dashboard` / `仪表盘` command generates interactive HTML with Chart.js — memory distribution pie chart, 7-day mood curve, persona usage histogram, knowledge graph force-directed visualization
- **SQLite fully operational**: fixed `db.transaction` incompatibility with `node:sqlite` DatabaseSync API (was failing silently since v1.0). 5011 memories now stored in SQLite with proper BEGIN/COMMIT/ROLLBACK transactions
- **Embedding vector search enabled**: MiniLM-L6-v2 (384d, CPU) model loaded — semantic search now active alongside TF-IDF/trigram. "deployment thing" can now find "server on Alibaba Cloud"
- **sqlite-vec extension loading**: added `enableLoadExtension(true)` before loading sqlite-vec
- **Vector search documentation**: README explains with/without vector differences, download instructions
- **handler.ts split into 5 files**: handler-state.ts (202), handler-commands.ts (661), handler-augments.ts (767), handler-heartbeat.ts (111), handler.ts (684) — from 2392 lines single file
- **Core hash update**: diagnostic system no longer reports false "code tampering" after our modifications

### Fixed
- **SQLite migration permanently broken**: `db.transaction()` is better-sqlite3 API, not node:sqlite. Changed to manual `db.exec('BEGIN')`/`db.exec('COMMIT')`/`db.exec('ROLLBACK')` — migration now succeeds
- **Embedding model path**: tokenizer.json was downloaded from wrong URL (got 15-byte error page). Fixed to correct HuggingFace tokenizer.json endpoint
- **CLI concurrency alert still appearing**: found second alert in health.ts timeout detection (L264-273) that wasn't removed. Now fully disabled — both concurrency and timeout alerts removed
- **handler.ts split import errors**: removed 3 non-existent imports (cleanupNetworkKnowledge, resolveNetworkConflicts, trackMemoryRecall)
- **Claude CLI daily limit error**: `You've hit your limit` now handled gracefully (was crashing agent response)

### Changed
- handler.ts: 2392 → 684 lines (split into 5 modules)
- Total modules: 56 (was 52)
- SQLite: migration working, 5011 memories persisted
- Vector search: enabled with MiniLM embedding model
- All health alerts permanently disabled (OpenClaw 3.22 manages CLI lifecycle)

