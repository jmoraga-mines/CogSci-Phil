# OpenGolem — Implementation Plan

**Version:** 0.1.0-draft
**Date:** 2026-03-05

---

## Phasing Strategy

Build in concentric rings: core first, then free tier features, then paid tier features. Each phase produces a usable artifact — no phase depends on future work to be functional.

**Prototype in Python (Pydantic models) → Port core to Rust once architecture stabilizes.**

The Python prototype validates interaction patterns, prompt engineering, and memory design. Pydantic models serve as the Rust struct blueprint. The port happens once, after Phase 2.

---

## Phase 0: Skeleton (Week 1–2)

**Goal:** Minimal agent loop that can call an LLM and execute one tool.

**Deliverables:**
- [ ] Python project scaffold with Pydantic models for all core data structures
- [ ] Agent loop: `messages[] → LLM → check stop_reason → dispatch tool → loop`
- [ ] Single tool: `shell` (sandboxed to workspace directory)
- [ ] Provider abstraction with OpenRouter as the sole implementation
- [ ] Config loader with JSON Schema validation
- [ ] CLI entry point: `opengolem agent -m "message"` (single message mode)
- [ ] CLI entry point: `opengolem agent` (interactive REPL mode)

**Data models to define (Pydantic):**
```
CompletionRequest / CompletionResponse
ToolCall / ToolResult
AgentConfig
SessionState
```

**Exit criteria:** User can send a message, the agent calls a tool if needed, and returns a response. Nothing else.

---

## Phase 1: Security Core (Week 3–4)

**Goal:** Security infrastructure that is always on from this point forward.

**Deliverables:**
- [ ] Audit log — append-only JSONL writer. Every tool execution, LLM call, and agent action is logged with timestamp, agent ID, action type, and parameters
- [ ] Bitwarden integration — `bw get` wrapper for credential retrieval. Session token management (unlock, cache, re-auth). API keys loaded from Bitwarden at startup, never written to disk
- [ ] Filesystem sandboxing — workspace-locked mode. Path validation on every file tool call. Reject path traversal attempts
- [ ] Security Agent (inline mode) — pre-execution gate on tool calls. Checks: is the tool permitted for this agent? Is the target path/URL within scope? Logs and blocks unauthorized attempts
- [ ] Tool capability declarations — each tool declares what it accesses (filesystem paths, network endpoints). Security Agent validates at registration
- [ ] Config update: remove all plaintext secret fields, replace with Bitwarden key references

**Data models:**
```
AuditEntry
SecurityPolicy
ToolCapability
CredentialReference (pointer to Bitwarden entry, not the secret itself)
```

**Exit criteria:** No tool execution can occur without passing through the Security Agent. All actions are logged. No secrets exist in plaintext anywhere in the system.

---

## Phase 2: Memory & Context (Week 5–7)

**Goal:** Semantic core memory and context builder that makes the agent stateful across sessions.

**Deliverables:**
- [ ] Memory trait/interface definition (Python ABC → will become Rust trait)
- [ ] Semantic Core plugin — SQLite storage + embedding-based retrieval
  - [ ] Memory unit CRUD (store, retrieve, update, delete)
  - [ ] Memory types: Fact, Event, Preference, Goal, Interaction
  - [ ] Embedding generation (sentence-transformers or OpenAI embeddings via provider)
  - [ ] Vector similarity retrieval with configurable top-k
  - [ ] Basic curation: confidence decay, deduplication, user-requested deletion
- [ ] Context builder — assembles prompt from layered sources:
  1. System prompt
  2. Security context (current permissions)
  3. Memory context (relevant retrieved memories)
  4. Session history
- [ ] Session manager — per-session message history, persistence to disk, session isolation
- [ ] Memory CLI: `opengolem memory search "query"`, `opengolem memory list`, `opengolem memory forget "id"`

**Data models:**
```
MemoryUnit / MemoryId / MemoryType / MemorySource
CurationStrategy / CurationReport
ContextFrame (assembled prompt structure)
SessionRecord
```

**Exit criteria:** Agent remembers facts across sessions. User can search, review, and delete memories. Context builder produces coherent prompts with relevant memory inclusion.

---

## Phase 3: Always-On Infrastructure (Week 8–9)

**Goal:** Heartbeat and cron scheduling so the agent runs as a persistent service, not just a CLI tool.

**Deliverables:**
- [ ] Heartbeat scheduler — periodic wake-up, reads HEARTBEAT.md, runs agent turn, delivers results
  - [ ] Configurable interval (default: 30 min)
  - [ ] HEARTBEAT_OK suppression (quiet when nothing needs attention)
  - [ ] Model override for heartbeat (default to cheapest viable model)
  - [ ] Active hours configuration (skip heartbeats outside configured window)
- [ ] Cron scheduler — time-based job execution
  - [ ] Cron expression parsing (5-field + timezone)
  - [ ] Session isolation for cron jobs (don't pollute main session)
  - [ ] Model override per job
  - [ ] Delivery control (announce to channel, or silent)
- [ ] Security Agent (scheduled mode) — periodic audit log review
  - [ ] Anomaly detection: unusual file access patterns, unexpected network calls
  - [ ] Summary report generation on configurable schedule
- [ ] Gateway / daemon mode: `opengolem gateway` — long-running process that manages heartbeat, cron, and incoming messages
- [ ] Cost tracking — token usage per agent, per session, per day. Budget alerts

**Data models:**
```
HeartbeatConfig / HeartbeatResult
CronJob / CronSchedule
CostRecord / BudgetConfig
```

**Exit criteria:** OpenGolem runs as a background service. Heartbeat fires on schedule. Cron jobs execute at configured times. Security Agent audits periodically. Token costs are tracked.

---

## Phase 4: Safety Plugin (Week 10–11)

**Goal:** Safety Agent as the first plugin, establishing the plugin interface pattern.

**Deliverables:**
- [ ] Plugin interface specification — JSON over stdin/stdout protocol
  - [ ] Plugin registration (capability declaration, health check)
  - [ ] Message types: tool_intercept, audit_query, bus_subscribe, report
  - [ ] Plugin lifecycle: start, health_check, stop, restart
- [ ] Plugin host in core — subprocess management, capability enforcement, health monitoring
- [ ] Safety Agent plugin (Python)
  - [ ] Safety profiles: High / Medium / Low with clear behavior definitions
  - [ ] Irreversibility protection — intercept destructive tool calls, require confirmation
  - [ ] Cost protection — monitor spending against budget, alert at thresholds, circuit breaker at cap
  - [ ] Informed consent flow — explain consequences when user adjusts safety level
- [ ] Safety profile selection in onboarding flow
- [ ] Plugin CLI: `opengolem plugin list`, `opengolem plugin status`

**Data models:**
```
PluginManifest (capability declarations)
PluginMessage (IPC protocol)
SafetyProfile / SafetyEvent
ConsentRecord
```

**Exit criteria:** Safety Agent runs as an external Python process. It intercepts destructive actions and requests confirmation. Users can adjust safety levels with informed consent. Plugin interface is proven and reusable for future plugins.

---

## Phase 5: EA Agent — Free Tier (Week 12–14)

**Goal:** Basic Executive Assistant with calendar awareness, task capture, and simple reminders.

**Deliverables:**
- [ ] EA plugin (Python) using the plugin interface from Phase 4
- [ ] Intake flow — structured onboarding conversation to capture priorities, rhythms, and accountability preferences. Stored in memory as typed Goal and Preference memories
- [ ] Calendar integration — read-only access to user's calendar (Google Calendar or ICS file). Surface upcoming events in heartbeat. Conflict detection
- [ ] Task capture — quick task/note creation from any interaction. Stored as memory units with type=Task
- [ ] Basic reminders — time-based reminders via cron. "Remind me to X at Y"
- [ ] Priority surfacing — when user asks "what should I focus on?", retrieve and rank their stated priorities against current tasks and calendar
- [ ] Basic accountability — heartbeat includes a check against stated priorities. If a priority hasn't been touched in N days, surface it once (not yet the full Jiminy Cricket assertiveness model)
- [ ] EA persona in SOUL.md — defines the EA's voice, boundaries, and interaction style

**Data models:**
```
Goal / Priority / Task
IntakeRecord
AccountabilityCheck
```

**Exit criteria:** EA can manage basic agenda, capture tasks, remind the user, and surface priorities. It proactively mentions neglected priorities during heartbeat but doesn't yet do full assertiveness calibration.

---

## Phase 6: Channel Integration (Week 15–16)

**Goal:** Connect OpenGolem to at least one external chat platform so it works beyond the CLI.

**Deliverables:**
- [ ] Channel plugin interface — abstract base for chat platform integrations
- [ ] Telegram channel plugin (recommended first channel — mature bot API, good DX)
  - [ ] Message receive/send
  - [ ] Session binding (one Telegram chat = one session)
  - [ ] Heartbeat and cron delivery to Telegram
  - [ ] Media handling (images, files)
- [ ] Message bus integration — channel messages route through the bus to the agent loop
- [ ] User allowlist — restrict which Telegram users can interact (security requirement)
- [ ] Delivery routing — heartbeat and cron results delivered to the user's most recently active channel

**Exit criteria:** User can interact with OpenGolem via Telegram. Heartbeat results appear in their Telegram chat. Security and safety protections work across the channel boundary.

---

## Phase 7: Rust Port — Core (Week 17–22)

**Goal:** Port the core runtime from Python to Rust. Plugins remain in Python.

**Scope of port:**
- [ ] Agent loop (loop.rs)
- [ ] Context builder (context.rs)
- [ ] Security Agent — inline mode (security.rs)
- [ ] Credential manager — Bitwarden integration (credentials.rs)
- [ ] Audit log (audit.rs)
- [ ] Session manager (session.rs)
- [ ] Heartbeat scheduler (heartbeat.rs)
- [ ] Cron scheduler (cron.rs)
- [ ] Message bus (bus.rs)
- [ ] Provider abstraction + OpenRouter implementation (providers/)
- [ ] Plugin host — subprocess management + IPC (plugins.rs)
- [ ] Tool registry + built-in tools (tools/)
- [ ] Config loader with validation (config.rs)
- [ ] CLI (cli.rs)

**Not ported (remain as Python plugins):**
- Safety Agent
- EA Agent
- Memory plugins (Semantic Core stays Python, talks to core via plugin interface)
- Channel plugins
- Skill plugins

**Approach:**
- Pydantic models → Rust structs + serde (1:1 mapping by design)
- Python subprocess IPC → same protocol, Rust is now the host instead of Python
- Use `tokio` for async runtime (heartbeat, cron, channel listeners, plugin management all concurrent)
- Use `clap` for CLI
- Use `rusqlite` for audit log and session storage
- Compile to single static binary

**Exit criteria:** `opengolem` is a single Rust binary. All plugins run as Python subprocesses communicating via JSON IPC. Behavior is identical to the Python prototype. Binary can be distributed without Python for the core (Python only needed for plugins).

---

## Phase 8+: Post-MVP (Backlog, Unordered)

These features are not part of the PoC/MVP. They are captured here for completeness but are explicitly deferred.

- [ ] **Full EA assertiveness calibration** — per-category responsiveness scoring, adaptive friction, cool-off periods, anti-goal tracking
- [ ] **Obsidian Vault memory plugin** — bidirectional sync, note ↔ memory mapping, wikilink graph
- [ ] **MemBox memory plugin** — Topic Loom, Trace Weaver, long-range event-timeline traces
- [ ] **Intent drift detection** (Safety) — compare action chains against original user request
- [ ] **Information exposure monitoring** (Safety) — cross-channel data leak prevention
- [ ] **Additional channels** — Discord, Slack, WhatsApp, Email
- [ ] **Skill marketplace / registry** — discover and install community-contributed skills
- [ ] **Multi-agent coordination** — multiple agents with explicit communication protocols
- [ ] **Voice interface** — STT/TTS integration (similar to nano-claw voice loop)
- [ ] **Web UI / dashboard** — browser-based management interface
- [ ] **Mobile app** — iOS/Android client
- [ ] **Self-extension** — agent can write and install its own skills (with Security Agent oversight)
- [ ] **Knowledge worker onboarding templates** — pre-built EA configs for common roles (PM, executive, researcher)

---

## Milestones Summary

| Milestone | Phase | Deliverable | Target |
|-----------|-------|-------------|--------|
| **"It talks"** | 0 | Agent loop + one tool + CLI | Week 2 |
| **"It's safe"** | 1 | Security Agent + audit log + Bitwarden | Week 4 |
| **"It remembers"** | 2 | Semantic memory + context builder | Week 7 |
| **"It's always on"** | 3 | Heartbeat + cron + gateway daemon | Week 9 |
| **"It protects you"** | 4 | Safety plugin + plugin interface | Week 11 |
| **"It helps you focus"** | 5 | EA agent (basic) | Week 14 |
| **"It's everywhere"** | 6 | Telegram integration | Week 16 |
| **"It's production-grade"** | 7 | Rust core + single binary | Week 22 |

---

## Risk Register

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| Bitwarden CLI UX friction | High | Medium | Provide clear setup guide. Consider fallback to system keychain for simple cases |
| Plugin IPC latency | Medium | Medium | Benchmark early. JSON over stdin/stdout is simple but may need optimization for high-frequency safety checks. Consider Unix domain sockets if needed |
| LLM cost spiral in always-on mode | High | High | Cost controls are core (Phase 3). Default to cheapest models for background tasks. Budget circuit breakers |
| Python ↔ Rust port introduces bugs | Medium | High | Pydantic models designed to mirror Rust structs. Comprehensive test suite in Python becomes the Rust test suite. Port one module at a time with integration tests |
| EA assertiveness annoys users into disabling it | Medium | High | Start with basic reminders (Phase 5). Full Jiminy Cricket deferred to Phase 8+. Gather user feedback before investing in assertiveness calibration |
| Scope creep into OpenClaw territory | High | High | Spec explicitly excludes Phase 8+ from MVP. Every feature addition must justify its place in the minimal core |
