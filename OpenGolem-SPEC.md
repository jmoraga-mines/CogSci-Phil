# OpenGolem — Specification Document

**Version:** 0.1.0-draft
**Date:** 2026-03-05
**Author:** Jim Moraga

---

## 1. Vision

OpenGolem is a safety-first, minimalist personal AI assistant targeting power users — not just engineers and vibe coders. It borrows nano-claw's core principles (radical simplicity, agent isolation, skills-on-demand) and extends them with:

- **Security as core infrastructure** — non-negotiable, always-on, not a feature toggle
- **Safety as a configurable plugin** — adjustable by user profile (public figure vs. private individual), with informed consent for any reduction
- **An Executive Assistant (EA) persona** — proactive accountability, priority management, and constructive friction ("Jiminy Cricket" model)
- **A broader user base** — knowledge workers willing to pay for an assistant that keeps them organized, safe, and honest with themselves

OpenGolem is not a maximalist platform. It is a minimal, auditable core with a plugin architecture for everything else.

---

## 2. Design Principles

### 2.1 Radical Minimalism
The core must remain small enough for a single person to read, understand, and audit. Target: under 5,000 lines of Rust for the runtime core. Complexity lives in plugins, not the core.

### 2.2 Agent Isolation
Every agent runs in its own sandboxed environment with explicit filesystem mounting and network access controls. No agent can observe or interfere with another agent's state unless explicitly permitted through the bus.

### 2.3 Security is Infrastructure
Security is not a feature — it is the foundation. The Security Agent runs at the core level. Users cannot disable it. They can be informed about what it does and why, but the floor is non-negotiable. Credential management (Bitwarden), permission boundaries, and audit logging are core, not plugins.

### 2.4 Safety is a Dial
Safety is a plugin that runs routinely (scheduled + preemptive). Users can adjust its sensitivity based on their risk profile. Adjustments require informed consent: the system explains consequences before allowing changes. Default: assertive. Direction of adjustment: downward only after explicit acknowledgment.

### 2.5 Constructive Friction
The EA agent is designed to be productively annoying. It starts assertive and users can tone it down based on interaction patterns. The assistant holds users accountable to their own stated goals, priorities, and commitments — not to the agent's opinions.

### 2.6 Opinionated Defaults, Escape Hatches Later
Setup and onboarding enforce rigidity to reduce decision fatigue:
- One credential store (Bitwarden)
- One memory backend at launch (semantic core)
- One recommended LLM provider path (OpenRouter)
- Good documentation over flexible configuration

---

## 3. Architecture Overview

```
┌─────────────────────────────────────────────────────────┐
│                    OpenGolem Core (Rust)                 │
│                                                         │
│  ┌──────────┐  ┌──────────┐  ┌───────────┐             │
│  │  Agent    │  │ Security │  │ Credential│             │
│  │  Loop     │  │ Agent    │  │ Manager   │             │
│  │          │  │ (core)   │  │(Bitwarden)│             │
│  └────┬─────┘  └────┬─────┘  └───────────┘             │
│       │              │                                   │
│  ┌────┴─────┐  ┌────┴──────┐  ┌───────────┐            │
│  │ Message  │  │ Audit     │  │ Session   │            │
│  │ Bus      │  │ Log       │  │ Manager   │            │
│  └────┬─────┘  └───────────┘  └───────────┘            │
│       │                                                  │
│  ┌────┴─────┐  ┌───────────┐  ┌───────────┐            │
│  │ Heartbeat│  │ Cron      │  │ Provider  │            │
│  │ Scheduler│  │ Scheduler │  │ Abstraction│           │
│  └──────────┘  └───────────┘  └───────────┘            │
└───────────────────────┬─────────────────────────────────┘
                        │ Plugin Interface (IPC / subprocess)
        ┌───────────────┼───────────────────┐
        │               │                   │
   ┌────┴────┐    ┌─────┴─────┐      ┌─────┴─────┐
   │ Safety  │    │ EA Agent  │      │ Memory    │
   │ Agent   │    │ (Jiminy   │      │ Plugins   │
   │(plugin) │    │  Cricket) │      │           │
   └─────────┘    └───────────┘      └───────────┘
                                     │  Semantic  │
                                     │  (core)    │
                                     ├────────────┤
                                     │  Obsidian  │
                                     │  (plugin)  │
                                     ├────────────┤
                                     │  MemBox    │
                                     │  (plugin)  │
                                     └────────────┘
```

### 3.1 Core Components (Rust)

| Component | Responsibility |
|-----------|---------------|
| **Agent Loop** | Main orchestration: user message → LLM → tool dispatch → response. Same minimal pattern as nano-claw: loop until `stop_reason != tool_use` |
| **Security Agent** | Core-level watchdog. Monitors filesystem access, network calls, credential usage, and tool execution. Runs both inline (pre-execution gating for high-risk actions) and scheduled (periodic audit of logs and state). Non-disableable |
| **Credential Manager** | Bitwarden CLI integration. Secrets never stored in config files. Runtime retrieval only. Core handles the Bitwarden session lifecycle |
| **Message Bus** | Internal routing between agents, heartbeat, cron, and channel integrations. Typed message passing with audit trail |
| **Audit Log** | Append-only, tamper-evident log of all agent actions, tool executions, security events, and safety decisions. Core infrastructure, not optional |
| **Session Manager** | Conversation context isolation. Each session maintains independent message history. Sessions are scoped per-channel and per-agent |
| **Heartbeat Scheduler** | Periodic agent wake-up. Reads a checklist, executes checks, reports only when something needs attention. Configurable interval (default: 30 min) |
| **Cron Scheduler** | Time-based job execution with session isolation, model overrides, and delivery control |
| **Provider Abstraction** | Unified LLM interface. OpenRouter as default (single API key, multi-model access). Supports model pinning per agent/task for cost control |

### 3.2 Plugin Interface

Plugins communicate with the core via a well-defined IPC boundary (subprocess with structured JSON messages over stdin/stdout). Plugins can be written in any language (Python recommended for LLM ecosystem access). The core enforces:

- **Capability declarations** — each plugin declares what it needs (filesystem paths, network access, tools)
- **Sandboxed execution** — plugins run in isolated processes with only declared capabilities
- **Health monitoring** — the core tracks plugin liveness and restarts failed plugins

### 3.3 Plugin Components

| Component | Tier | Responsibility |
|-----------|------|---------------|
| **Safety Agent** | Free (basic) / Paid (full) | Configurable watchdog for user-protective behaviors. Monitors for: excessive spending, irreversible actions without confirmation, drift from stated intent. Runs scheduled + preemptive. Users can adjust sensitivity with informed consent |
| **EA Agent** | Free (basic) / Paid (full) | Executive Assistant persona. Agenda management, priority tracking, goal accountability. Starts assertive, learns what friction the user responds to |
| **Memory Plugins** | Varies | Pluggable memory backends behind a common trait/interface |
| **Channel Plugins** | Free | Chat platform integrations (Telegram, Discord, Slack, etc.) |
| **Skill Plugins** | Free | Domain-specific capabilities loaded on demand (weather, GitHub, calendar, etc.) |

---

## 4. The Agent Loop

The core loop follows the proven nano-claw pattern:

```
User message
    │
    ▼
┌─────────────────┐
│ Context Builder  │──── Load: system prompt + memory + skills + session history
│                  │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   LLM Call       │──── Send messages[] to provider
│                  │
└────────┬────────┘
         │
         ▼
┌─────────────────┐     ┌──────────────────┐
│ stop_reason ==   │────▶│ Security Gate    │──── Pre-execution check
│ "tool_use"?      │ yes │ (inline)         │     (is this action permitted?)
│                  │     └────────┬─────────┘
└────────┬────────┘              │
         │ no                    ▼
         │              ┌──────────────────┐
         ▼              │ Execute Tool     │
┌─────────────────┐     │                  │
│ Return response  │     └────────┬─────────┘
│ to user          │              │
└─────────────────┘              ▼
                        ┌──────────────────┐
                        │ Append results   │
                        │ to messages[]    │──── Loop back to LLM Call
                        └──────────────────┘
```

### 4.1 Context Builder

Assembles the prompt from layered sources:

1. **System prompt** — core identity, safety rules, tool definitions
2. **Security context** — current permission state, active restrictions
3. **Memory context** — relevant memories retrieved from active memory plugin
4. **Skills** — loaded skill definitions relevant to the current task (Markdown files referencing external scripts, never inline code — per nano-claw's lesson on token efficiency)
5. **Session history** — conversation messages for the active session
6. **EA context** (if active) — current priorities, pending accountability items, overdue goals

### 4.2 Tool Registry

Tools register into a dispatch map. Adding a tool means adding one handler — the loop never changes. Built-in tools:

- `shell` — sandboxed command execution (scoped to workspace)
- `read_file` / `write_file` — filesystem operations (scoped to workspace)
- `memory_read` / `memory_write` — interface to active memory plugin
- `schedule` — create/modify cron jobs and heartbeat tasks

Plugin-provided tools register through the plugin interface and are subject to Security Agent review.

---

## 5. Security Model

### 5.1 Core Security (Non-Disableable)

| Control | Description |
|---------|-------------|
| **Credential isolation** | API keys and secrets live in Bitwarden. Never written to config files, environment variables, or logs. Retrieved at runtime via `bw get` with session tokens |
| **Filesystem sandboxing** | Each agent's workspace is an explicit mount. No path traversal. The `workspace_locked` flag (default: true) restricts all tools to the workspace directory |
| **Network allowlist** | Outbound network access requires explicit declaration. The Security Agent monitors and logs all network calls |
| **Audit logging** | Every tool execution, LLM call, credential access, and plugin action is logged to an append-only audit log with timestamps and agent identity |
| **Tool execution gating** | High-risk tools (shell, write_file, network) pass through the Security Agent's inline gate before execution. The gate checks: is this agent authorized for this tool? Is the target path/URL within declared scope? |
| **Plugin capability enforcement** | Plugins declare capabilities at registration. The core enforces these declarations at runtime. A plugin that declares read-only filesystem access cannot call write_file |

### 5.2 Security Agent Behaviors

**Inline (pre-execution):**
- Intercepts tool calls before execution
- Validates against declared permissions
- Blocks and logs unauthorized attempts
- Escalates ambiguous cases to user with explanation

**Scheduled (periodic audit):**
- Reviews audit log for anomalous patterns (unusual file access, unexpected network calls, credential usage spikes)
- Checks plugin health and capability compliance
- Generates security summary on configurable schedule
- Reports findings through the message bus (delivered to user's active channel)

### 5.3 User-Facing Security

- Users can view the audit log at any time
- Security events are surfaced proactively (not buried in logs)
- Users cannot disable core security, but receive clear explanations of what is being enforced and why
- The Security Agent explains consequences when users request actions that are blocked

---

## 6. Safety Model (Plugin)

### 6.1 Safety Agent

The Safety Agent runs as a plugin with elevated read access to the audit log and message bus. It monitors user-protective concerns that are distinct from security:

| Concern | Example |
|---------|---------|
| **Cost protection** | LLM spending exceeding user-defined budget. Alert thresholds with circuit breakers |
| **Irreversibility protection** | Actions that cannot be undone (sending emails, deleting files, posting publicly) require explicit confirmation |
| **Intent drift** | Agent taking actions that diverge from the user's stated objective. Detected by comparing current action chain against the original request |
| **Information exposure** | Preventing the agent from sharing sensitive information in inappropriate channels (e.g., work data in a personal chat) |
| **Behavioral guardrails** | Preventing the agent from taking autonomous actions beyond its authorized scope, especially in always-on mode |

### 6.2 Safety Levels

Users select a safety profile during onboarding. Profiles can be adjusted later with informed consent (the system explains what changes and what risks increase).

| Level | Description | Target User |
|-------|-------------|-------------|
| **High** | All confirmations enabled. Proactive warnings. Conservative autonomous action limits. Heartbeat safety checks every cycle | Public figures, high-risk roles, new users (default) |
| **Medium** | Confirmations for irreversible actions only. Periodic safety audits. Standard autonomous limits | Most knowledge workers |
| **Low** | Minimal confirmations. Safety audits on demand. Extended autonomous action scope | Experienced users who explicitly accept risks |

### 6.3 Informed Consent Flow

```
User requests safety reduction
    │
    ▼
Safety Agent explains:
  - What protections are being removed
  - What specific risks increase
  - Examples of scenarios that would no longer be caught
    │
    ▼
User explicitly confirms (not just a toggle — requires typed acknowledgment)
    │
    ▼
Change logged in audit trail with user's acknowledgment
```

---

## 7. Executive Assistant Agent (Plugin)

### 7.1 Core Philosophy

The EA is not a passive task list. It is a proactive accountability partner that:

- **Mirrors, not advises** — "You said X was your priority. You haven't touched it in 5 days." Not "I think you should work on X."
- **Starts assertive** — default behavior is constructive nagging. Users tone it down through interaction, not through settings
- **Learns friction response** — tracks which nudges the user responds to and which they consistently dismiss. Adapts accordingly
- **Respects autonomy** — the user is always in control. The EA surfaces information and accountability; it does not block or override

### 7.2 Intake Process

During onboarding (or first activation), the EA conducts a structured intake:

1. **Priorities** — What are your current top priorities? (user defines, EA stores as their contract with themselves)
2. **Rhythms** — What does a good day/week look like? What recurring commitments matter?
3. **Accountability preferences** — What do you want to be held accountable for? How do you want to be reminded?
4. **Anti-goals** — What are you avoiding or procrastinating on? (optional but powerful for Jiminy Cricket mode)

### 7.3 EA Behaviors

**Proactive (heartbeat-driven):**
- Calendar review — upcoming events, conflicts, preparation reminders
- Priority check-in — are stated priorities getting attention?
- Task aging — overdue items surfaced with increasing urgency
- Goal drift detection — patterns of avoidance flagged constructively

**Reactive (user-triggered):**
- "What should I focus on?" — prioritized based on user's own stated priorities, deadlines, and patterns
- "Reschedule my day" — calendar reorganization respecting priorities
- "How am I doing on [goal]?" — progress summary with honest assessment
- Task capture — quick task/note intake from any channel

### 7.4 Assertiveness Calibration

The EA tracks a per-category responsiveness score:

```
Category: "calendar_reminders"    → User responds 80% of the time → Keep current frequency
Category: "priority_accountability" → User responds 30% of the time → Reduce frequency, try different framing
Category: "overdue_tasks"          → User responds 90% of the time → This friction is valued, maintain
```

Users can also explicitly say "stop nagging me about X" — the EA acknowledges, logs it, and stops. But it may surface it again after a configurable cool-off period ("You asked me to stop reminding you about X three weeks ago. Just checking — is that still the case?").

---

## 8. Memory Architecture

### 8.1 Design Principles

Memory is plugin-based with a common trait/interface. The core defines what memory operations look like; plugins implement storage, retrieval, and curation strategies.

### 8.2 Memory Interface (Core)

```rust
trait MemoryProvider {
    /// Store a memory unit with metadata
    fn store(&self, content: MemoryUnit) -> Result<MemoryId>;

    /// Retrieve relevant memories for a given query/context
    fn retrieve(&self, query: &str, limit: usize) -> Result<Vec<MemoryUnit>>;

    /// Curate: compress, merge, or expire memories
    fn curate(&self, strategy: CurationStrategy) -> Result<CurationReport>;

    /// Export memories in a human-readable format
    fn export(&self, format: ExportFormat) -> Result<Vec<u8>>;

    /// Import memories (for user corrections or migrations)
    fn import(&self, data: &[u8], format: ExportFormat) -> Result<ImportReport>;
}

struct MemoryUnit {
    id: MemoryId,
    content: String,
    memory_type: MemoryType,     // Fact, Event, Preference, Goal, Interaction
    source: MemorySource,        // Which agent/session created this
    created_at: Timestamp,
    last_accessed: Timestamp,
    confidence: f32,             // How certain the system is about this memory
    tags: Vec<String>,
}
```

### 8.3 Memory Plugin Roadmap

| Plugin | Priority | Description |
|--------|----------|-------------|
| **Semantic Core** | P0 (MVP) | Embedding-based storage and retrieval. Simple but functional. Similar to OpenClaw's memory system. Stores facts, events, preferences as typed memory units with vector similarity retrieval |
| **Obsidian Vault** | P1 | Bidirectional sync with an Obsidian vault. Memory units map to notes. Links map to relationships. User can browse, edit, and correct memories through Obsidian's UI. Changes sync back to the agent's memory |
| **MemBox** | P2 | Topic Loom implementation for sliding-window topic grouping. Trace Weaver for long-range event-timeline linking. Preserves narrative and causal flow that embedding-only retrieval destroys. Higher token cost, higher fidelity |

### 8.4 Memory Curation

All memory plugins must implement the `curate` method. Curation runs on a schedule (default: daily) and handles:

- **Compression** — merge redundant or overlapping memories
- **Confidence decay** — reduce confidence on memories that haven't been accessed or confirmed
- **Conflict resolution** — flag contradictory memories for user review rather than silently resolving
- **Privacy sweep** — remove memories the user has requested be forgotten

---

## 9. LLM Provider Strategy

### 9.1 Provider Abstraction

The core defines a provider trait that all LLM backends must implement:

```rust
trait LLMProvider {
    /// Send a completion request
    fn complete(&self, request: CompletionRequest) -> Result<CompletionResponse>;

    /// Check provider health and quota
    fn health(&self) -> Result<ProviderHealth>;

    /// Estimate cost for a request
    fn estimate_cost(&self, request: &CompletionRequest) -> Result<CostEstimate>;
}
```

### 9.2 Default Configuration

OpenRouter is the recommended default provider for onboarding simplicity (one API key, multi-model access). Recommended model pinning:

| Agent/Task | Model Tier | Rationale |
|------------|-----------|-----------|
| Heartbeat checks | Cheap (e.g., GPT-5 Nano, Gemini Flash) | High frequency, low complexity. Cost control |
| Security Agent | Mid-tier (e.g., Claude Sonnet) | Needs reasoning ability for pattern detection, but runs frequently |
| Safety Agent | Mid-tier | Similar to Security — periodic reasoning at moderate cost |
| EA Agent | Mid-to-high tier | Needs strong reasoning for accountability and priority inference |
| User-facing chat | User's choice (default: mid-tier) | Balance of quality and cost |
| Complex reasoning tasks | High tier (e.g., Claude Opus) | On-demand only, never in background loops |

### 9.3 Cost Controls (Core)

- Per-agent token budgets (configurable)
- Per-session token budgets
- Daily/monthly spending caps with circuit breaker
- Cost estimation before expensive operations (surfaced to user)
- Heartbeat and cron jobs default to cheapest viable model

---

## 10. Onboarding Flow

### 10.1 Principles

Onboarding is opinionated and sequential. No decision paralysis. The user makes a small number of meaningful choices and the system handles the rest.

### 10.2 Steps

```
1. Install
   └── Single binary (Rust) + Python runtime for plugins
   └── `opengolem install` or package manager

2. Credential Setup
   └── "Do you have Bitwarden CLI installed?" → guide if not
   └── Store LLM API key in Bitwarden
   └── OpenGolem retrieves it at runtime — never stored locally

3. LLM Provider
   └── Default: OpenRouter
   └── User provides API key → stored in Bitwarden
   └── System validates key and confirms available models

4. Workspace
   └── Create ~/.opengolem/ workspace
   └── Initialize config.json with secure defaults
   └── Create HEARTBEAT.md with sensible starter checklist

5. Safety Profile
   └── Brief explanation of High / Medium / Low
   └── Default: High
   └── User can adjust (with informed consent flow)

6. EA Intake (optional, can defer)
   └── "What are your current priorities?"
   └── "What does a good week look like?"
   └── "What do you want to be held accountable for?"
   └── Stored as the user's self-contract in memory

7. Channel Setup (optional, can defer)
   └── Connect one chat platform (Telegram recommended for simplicity)

8. First Interaction
   └── System runs a test heartbeat
   └── EA introduces itself and confirms priorities
   └── Security Agent confirms its presence and explains its role
```

---

## 11. Directory Structure

```
~/.opengolem/
├── config.json              # Core configuration (no secrets — those live in Bitwarden)
├── workspace/               # Agent workspace (sandboxed)
│   ├── HEARTBEAT.md         # Heartbeat checklist
│   ├── SOUL.md              # Agent identity / persona
│   └── skills/              # User-defined skill files
│       └── *.md             # Markdown only — no inline code
├── memory/                  # Memory plugin storage
│   └── semantic/            # Default semantic core storage
├── audit/                   # Append-only audit logs
│   └── YYYY-MM-DD.jsonl     # Daily log files
├── plugins/                 # Installed plugins
│   ├── safety/
│   ├── ea/
│   └── memory-obsidian/
├── sessions/                # Conversation history per session
│   └── {sessionId}.json
└── cron.json                # Scheduled task definitions
```

---

## 12. Free Tier vs. Knowledge Worker Tier

### 12.1 Free Tier

| Feature | Included |
|---------|----------|
| Core agent loop | Yes |
| Security Agent (full) | Yes — security is not gated behind payment |
| Basic Safety | Yes — irreversibility protection, basic cost alerts |
| Basic EA | Yes — simple calendar, task capture, basic reminders |
| Semantic Core memory | Yes |
| Heartbeat (basic) | Yes — single checklist, default interval |
| One channel integration | Yes |
| Cost controls | Yes |
| Audit log | Yes |

### 12.2 Knowledge Worker Tier

| Feature | Added |
|---------|-------|
| Full Safety Agent | Configurable profiles, intent drift detection, behavioral guardrails, information exposure monitoring |
| Full EA Agent | Priority inference from behavior, Jiminy Cricket accountability, assertiveness calibration, anti-goal tracking, cross-session goal tracking |
| MemBox memory plugin | Topic Loom + Trace Weaver for high-fidelity long-range memory |
| Obsidian Vault sync | Bidirectional memory ↔ Obsidian with user review and correction |
| Advanced heartbeat | Multiple checklists, per-check rotation, smart scheduling |
| Multi-channel | Unlimited channel integrations |
| Model routing | Per-agent model pinning, tiered routing, fallback chains |
| Priority support | Community → direct support channel |

### 12.3 Pricing Philosophy

"The free version keeps you safe. The paid version keeps you organized and accountable."

Security is never gated. The paid tier funds the token-intensive features: rich memory, proactive accountability, and deep safety monitoring.

---

## 13. Technology Stack

| Layer | Technology | Rationale |
|-------|-----------|-----------|
| **Core runtime** | Rust | Memory safety, single binary distribution, strong async (tokio), type system maps to security guarantees |
| **Plugin runtime** | Python (Pydantic models) | LLM ecosystem access, rapid iteration on prompts and interaction patterns. Pydantic models mirror Rust structs for clean porting |
| **Plugin interface** | JSON over stdin/stdout (subprocess IPC) | Language-agnostic, simple, auditable. No shared memory or FFI complexity |
| **Credential store** | Bitwarden CLI | Encrypted, widely adopted, CLI-accessible, user likely already has it |
| **LLM access** | OpenRouter (default) | Single API key, multi-model, cost transparency |
| **Memory (core)** | SQLite + embeddings | Simple, file-based, portable, no external database dependency |
| **Memory (Obsidian)** | Filesystem + Markdown | Direct file sync with Obsidian vault |
| **Audit log** | JSONL files | Append-only, human-readable, trivially parseable |
| **Configuration** | JSON + JSON Schema validation | Validated at startup, no silent misconfigurations |
