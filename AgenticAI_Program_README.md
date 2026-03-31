# Agentic AI Program

## Purpose
This directory is the design and documentation home for a project focused on building an always-on assistant architecture that is philosophically grounded, technically implementable, and governable over time.

The program exists to reduce drift, improve meta-cognitive grounding, maintain shared understanding and aligned goals, and design an agentic AI system that uses the right cognitive machinery for the right class of problem.

This is not only a tooling project. It is also a philosophical, technical, managerial, strategic, systemic, and cybernetic design project.

## Core thesis
Reliable agentic AI requires more than stronger models or more tools.
It requires an operating discipline that combines:

1. **OSAR v2 as philosophical grounding**
   - a framework for defining, maintaining, repairing, and evolving shared understanding and coordinated goals
   - grounded ontologically, epistemologically, and through languaging discipline
   - extended into managerial, strategic, systemic, cybernetic, and continuously improving practice

2. **Drift minimization and meta-cognitive grounding**
   - explicit handling of observer limits, assumptions, confidence, visibility boundaries, and unsupported claims
   - reduction of hidden-prior creep, context loss, semantic drift, and undocumented policy hardening

3. **Neuro-symbolic balance**
   - use LLMs where ambiguity, synthesis, interpretation, and interface adaptation matter
   - use deterministic algorithms, validation, rules, and heuristics where exactness, reproducibility, and enforcement matter
   - use DS/classic AI, GenAI, or agentic systems only where each is actually appropriate to the problem

4. **Probabilistic vs deterministic skill design**
   - define when to use LLMs, DS/classic AI, GenAI, agents/agentic AI, and deterministic logic
   - clarify decision criteria by ambiguity, risk, reversibility, structure, latency, and computational tractability
   - reduce avoidable LLM failure modes through explicit allocation rather than defaulting to model improvisation

5. **Context engineering**
   - maintain focus, scope, depth, breadth, and task continuity across single-agent and multi-agent work
   - manage context packaging, memory, delegation, specialist handoff, and coordination stacks deliberately

6. **Librarian-style knowledge architecture**
   - use curator pipelines to transform raw sources (PDF, text, CSV, etc.) into structured Markdown/JSON knowledge stores
   - organize those stores into catalogs and source breakdowns that respect file-size and retrieval constraints
   - create specialist librarians who answer from their own catalogs only, refuse unsupported claims, and always provide citations
   - create master librarians that query specialists across domains and provide summaries with receipts

7. **Governance against drift**
   - detect and reduce assertion/assessment confusion, hidden assumptions, retrieval drift, context drift, semantic drift, and silent governance hardening
   - make changes reviewable, explainable, and measurable

## Foundations

The program is grounded in several intersecting traditions:

### 1) OSAR’s philosophical lineage
OSAR v2.1 is not philosophically neutral. Its grounding draws on traditions concerned with observation, language, coordination, and recursive learning — including Maturana and Varela on observer-dependence and systemic sense-making; Echeverría on language, commitments, and coordination; Wittgenstein on meaning through use and distinctions; Austin and Searle on speech acts and the world-shaping force of language; and systems/cybernetic thinking on feedback, recursion, adaptation, and boundary choice.

These influences matter here not as ornamental citations, but because they explain why the program treats observer, system boundary, languaging, and recursive correction as structurally decisive.

### 2) Complexity theory and Cynefin
The program also draws on complexity-aware thinking, including Cynefin-style distinctions between ordered, complicated, complex, chaotic, and confused domains.

This matters because the architecture should not assume every problem can be solved with the same decision logic, control style, evidence standard, or planning horizon. Different domains require different forms of sense-making, experimentation, escalation, constraint, and review.

## Program pillars

### 1) OSAR v2.1 as the grounding model
OSAR is the philosophical and operational grounding model for the program.
It is treated as the framework for:
- observer awareness
- system framing
- action selection
- result interpretation
- recursive learning
- governance update
- maintaining shared understanding and coordinated goals

### 2) Drift minimization and meta-cognitive grounding
The system should explicitly track:
- assertions vs assessments
- visibility boundaries
- uncertainty and unsupported claims
- observer changes
- context loss and degradation
- policy drift and governance hardening

### 3) Decision allocation / neuro-symbolic balance
Different problems should be routed to different machinery:
- LLMs for ambiguity, synthesis, and interpretation
- deterministic logic for enforcement, validation, routing, exact calculation, and state transitions
- DS/classic AI where statistical structure and reproducibility matter
- GenAI where generation is the point
- agents/agentic AI where decomposition, persistent state, or tool-rich coordination materially improves outcomes

### 4) Context engineering
The architecture should manage:
- working context
- durable memory
- specialist context
- scratchpads
- delegation boundaries
- depth/breadth coordination across agent stacks
- context packaging for handoffs and reviews

### 5) Librarian architecture
The knowledge layer should be structured as:
- **curators** that build bounded, structured knowledge assets from raw data
- **specialist librarians** that answer only from their catalogs, refuse hallucination, and cite sources
- **master librarians** that federate across specialists and return summaries with receipts

### 6) Governance and evaluation
The program should define:
- how doctrinal, architectural, and implementation changes are reviewed
- how drift is measured
- how unsupported claims are tracked
- how retrieval quality and coordination quality are evaluated
- how the system iterates until documentation and design artifacts meet their target quality bar

## Directory map
- `osar/` — recovered OSAR v2 lineage, related artifacts, critiques, and the v2.1 steelman replacement
- `README.md` — program overview and thesis
- `documentation_plan.md` — documentation roadmap for the broader program

## Immediate outputs in this first pass
1. Recover OSAR v2 and related artifacts from prior materials
2. Add those materials under `docs/agentic-ai/osar/`
3. Produce the first steelman replacement: `docs/agentic-ai/osar/osar_v2.1.md`
4. Critique and rate the steelman against its objectives, using adversarial review and iteration until it reaches the target quality bar
5. Define the documentation roadmap for the rest of the program

## Design rule
Every major document and design choice in this program should answer:
**What failure mode does this reduce, and why is this the right level of abstraction for solving it?**
