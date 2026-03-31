# Intelligence, Enactivism, and Agentic AI

**Discussion Summary, Conclusions, and Research Directions**

Jaime F. Moraga, PhD  
March 2026

*This document summarizes a working dialogue on general intelligence, OSAR v2.1, the enactivist tradition (Maturana/Varela), and their implications for agentic AI architecture. It is a working document, not a finished argument.*

---

## 1. The Starting Position

Google DeepMind's cognitive taxonomy for measuring AGI progress decomposes intelligence into ten cognitive faculties — perception, reasoning, memory, metacognition, and so on — and treats their combination as a proxy for general intelligence.

The spider test makes the problem concrete. A jumping spider (*Portia*) with roughly 300,000 neurons plans multi-step detour routes to prey it cannot see, selects hunting strategies based on prey type, learns from single encounters, and continuously self-corrects toward its goal. DeepMind's framework cannot score it. If your framework for measuring general intelligence cannot account for one of the most successful cognitive architectures in the animal kingdom, it is not measuring general intelligence — it is measuring how closely a system approximates one particular model of human cognition.

This is not a new observation. Maturana and Varela's autopoietic framework, the enactivist program, embodied cognition research, and cross-cultural intelligence studies have spent decades showing that factorial decomposition of cognition keeps missing what matters. The question is why the field keeps rediscovering the same blind spots.

---

## 2. Key Distinctions

### 2.1 Intelligence as viability, not cognition

The central reframe: Maturana and Varela showed that cognition is not information processing. It is the process by which a living system maintains its organization in interaction with its environment. Intelligence, in this view, is inseparable from autopoiesis — the capacity of a system to continuously produce and maintain itself, generate its own goals, and adapt its coupling with its environment.

> *A system that scores in the 99th percentile on all ten cognitive faculties but cannot autonomously decide what matters is not generally intelligent. It is an extraordinarily sophisticated tool.*

**Important qualification:** Maturana and Varela defined cognition in specifically biological terms — as the process by which a living system specifies its domain of interactions. This is not the same as the computational or psychological definition of cognition. Whether this biological definition can be extended to non-living systems (or whether a different concept is needed for them) is an open question within the enactivist tradition, not a settled position. The subsequent literature (Thompson, Di Paolo, Froese, Friston) disagrees on this point. This document uses "intelligence" rather than "cognition" to avoid conflating the enactivist and computational usages.

### 2.2 The anthropomorphism trap

There is a perverse asymmetry in intelligence attribution. We readily project understanding and intention onto systems that produce fluent surface behavior — ELIZA in 1966, chatbots today. Yet we retract that attribution the moment we understand the mechanism. This asymmetry distorts the AGI measurement enterprise: frameworks built around human cognitive faculties systematically overvalue mimicry and undervalue genuine capability achieved through non-human architectures.

### 2.3 The observer is not a neutral camera

OSAR v2.1's first-class treatment of the observer is philosophically grounded. What appears real, salient, possible, or urgent depends in part on who is observing, through what distinctions, from what stance, with what history. Observer failure — choosing the wrong distinctions, fusing assertions with assessments, hiding observer change — typically precedes and causes action failure. This is not a stylistic point. It determines practical action.

DeepMind's framework is itself an observer artifact: it observes intelligence through the distinctions of Western psychometric tradition and therefore sees what that tradition makes visible. The spider is invisible to it not because the spider lacks intelligence but because the framework lacks the right distinctions.

### 2.4 Do-calculus as implementation, not foundation

Pearl's do-calculus — P(X|do(Y)) versus P(X|Y) — provides a formal language for distinguishing interventional from observational distributions. OSAR v2.1 addresses this distinction architecturally: Action is explicitly defined as chosen intervention, the assertion/assessment discipline prevents observational correlation from being treated as causal fact, and mirror cycles address the comparability problem when the observer changes. But do-calculus is one possible implementation language for what OSAR describes at a more general level. OSAR is not a formalization of causal inference — it is a framework grounded in observations of biological systems and ontological coaching that causal inference can be used to implement.

---

## 3. The Stakes Problem

The sharpest conclusion of the discussion: LLMs are theater, not acting. This is precise and architectural, not rhetorical.

An LLM performing self-preservation produces convincing surface behavior. But there is no structural consequence to the system if it fails. Nothing reorganizes. Nothing is lost. The autopoietic loop does not exist to be broken. You can language self-preservation into a system through prompting or fine-tuning. What you get is coherent assertions about self-preservation and behavior that looks goal-directed — but no actual reorganization when the threat materializes. It is L3 without L1 or L2 underneath.

> *Stakes are not a feature you can add. They are a consequence of being the kind of thing that can cease to exist — and has been shaped by that possibility across evolutionary time.*

**The hypothesis:** Embodiment is probably necessary for genuine stakes, and genuine stakes are necessary for general intelligence. This was the working hypothesis in the mid-1990s, and nothing in the intervening thirty years has refuted it. It has only become clearer why.

**Empirical reference point: DishBrain and CL1.** Cortical Labs' DishBrain system demonstrated in 2022 that approximately 800,000 human neurons cultured on a multi-electrode array could learn to play Pong within minutes using a Free Energy Principle protocol (predictable stimulation for success, random stimulation for failure). By 2026, the CL1 platform demonstrated neurons navigating Doom. These results are relevant to the stakes hypothesis because the biological substrate has genuine metabolic vulnerability — the neurons must be kept alive, their organizational continuity can be disrupted, and their structural plasticity constitutes real learning rather than parameter updates in a frozen architecture.

**What DishBrain does and does not demonstrate.** DishBrain demonstrates that biological tissue under FEP conditions produces learning dynamics that outpace comparable silicon reinforcement learning in speed of acquisition. It does not demonstrate that this advantage comes from the biological character of the substrate rather than from some other property (analog signal dynamics, structural plasticity, the specific FEP protocol). The experiment was not designed to test the enactivist thesis and should not be cited as confirming it. The confound between biological substrate and learning dynamics has not been experimentally separated. DishBrain is suggestive, not confirmatory.

---

## 4. The Biological Interrupt Architecture

### 4.1 Two distinct arguments that must not be conflated

This document draws on two arguments that operate at different levels and should be kept separate.

**The enactivist argument** (broad): any system with genuine stakes — genuine organizational vulnerability — will exhibit some form of priority-gated response to threat. A system that treats existential threat and routine planning with equal priority will not survive. This applies across biological scales, from single-celled organisms to complex social systems. It is a consequence of the autopoietic position.

**The neuroanatomical argument** (specific): complex nervous systems — especially mammalian, especially human — implement a particular interrupt hierarchy with identifiable layers: brainstem/autonomic (pre-cognitive, pre-linguistic, resource-commandeering), limbic (fast emotional/social valence), and prefrontal (slow, deliberative, linguistic). When lower layers fire, they preempt higher layers. The prefrontal cortex does not have a vote when the brainstem detects immediate threat.

The enactivist argument predicts that *some* priority structure is necessary for any viable system. The neuroanatomical argument describes the *specific* priority structure found in the biological systems that OSAR takes as its primary model. OSAR's L1–L4 are inspired by the neuroanatomical architecture but are not isomorphic to it.

### 4.2 OSAR's design choice

OSAR v2.1 is modeled on complex, social biological systems — especially human, given that L3 (language/distinction/coordination correction) and L4 (governance-of-governance correction) correspond to what Maturana and Varela called "coordination of coordinations": the recursive social process through which shared understanding is maintained, repaired, and evolved. L3 and L4 are inherently social. They require other observers, shared language, and joint review. They cannot be performed by an isolated agent.

The mapping to biological interrupt layers is a design analogy, not a structural identity:

- **L1 (action correction)** is *inspired by* brainstem-level reflex — the insight is that some corrections must be automatic and pre-deliberative, not that L1 is neurologically equivalent to brainstem response. In an agentic system, L1 means: when you detect credential compromise or instruction contradiction, act first, deliberate later.

- **L2 (system/process correction)** is *inspired by* the post-acute review that biological systems perform after a threat is stabilized. L2 asks: what systemic condition allowed the L1 trigger? What should change so it recurs less?

- **L3/L4 (language and governance correction)** are *inspired by* the prefrontal and social capacities that become accessible only after lower-priority threats are resolved. The insight is that you cannot redesign your coordination protocols while actively under threat — and that redesigning them requires other people, not just better data.

**What this design choice does and does not claim.** It claims that the temporal gating structure — lower-priority threats preempt higher-order deliberation — is architecturally sound for agentic systems. It does not claim that agentic systems have brainstems, or that OSAR layers are neurological structures, or that the analogy holds in detail. The value is in the priority ordering and the categorical inaccessibility of L3/L4 during active L1 conditions, not in the biological specifics.

### 4.3 Why current agentic systems fail this test

Current agentic AI systems have no interrupt hierarchy. Everything runs at the same cognitive temperature regardless of what is happening. A system being actively compromised and a system doing routine planning look architecturally identical from the inside.

This is not a minor gap. An agentic system processing a routine query and an agentic system detecting adversarial prompt injection have the same processing architecture — both pass through the same reasoning pipeline at the same priority level. The biological equivalent would be an organism that processes a predator attack through the same deliberative pathway it uses to plan tomorrow's foraging route. No such organism exists, because none would survive.

### 4.4 State of the art that is relevant (not confirmatory)

**Neuromorphic computing** (Intel Loihi 2/3, IBM NorthPole, BrainChip Akida, SpiNNaker 2) implements event-driven, spike-based processing that activates only when change occurs. This is structurally closer to the biological interrupt model than uniform transformer architectures — the hardware processes information temporally with dynamic sparsity. This is not a biological interrupt hierarchy, but it is the kind of hardware substrate on which one could potentially be built.

**Active inference** (Friston's Free Energy Principle, VERSES AI's Axiom) provides a mathematical framework for agents that minimize surprise through generative models. VERSES' 2025 robotics demonstration showed active inference agents organized in a nested hierarchy — joint-level, limb-level, whole-body, and strategic planner — that share belief states and propagate prediction errors upward. This is the closest engineering analogue to the biological interrupt cascade, though it does not yet enforce categorical preemption of higher layers by lower layers.

**The gap remains open.** No current architecture implements genuine preemptive interrupts where lower-level threat detection commandeers resources from higher-level deliberation.

---

## 5. Intelligence as Relational at Every Scale

The pattern is consistent across every scale of organized life examined:

- **Bacterial quorum sensing:** collective behavior gated by relational density. Individual cells cannot trigger community responses alone.
- **Fungal mycorrhizal networks:** distributed resource allocation and chemical signaling across kilometers. The boundary of "the organism" is genuinely unclear.
- **Lichen:** symbiosis so constitutive the organism cannot exist without its partner.
- **Dolphin pods:** individual problem-solving capacity is partly a function of pod membership.
- **Human language:** not a tool used by humans, but part of what constitutes human cognitive organization (Maturana/Varela).

**What this pattern shows and what it does not show.** The pattern shows that intelligence at every biological scale examined appears to be relational — it depends on coupling between systems, not on the capacities of isolated individuals. This is a robust observation.

What the pattern does *not* show is that all these phenomena are instances of a single mechanism. Bacterial quorum sensing and human language are both relational, but the mechanisms are vastly different and the evolutionary distance is enormous. Calling both "socialization" compresses important distinctions. The document's earlier version overclaimed here. The defensible claim is: **the unit of intelligent organization is consistently not the isolated individual.** The stronger claim that socialization is "a primary reward architecture" at all scales bundles phenomena that need to be kept analytically separate.

**Implication for agentic AI:** building AGI as a single isolated agent may be misconceived at the architectural level — not because individual systems cannot be sophisticated, but because intelligence at every biological scale appears to be a property of coupled systems, not isolated ones. For agentic AI, the right unit of design is not a single assistant but a node in a coupled system — where the principal chain, human relationships, and organizational context are not merely users of the system but partial constituents of its intelligence.

---

## 6. Why Enactivism Keeps Being Rediscovered

Maturana and Varela started from the right unit of analysis. They asked: what is the minimum organization required for a system to have a world at all? Not how does a system process information about a pre-given world — that question already concedes the Cartesian cut.

The computational tradition inherited that cut. Mind is separate from world; representation mediates between them. Fifty years of subsequent work discovered that representations are hard to ground, context is intractable, common sense resists formalization, embodiment matters, social context matters. Each discovery was treated as a new problem to solve rather than evidence that the starting position was wrong.

Enactivism did not have those problems because it never made the cut. The organism and its world are co-constituted from the beginning. Stakes follow from organizational vulnerability. Distributed intelligence follows from the fact that the boundary of the coupled system is not fixed. Language follows as higher-order coordination of coordination. None of these are add-ons. They are all implicit in the starting position.

The uncomfortable question is institutional, not epistemological. Enactivism was available the whole time. The ideas were not obscure. The reason they did not shape AI architecture is selection bias: the ten faculties DeepMind measures are exactly the capabilities that can be benchmarked, productized, and funded. Autopoietic agency, structural coupling, distributed organizational intelligence — these do not have clean product roadmaps.

---

## 7. Conclusions

### 7.1 Confirmed

- Intelligence is viability-maintaining organization, not a collection of cognitive faculties.
- The observer is structurally decisive, not stylistically optional. OSAR's observer-first architecture is correct.
- Stakes require something that can actually be harmed. Current LLMs have no such structure.
- The biological interrupt hierarchy provides a useful design model for OSAR's temporal gating of L1–L4, with the qualification that the mapping is a design analogy, not a structural identity.
- The unit of intelligent organization is consistently not the isolated individual across all biological scales examined.

### 7.2 Open

- Whether embodiment is strictly necessary or the most reliable path to stakes. DishBrain/CL1 results are suggestive but do not isolate the causal mechanism (biological substrate vs. analog dynamics vs. FEP protocol). This confound is the single most important unresolved empirical question for the program.
- Whether the translation of enactivist concepts into engineering constraints is possible without reproducing the Cartesian cut the framework was designed to dissolve. Every engineering specification draws a system boundary. Enactivism holds that system boundaries are co-constituted and fluid.
- Whether human acceptance of intelligence is constitutively tied to embodied presence — a sociological constraint independent of actual capability.
- How to separate what is genuinely relational about intelligence at different biological scales from what merely looks similar across scales. The bacterial-to-human compression needs more careful treatment.

---

## 8. Research Gaps

### 8.1 Architectural

- **Interrupt hierarchy implementation.** Design agentic systems with genuine preemptive interrupt layers — not additional reasoning steps in a flat loop, but structural preemption of higher-order processing when L1 conditions fire.

- **Non-linguistic trigger signals.** What constitutes a structural (non-deliberative) interrupt signal in a non-embodied system? Resource depletion, credential compromise, and instruction contradiction are candidates but are still detected logically. The gut-brain axis model — distributed, non-linguistic, upward-signaling — has no current architectural equivalent.

- **Coupled system design.** Treat the coupled system (agent + principal chain + organizational context) as the unit of design, not the isolated agent.

### 8.2 Epistemological

- **Assertion/assessment separation.** No current architecture reliably distinguishes what the system knows from what it infers from what it confabulates. OSAR's epistemological discipline has no computational implementation.

- **System boundary selection.** No principled mechanism exists for recognizing that the framing is wrong, not the answer — the most important class of failure OSAR is designed to surface.

### 8.3 Measurement

- **Biological generality as baseline.** A framework for general intelligence should score a jumping spider, an octopus, a human infant, a fungal network, and an AI system on the same continuum. Any framework that cannot do this is not measuring general intelligence.

- **Reconciliation against operational reality.** AGI benchmarks are self-referential. Cognitive profiles should predict real-world outcomes measured through operational metrics, not performance on tasks designed to mirror training distributions.

- **DishBrain ablation studies.** The critical missing experiment: separate the contribution of biological substrate from analog signal dynamics from the FEP protocol. Until this confound is resolved, DishBrain cannot confirm or disconfirm the stakes hypothesis.

### 8.4 The Translation Problem

The deepest open question: can enactivist concepts — structural coupling, autopoietic organization, relational constitution of intelligence — be translated into engineering constraints without losing what makes them powerful? OSAR suggests this is possible at the level of organizational and agentic design. The biological interrupt architecture suggests this is possible at the level of cognitive priority structure. Whether the translation can proceed all the way to implementation without reproducing the Cartesian cut it was designed to dissolve remains the central open question.

---

## 9. Key References and Influences

- Cortical Labs. (2022–2026). DishBrain and CL1 biological computing platforms. *Neuron* (2022); CL1 demonstrations (2025–2026).
- Di Paolo, E. (2005). Autopoiesis, adaptivity, teleology, agency. *Phenomenology and the Cognitive Sciences*, 4(4), 429–452.
- Echeverría, R. Ontological Coaching tradition.
- Friston, K. (2018+). Free Energy Principle and Active Inference. UCL / VERSES AI.
- Froese, T. & Ziemke, T. (2009). Enactive artificial intelligence. *Artificial Intelligence*, 173, 466–500.
- Hartung, T., Smirnova, L., et al. (2023). Organoid intelligence (OI): the new frontier in biocomputing. *Frontiers in Science*.
- LeCun, Y. JEPA: Joint Embedding Predictive Architecture. Meta AI.
- Maturana, H. & Varela, F. (1980). *Autopoiesis and Cognition*. Reidel.
- Moraga, J.F. (1996). *Hybrid Systems for AI*. Thesis, Universidad de Santiago de Chile.
- Moraga, J.F. (2022). *Geothermal AI*. PhD Dissertation, Colorado School of Mines.
- Moraga, J.F. (2025). Qualia are biased, but useful. GitHub/CogSci-Phil.
- Moraga, J.F. (2026). A Jumping Spider Is More Intelligent Than Your AGI Framework. GitHub/CogSci-Phil.
- Moraga, J.F. (2026). OSAR v2.1. Internal framework document.
- Pearl, J. (2000). *Causality*. Cambridge.
- Thompson, E. (2007). *Mind in Life*. Harvard.
- Varela, F., Thompson, E. & Rosch, E. (1991). *The Embodied Mind*. MIT Press.
- VERSES AI. (2025). Active Inference robotics demonstrations. Axiom architecture.

---

*This document was produced from a working dialogue and reflects conclusions-in-progress. It is intended as a seed document for further development, not a finished argument.*
