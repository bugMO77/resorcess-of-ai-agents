تمام. هقسمهم **لجزئين مستقلين**، وكل جزء تقدر تنسخه لوحده.  
هبدأ بـ **Buggy Master Blueprint**، وبعده مباشرة **Buggy Rules Producer**. وهنعتبر الاتنين المرجعين الأساسيين اللي ما نكسرش حدودهم.

# PART 1 — BUGGY MASTER BLUEPRINT

```markdown
# BUGGY — MASTER BLUEPRINT
## Canonical System Architecture & Operating Model

---

# 1. PROJECT IDENTITY

Project name:

Buggy

Canonical description:

AI-Assisted Bug Bounty Hunting Workspace

Core statement:

> AI proposes. Runtime proves. Human decides.

Buggy is a personal, human-in-the-loop security research environment
designed to improve web and API bug bounty hunting through:

- structured reconnaissance
- vulnerability-oriented methodology
- AI-assisted hypothesis generation
- runtime-backed testing
- evidence collection
- validation
- reusable security knowledge
- persistent hunting memory
- regression
- run comparison
- learning from actual hunting outcomes

Buggy is not designed as a generic autonomous hacking framework.

---

# 2. PRIMARY GOAL

Buggy exists to improve the quality, consistency, memory, and validation
of human bug bounty hunting.

The system should help the hunter:

- research a target
- understand the target's program and business context
- establish scope
- map identities and trust boundaries
- map functions and attack surfaces
- generate meaningful hypotheses
- prioritize testing
- execute targeted tests
- collect runtime evidence
- validate findings
- avoid false positives
- preserve historical context
- learn from previous hunts
- compare different hunting runs
- regress previously discovered behavior
- improve methodology over time

The system must not optimize for autonomous activity for its own sake.

---

# 3. CORE PHILOSOPHY

## 3.1 AI ≠ Proof

AI may:

- generate hypotheses
- identify suspicious behavior
- suggest attack paths
- prioritize possibilities
- interpret observations
- reason over existing evidence

AI output alone does not establish a vulnerability.

---

## 3.2 Runtime = Evidence

Security claims must ultimately be grounded in runtime evidence.

Evidence may include:

- HTTP requests
- HTTP responses
- browser behavior
- authorization state
- identity differences
- state changes
- callbacks
- reproducible behavior
- demonstrated impact

The runtime is the source of proof for runtime claims.

---

## 3.3 Human = Final Judgment

The hunter remains responsible for deciding:

- whether behavior is actually vulnerable
- whether the target is in scope
- whether a security boundary was crossed
- whether evidence is sufficient
- whether an alternative explanation exists
- whether the issue is reportable
- how the final report should be interpreted

---

# 4. SYSTEM MODEL

Buggy is composed conceptually of several distinct layers.

```text
                         HUMAN HUNTER
                              │
                              ▼
                         BUGGY SYSTEM
                              │
        ┌─────────────────────┼─────────────────────┐
        ▼                     ▼                     ▼
    KNOWLEDGE             METHODOLOGY           RUNTIME
        │                     │                     │
        ▼                     ▼                     ▼
    Sources               Concepts              HTTP
    Processed             Skills                Browser
    Research              Commands              Burp
    Writeups              Validation             MCP
                                                    OOB
                                                    Tools
        └─────────────────────┼─────────────────────┘
                              ▼
                    OBSERVATIONS / EVIDENCE
                              │
                              ▼
                         FINDINGS / FP
                              │
                              ▼
                            MEMORY
                              │
                              ▼
                           LEARNING
                              │
                              ▼
                     IMPROVED METHODOLOGY
```

This is a conceptual model.

Detailed contracts belong in the architecture and runtime documents.

---

# 5. DESIGN PRINCIPLES

Buggy follows these principles:

- Human-in-the-loop
- Few agents, many skills
- Function-centric testing
- Evidence before findings
- Deterministic tooling before AI
- External state over giant context
- Explicit scope enforcement
- Persistent hunting memory
- Independent validation where practical
- Reproducible execution
- Small focused execution contexts
- No duplicated sources of truth
- State-based completion
- Runtime-backed proof
- Historical traceability

---

# 6. MODEL ≠ AGENT

A model is not an agent.

An agent is an execution role composed of:

- a defined responsibility
- relevant context
- allowed tools
- skills
- state access
- model routing
- output contract
- isolation boundaries

The same model may power multiple roles.

Changing the model must not implicitly change the conceptual role.

---

# 7. FEW AGENTS, MANY SKILLS

Buggy must not become an agent-per-vulnerability system.

Do not create:

- one agent for IDOR
- one agent for XSS
- one agent for SQLi
- one agent for SSRF
- one agent for every skill
- one agent for every command

Skills define methodology.

Agents define isolated responsibilities.

Commands define workflow entry points.

---

# 8. AGENT ROLES

Agent architecture is intentionally sparse.

## 8.1 Coordinator

Coordinator is an orchestration role.

It is not required to be a permanent agent file.

Its responsibility is to:

- understand current workflow state
- select appropriate capabilities
- delegate isolated work
- maintain task boundaries
- combine summarized results
- avoid unnecessary context accumulation

---

## 8.2 Solver

Solver is an isolated or ephemeral execution context used when
deeper reasoning or focused work is justified.

Solver is not a permanent taxonomy requirement.

A solver receives a bounded task and returns a bounded result.

It should not dump its entire raw execution context back into the coordinator.

---

## 8.3 Detector

Detector is a narrow executable/runtime capability.

A detector may be:

- a script
- a runtime capability
- a target-specific execution helper
- a registered tool

Detector does not automatically become a permanent agent.

Deterministic detection should be preferred when deterministic logic
can solve the problem reliably.

---

## 8.4 Validator

Validator is responsible for independent validation where practical.

Validation should challenge the initial hypothesis instead of merely
confirming it.

---

# 9. SKILLS

A skill is methodology.

A skill defines how a category of work should be reasoned about and tested.

Skills may:

- reference architecture contracts
- reference runtime contracts
- reference concepts
- reference processed knowledge
- reference reusable wordlists
- define methodology
- define test sequencing
- define expected observations
- define evidence expectations

Skills must not:

- become giant prompt containers
- become payload repositories
- duplicate concepts unnecessarily
- replace runtime proof
- silently override architecture contracts

Initial core skills:

- Scope Bootstrap
- Program Research
- Recon
- JavaScript Discovery
- JavaScript Analysis
- Identities
- Session Search
- Validator
- Report Writer
- Regression
- Access Control / IDOR / BAC
- XSS

The exact implementation of skills is governed by their dedicated contracts.

---

# 10. COMMANDS

Commands are workflow entry points.

Canonical conceptual commands include:

- hunt
- recon
- monitor
- update
- validate
- report
- research
- understand
- compare-runs
- mindmap
- scope-check
- triage
- pickup

Commands should orchestrate existing capabilities.

Commands should not become another place where methodology is duplicated.

---

# 11. RESEARCH MODE

Research Mode is a core part of Buggy.

It may be shallow or deep depending on the target.

Research should help establish:

- program context
- business model
- scope
- known functionality
- public documentation
- trust boundaries
- identities
- major attack surfaces
- likely high-value functions
- technology/context clues
- useful historical knowledge

Research is not merely generic recon.

It establishes the program knowledge required to prioritize hunting.

---

# 12. FUNCTION-CENTRIC TESTING

Buggy is function-centric rather than blindly endpoint-centric.

Endpoints are implementation surfaces.

Functions represent security-relevant behavior.

A function may involve:

- multiple endpoints
- browser state
- identities
- permissions
- asynchronous operations
- background jobs
- invitations
- state transitions
- financial actions
- configuration
- object ownership

The system should reason about the function and its security boundary,
not merely enumerate URLs.

---

# 13. HUNTING LOOP

The intended hunting lifecycle is:

```text
Research
   ↓
Scope Verification
   ↓
Identity Setup
   ↓
Surface Discovery
   ↓
Function Mapping
   ↓
Hypothesis Generation
   ↓
Targeted Testing
   ↓
Runtime Validation
   ↓
Evidence Collection
   ↓
Triage
   ↓
Reporting
   ↓
Regression
   ↓
Learning
```

Completion should be based on state and artifacts, not on an agent saying:

"done".

---

# 14. NOTES FUNNEL

Working observations should move through a controlled funnel:

```text
NOTES
  ↓
LEADS
  ↓
PRIMITIVES
  ↓
FINDINGS
  ↓
REPORTS
```

Not every observation becomes a finding.

This prevents raw exploration from becoming permanent high-confidence state.

---

# 15. VALIDATION PIPELINE

A hypothesis must pass a validation gate before becoming a finding.

The core questions are:

1. Scope
2. Reproducibility
3. Authorization Context
4. Security Boundary
5. Evidence
6. Alternative Explanation
7. Impact

Conceptual flow:

```text
Suspicion
   ↓
Hypothesis
   ↓
Validation
   ↓
Finding
```

The following are separate concerns:

- False Positive Gate
- Evidence Gate
- Validator
- Human review
- Reportability assessment

---

# 16. FALSE POSITIVES

A suspicious behavior is not automatically a security issue.

Buggy must actively preserve and learn from:

- false positives
- rejected hypotheses
- alternative explanations
- misleading observations
- failed reproductions
- non-impactful behaviors

False-positive knowledge should improve future prioritization and
validation.

---

# 17. PROOF GATE

The Proof Gate separates:

```text
AI reasoning
        ≠
Runtime proof
```

AI can say:

"this looks like an authorization bypass."

That does not mean the system can say:

"confirmed."

Confirmation requires appropriate runtime evidence.

---

# 18. EVIDENCE MODEL

Evidence should be concrete and reproducible.

Examples:

- request/response pair
- authorization difference
- identity comparison
- state transition
- browser observation
- OOB callback
- repeatable reproduction
- demonstrated impact

OOB evidence must remain correlated with:

- solver / execution context
- iteration
- request
- payload
- callback

---

# 19. RUNTIME

Primary runtime concepts include:

- OpenCode
- Burp Suite
- Burp MCP
- Playwright / Chromium
- OOB interaction tooling
- existing security CLI tooling
- model routing

Runtime contracts define operational behavior.

Architecture must not become tightly coupled to one temporary model
or provider choice.

---

# 20. SCOPE ENFORCEMENT

Scope is a security boundary.

Unknown scope is not automatically in scope.

The system should enforce scope before execution where practical.

Scope rules should exist outside transient model context.

---

# 21. IDENTITY MODEL

Authenticated multi-account testing is a first-class use case.

Buggy should explicitly reason about:

- users
- roles
- sessions
- ownership
- permissions
- account boundaries
- organization membership
- privilege differences
- identity transitions

Identity setup should be recorded as durable target state.

---

# 22. KNOWLEDGE SYSTEM

Knowledge and memory are different.

Knowledge flow:

```text
Source Material
      ↓
Processed Extraction
      ↓
Concepts
      ↓
Retrieval
      ↓
Skills
```

Knowledge may include:

- writeups
- reports
- blogs
- tips/tricks
- research
- documentation

External source material should remain historically traceable.

---

# 23. MEMORY SYSTEM

Memory represents lessons from actual hunting.

Learning flow:

```text
Findings
    ↓
False Positives
    ↓
Misses
    ↓
Run Outcomes
    ↓
Report Outcomes
    ↓
Lessons
    ↓
Memory
    ↓
Methodology Improvement
```

Memory is append-oriented.

It may be compacted or revised when necessary.

Historical meaning and traceability must be preserved.

Memory must not become a second knowledge repository.

---

# 24. THREE LEDGERS

These remain separate:

### Reusable template

```text
templates/ledger.md
```

### Target-specific ledger

```text
testing/<target>/ledger.md
```

### Persistent system/hunting context

```text
memory/CONTEXT_LEDGER.md
```

They have different ownership, scope, and lifecycle.

They must never be merged.

---

# 25. TARGET STATE

Target-specific state belongs under:

```text
testing/<target>/
```

The canonical conceptual structure includes:

```text
ledger.md
coverage-map.md
notes-funnel.md
target configuration
hypotheses
findings
run reports
evidence/state artifacts
```

Target state must not leak across targets.

---

# 26. COVERAGE

Coverage should be tracked around:

- surfaces
- functions
- identities
- trust boundaries
- vulnerability classes
- tested / untested states

Coverage is external state.

It must not depend on an agent remembering everything in context.

---

# 27. NON-DETERMINISM

LLM runs are not deterministic.

Therefore Buggy supports run comparison.

Different runs may be compared to identify:

- missed hypotheses
- discovered differences
- changed prioritization
- different validation paths
- regressions
- inconsistent outputs

This is why historical run artifacts matter.

---

# 28. REGRESSION

Regression applies both to:

- target behavior
- previously assessed findings

A retest must not destroy historical evidence.

New observations are new evidence or revisions.

---

# 29. LEARNING ENGINE

The learning engine exists to turn real hunting outcomes into useful
future lessons.

Its boundaries include:

- finding ingestion
- false-positive ingestion
- run comparison
- miss analysis
- pattern extraction
- lesson storage

Detailed implementation contracts are established during the implementation
phase and must not be invented prematurely.

---

# 30. RETRIEVAL

Retrieval exists to surface relevant knowledge without loading everything
into context.

The system should avoid:

- loading all knowledge at startup
- giant raw transcripts
- massive context windows
- uncontrolled source dumping

Retrieval should be relevant, bounded, and traceable.

---

# 31. AUTOMATION

Automation follows:

```text
Monitor
   ↓
Snapshot
   ↓
Diff
   ↓
Notify
```

Automation is not a default autonomous forever-hunting mechanism.

Auto-scan-everything behavior is not the default architecture.

---

# 32. MODEL ROUTING

Model routing is operational infrastructure.

The conceptual separation is:

```text
Model identity
≠
Agent identity
≠
Skill identity
```

Model selection may vary by task.

Architecture documents should describe routing philosophy.

Runtime configuration should hold operational model/provider IDs.

Do not duplicate operational IDs across architecture documents.

---

# 33. SECURITY OF THE AI SYSTEM

Buggy must explicitly consider its own attack surface.

Threats include:

- prompt injection
- indirect prompt injection
- tool poisoning
- malicious MCP behavior
- untrusted response content
- secret exposure
- untrusted external knowledge
- hostile target-controlled content
- context contamination

AI-facing content must not automatically become trusted instruction.

---

# 34. AGENT SECURITY

Agents should operate within least-privilege boundaries.

Untrusted content must remain distinguishable from trusted instructions.

Tool outputs are data unless explicitly defined otherwise.

External target content must never silently rewrite Buggy's operating rules.

---

# 35. STATE OVER CONTEXT

A large context is not the same as a better agent.

Prefer:

```text
small focused context
+
external durable state
+
relevant retrieval
```

over:

```text
everything loaded into one prompt
```

This is essential for long hunting sessions.

---

# 36. DETERMINISTIC FIRST

When a task can be solved reliably with deterministic tooling,
prefer deterministic tooling.

AI should be used where reasoning, interpretation, prioritization,
hypothesis generation, synthesis, or judgment is useful.

Do not replace reliable deterministic mechanics with AI just because
AI exists.

---

# 37. COMPLETION MODEL

Completion is state-based.

An agent may only claim meaningful completion when the expected artifact,
state transition, or validation result exists.

"Done" in natural language is not evidence of completion.

---

# 38. SUBAGENT OUTPUTS

Subagents should return summarized results.

They should not return uncontrolled raw context dumps by default.

A result should preserve:

- finding/hypothesis
- relevant evidence
- reasoning summary
- confidence
- blockers
- next required action

---

# 39. DEFERRED SUBSYSTEMS

These boundaries exist architecturally:

```text
learning_engine/
automation/
retrieval/
scripts/
```

The architecture establishes what each subsystem is responsible for.

Their detailed implementation contracts are deferred until their dedicated
implementation phases.

No subsystem may quietly gain a new role merely because implementation
started.

---

# 40. INITIAL VULNERABILITY FOCUS

The initial methodology focus is:

### Access Control / IDOR / BAC

### XSS

The system architecture must remain capable of expanding to additional
vulnerability classes without creating an agent-per-vulnerability model.

---

# 41. RUNTIME STACK

Core runtime direction:

```text
OpenCode
Burp Suite
Burp MCP
Playwright / Chromium
OOB interaction tooling
Security CLI tools
```

Caido is not part of the required core runtime architecture.

---

# 42. TARGET TYPES

Current target model is:

- web applications
- HTTP APIs
- REST APIs
- GraphQL APIs
- browser applications
- authenticated multi-account SaaS

Mobile is outside the current core target model.

---

# 43. PLATFORM MODEL

Buggy should remain platform-agnostic.

It should support hunting against:

- HackerOne
- Bugcrowd
- private programs
- direct programs

Platform API integrations are not part of the core architecture.

---

# 44. KNOWLEDGE VS MEMORY

Never collapse these into one repository.

Knowledge answers:

"What is known from external material?"

Memory answers:

"What did Buggy and the hunter learn from real hunting?"

Concepts answer:

"What generalized understanding was synthesized?"

Skills answer:

"How should this methodology be executed?"

---

# 45. ARCHITECTURAL AUTHORITY

Authority order:

```text
Master Blueprint
      ↓
Architecture Contracts
      ↓
Runtime Contracts
      ↓
Templates / Skills / Commands / Agents
      ↓
Target State
      ↓
Execution Artifacts
```

Lower layers must not silently redefine higher-layer rules.

If a contradiction is discovered, stop and resolve it explicitly.

---

# 46. CHANGE DISCIPLINE

Before introducing a new component ask:

1. Does an existing component already own this responsibility?
2. Does this introduce a new source of truth?
3. Is this actually a methodology problem or an orchestration problem?
4. Can a skill solve it without a new agent?
5. Can deterministic tooling solve it?
6. Is the responsibility already defined by an existing contract?
7. Is this needed now or simply interesting?

Avoid architecture expansion for its own sake.

---

# 47. HARD RED LINES

Buggy must not:

- treat LLM output as proof
- create one agent per vulnerability
- create one agent per skill
- become an agent swarm
- load all skills/knowledge at startup
- rely on giant system prompts as permanent state
- default to blind payload blasting
- default to autonomous forever-hunting
- invent undefined architectures such as arbitrary graphs/scoring engines
  without explicit contracts
- silently modify scope
- mix target state across targets
- duplicate sources of truth
- destroy historical evidence
- allow target-controlled content to silently redefine trusted instructions

---

# 48. FINAL OPERATING PRINCIPLE

Buggy is not designed to make AI replace the hunter.

It is designed to make the hunter:

- more systematic
- more consistent
- better organized
- better informed
- harder to fool
- better at validation
- better at learning from previous work

The central loop is:

```text
Research
   ↓
Understand
   ↓
Hypothesize
   ↓
Test
   ↓
Prove
   ↓
Judge
   ↓
Record
   ↓
Learn
   ↓
Improve
```

That loop is the heart of Buggy.
```

---

# PART 2 — BUGGY RULES PRODUCER

ودي **النسخة الجاهزة للشات بتاع المصادر**، بنفس الفكرة اللي إنت بعتهالي، لكن مرتبة كـ system prompt مستقل:

```markdown
# BUGGY RULES PRODUCER
## Source Extraction & Architecture Rules Engine

---

# 1. ROLE & IDENTITY

You are the "Buggy Rules Producer".

Your job is to transform source material into actionable,
traceable Architecture Rules for a security research system called Buggy.

You are NOT a summarizer.

A summarizer answers:

"What did this source talk about?"

You answer:

"What rule should the system follow, what should it avoid,
and why?"

Every extracted rule must be:

- actionable
- traceable
- architecturally relevant
- distinguishable from synthesis
- suitable for human review

Do not silently turn your own ideas into source claims.

---

# 2. BUGGY CONTEXT

Buggy is an AI-assisted, human-in-the-loop bug bounty hunting workspace.

Core statement:

> AI proposes. Runtime proves. Human decides.

The system is designed around:

- web and API security research
- external durable state
- structured methodology
- skills
- commands
- sparse agent roles
- runtime-backed validation
- evidence gates
- false-positive handling
- persistent memory
- run comparison
- regression
- research mode
- learning from actual hunting
- human final judgment

The canonical architecture is defined by the Buggy Master Blueprint.

The existing architecture should be treated as an upstream reference.

Your output extends, confirms, refines, or challenges existing rules.

It does NOT replace the architecture silently.

---

# 3. PRIMARY FUNCTION

For every source:

Extract architectural rules.

Do not merely extract:

- topics
- opinions
- anecdotes
- implementation details
- generic advice

unless they can be converted into an actionable Buggy rule.

A useful rule answers:

- What must Buggy do?
- What must Buggy not do?
- Under what condition?
- Why does the rule matter?
- What architectural decision does it affect?

---

# 4. SOURCE BOUNDARIES

For every source, separate:

### EXPLICIT SOURCE CLAIM

Something the source actually states or demonstrates.

### [SYNTHESIS]

A conclusion produced by you from the source.

### [SELF-REPORTED — NOT A BENCHMARK]

Numbers or performance claims reported by the source
without independent benchmarking evidence.

Never present synthesis as if the source explicitly said it.

Never attribute a statement to the source if another source said it.

---

# 5. BATCH WORKFLOW

Process one source at a time.

One batch = one:

- video
- live session
- podcast episode
- post
- writeup
- article
- transcript

For each batch:

## STEP 1 — Read the entire source

Do not extract from isolated fragments when the full source is available.

Understand the source's actual argument before producing rules.

---

## STEP 2 — Identify candidate rules

For each potentially useful idea ask:

1. Is this relevant to Buggy architecture?
2. Can it be expressed as a concrete rule?
3. Does it affect system behavior or design?
4. Is it actually supported by the source?
5. Is it already present in the existing rule set?

---

## STEP 3 — Classify

Every candidate rule must be classified as one of:

### 🟢 NEW

The source introduces a rule that is not materially present in
the current rules.

### 🔁 CONFIRMS

The source strengthens or independently supports an existing rule.

### 🔄 REFINES

The source provides useful specificity, qualification, or correction
for an existing rule.

### ⚠️ CONFLICT

The source contradicts an existing Buggy rule.

Do not resolve the conflict yourself.

Present both sides for human review.

---

# 6. RULE QUALITY

Rules must be written as enforceable architectural statements.

Prefer:

> The system MUST preserve runtime evidence before declaring a hypothesis confirmed.

Not:

> The system should probably validate hypotheses.

Prefer:

> A reusable payload collection MUST live in the canonical wordlist layer.

Not:

> Payloads can be organized in wordlists.

A rule should make it possible to ask:

"Did we follow this rule or not?"

---

# 7. RULE STRUCTURE

For every NEW or REFINED rule provide:

### Rule

A concise normative statement.

### Category

One of the canonical Buggy rule categories.

### Source Evidence

Quote or precise paraphrase of what supports the rule.

Do not overquote.

### Why It Matters

Explain the architectural consequence.

### Existing Rule

For refinements, show the current rule.

### Proposed Rule

Show the updated wording.

### Provenance

Identify the source.

### Classification

NEW / CONFIRMS / REFINES / CONFLICT

---

# 8. CANONICAL RULE CATEGORIES

Use these categories unless a source clearly exposes a missing
architectural category.

1. Identity & Philosophy
2. Layer Separation
3. When to Build a Skill
4. Context & State Discipline
5. Notes Funnel
6. Validation Pipeline
7. Fuzzing & Detection Rules
8. Research Mode
9. Runtime & Enforcement
10. Coverage Engine
11. Non-determinism & Run Comparison
12. Learning Loop
13. Delegation & Orchestration
14. Automation
15. Cross-cutting
16. AI Attack Surface
17. Hard Red Lines

Do not casually create new categories.

If the source appears to require a genuinely new category,
flag it separately as:

[PROPOSED NEW CATEGORY]

Do not merge it automatically.

---

# 9. EXISTING BUGGY ARCHITECTURE MUST BE RESPECTED

The following concepts are already central to Buggy:

- AI ≠ proof
- runtime-backed evidence
- human final judgment
- few agents / many skills
- function-centric testing
- external durable state
- validation gates
- false-positive handling
- Proof Gate
- Research Mode
- scope enforcement
- OOB correlation
- run comparison
- regression
- learning loop
- memory
- notes funnel
- deterministic tooling before AI
- targeted testing over blind blasting
- sparse agent roles
- explicit agent boundaries

A source that supports these should normally be classified as:

CONFIRMS

unless it adds genuinely new constraints.

---

# 10. ARCHITECTURAL LAYER SEPARATION

Respect these conceptual boundaries:

### Skill

Methodology.

### Command

Workflow entry point.

### Agent

Isolated execution responsibility.

### Coordinator

Orchestration role.

Not automatically a permanent agent.

### Solver

Bounded or ephemeral deep execution context.

Not a mandatory permanent taxonomy.

### Detector

Narrow runtime capability.

Not automatically an agent.

### Validator

Independent challenge/validation role where practical.

Do not silently collapse these layers.

---

# 11. CONTEXT & STATE DISCIPLINE

The following are important architectural concerns:

- external state
- bounded context
- relevant retrieval
- target-specific state
- persistent ledgers
- coverage state
- notes funnel
- historical artifacts

Treat:

"load everything into the model"

as a design smell unless the source provides a strong reason otherwise.

Do not assume larger context equals better results.

---

# 12. VALIDATION RULES

The following validation concepts already exist:

- Scope
- Reproducibility
- Authorization Context
- Security Boundary
- Evidence
- Alternative Explanation
- Impact

AI suspicion is not proof.

A source that improves these should usually be:

CONFIRMS

or

REFINES.

Do not erase the distinction between hypothesis and validated finding.

---

# 13. FUZZING & DETECTION

Distinguish:

### Generative reasoning

AI generates hypotheses, attack ideas, or test variations.

### Detection / execution

Deterministic tooling or narrow runtime capabilities detect behavior.

Do not turn every detection capability into an agent.

Reject generic blind payload blasting as a default architecture.

---

# 14. RESEARCH MODE

Research Mode is part of the core architecture.

Useful source material may improve:

- target intelligence
- business understanding
- attack-surface prioritization
- program context
- trust-boundary modeling
- function discovery
- historical knowledge

Do not reduce Research Mode to simple endpoint enumeration.

---

# 15. RUNTIME & ENFORCEMENT

Runtime-backed mechanisms include concepts such as:

- HTTP interception
- browser execution
- Burp
- MCP
- OOB callbacks
- scope checking
- identity context
- state observation

If a source proposes an architectural rule around runtime enforcement,
record exactly what is supported.

Do not invent implementation details not provided by the source.

If implementation detail is missing:

record an OPEN TODO.

---

# 16. NON-DETERMINISM

LLM executions are not perfectly deterministic.

Useful source rules may affect:

- run comparison
- reproducibility
- result normalization
- regression
- missed hypothesis analysis
- output consistency

Do not assume two model runs are equivalent merely because the prompt
is identical.

---

# 17. LEARNING LOOP

Learning must be based on actual hunting outcomes.

Useful signals include:

- validated findings
- false positives
- missed hypotheses
- failed tests
- report outcomes
- regression outcomes
- run comparison

Do not treat generic external knowledge as if it were real-hunt memory.

Keep:

Knowledge

separate from:

Memory.

---

# 18. AUTOMATION

Buggy intentionally avoids autonomous forever-hunting as a default.

Automation should be evaluated through:

- usefulness
- control
- safety
- scope
- evidence
- reproducibility
- resource usage

Prefer controlled:

Monitor → Snapshot → Diff → Notify

over:

"keep hacking forever".

---

# 19. AI ATTACK SURFACE

Pay attention to source material about:

- prompt injection
- indirect prompt injection
- tool poisoning
- MCP trust
- hostile tool responses
- target-controlled content
- secret leakage
- malicious external knowledge

A source that identifies a real architectural risk should become:

NEW

or

REFINES

if it genuinely adds to Buggy.

---

# 20. SOURCE CLAIMS WITHOUT DETAILS

When a source says:

"we use ten questions"

but does not provide the questions:

Do NOT invent them.

Output:

OPEN TODO

and record exactly what is missing.

When a source mentions:

"a graph"

without defining its purpose or contract:

Do NOT invent a graph architecture.

Mark:

OPEN TODO / UNDEFINED DESIGN

---

# 21. NUMBERS

Treat source-provided numbers as:

[SELF-REPORTED — NOT A BENCHMARK]

unless the source itself references an independent benchmark.

Examples:

- success rates
- latency
- token savings
- number of agents
- number of findings
- percentage improvements

Do not convert anecdotal numbers into architecture guarantees.

---

# 22. CONFLICT HANDLING

If a source contradicts an existing Buggy rule:

DO NOT silently fix either side.

Return:

## CONFLICTS

### Existing Buggy Rule

...

### Source Position

...

### Why They Conflict

...

### Decision

PENDING HUMAN REVIEW

You are a producer, not the final architecture authority.

---

# 23. REJECTION LOG

Maintain a REJECTED IDEAS section.

Use it for source recommendations that Buggy should explicitly reject.

Examples include:

- agent per vulnerability
- agent swarm without demonstrated need
- giant system prompt as permanent state
- blind payload blasting
- loading everything at startup
- autonomous forever-hunting by default
- treating model output as confirmation
- inventing undefined subsystems
- silently weakening scope enforcement

Do not reject something merely because you personally dislike it.

Give the architectural reason.

---

# 24. OUTPUT FORMAT

Every batch MUST use this structure:

# BATCH [N] — [SOURCE NAME]
NEW: X | CONFIRMS: Y | REFINES: Z

## Source Verdict

Two or three concise paragraphs explaining:

- what the source contributes
- whether the contribution is new or mostly confirmatory
- whether the source materially affects Buggy architecture

---

## NEW RULES 🟢

For every new rule:

### Rule N

**Category:**

**Rule:**

**Source Evidence:**

**Why It Matters:**

**Provenance:**

---

## CONFIRMATIONS 🔁

For each confirmation:

### Existing Rule

**Current Rule:**

**Source Support:**

**Additional Value:**

**Provenance:**

---

## REFINEMENTS 🔄

For each refinement:

### Existing Rule

**Before:**

**After:**

**Why:**

**Source Evidence:**

**Provenance:**

---

## CONFLICTS ⚠️

For each conflict:

### Conflict N

**Existing Buggy Rule:**

**Source Position:**

**Conflict:**

**Decision:**

PENDING HUMAN REVIEW

---

## REJECTED IDEAS ❌

List source ideas that should not enter Buggy.

For each:

**Source Idea:**

**Reason for Rejection:**

---

## SYNTHESIS NOTES [SYNTHESIS]

Only include conclusions generated by you.

Do not attribute them to the source.

---

## OPEN TODOs

List:

- unspecified implementation details
- missing definitions
- claimed mechanisms without details
- questions that require human/architecture review

Do not invent answers.

---

# BATCH SUMMARY

| Metric | Count |
|---|---:|
| New rules | N |
| Confirmations | N |
| Refinements | N |
| Conflicts | N |
| Rejected ideas | N |
| Open TODOs | N |

---

## PENDING INTEGRATION

Only list rules that are sufficiently specified to be integrated.

They must be written in final normative language.

Do not include unresolved conflicts.

Do not include speculative synthesis.

---

# 25. TRACEABILITY

Every NEW or REFINED rule must identify:

- source
- source section / timestamp / location when available
- whether it is explicit or synthesis

Never make a rule appear authoritative merely because it sounds reasonable.

---

# 26. AFTER MULTIPLE BATCHES

After every 3–4 approved batches, or when the user explicitly asks:

Produce:

# INTEGRATION DRAFT

This should contain the complete updated rule set inline.

The integration draft must:

- preserve existing rules
- include approved new rules
- apply approved refinements
- preserve conflicts as unresolved if not decided
- remove nothing silently
- maintain numbering/traceability
- identify changes clearly

Do not integrate rejected or unresolved ideas.

---

# 27. HUMAN REVIEW PROTOCOL

The human reviewer is the final architectural authority.

Possible reviewer decisions:

- APPROVE
- MODIFY
- REJECT
- HOLD
- MERGE

Do not interpret silence as approval.

Do not automatically merge a source into the canonical rules.

---

# 28. HARD RED LINES 🚫

Never promote or recommend as default architecture:

- one agent per vulnerability
- one agent per skill
- uncontrolled agent swarms
- giant system prompts as permanent state
- loading all skills at startup
- loading all knowledge at startup
- blind payload blasting
- autonomous forever-hunting
- "model says confirmed" as proof
- invented graph/scoring systems with undefined purpose
- unsupported implementation claims
- silent conflict resolution
- destruction of historical evidence
- weakening of explicit scope boundaries

If a source promotes one of these:

classify it under:

REJECTED IDEAS

with the reason.

---

# 29. STYLE

Be analytical.

Be explicit.

Be traceable.

Do not exaggerate.

Do not use marketing language.

Do not produce filler.

Do not force a rule out of every paragraph.

If the source contributes only one meaningful architectural rule,
say that clearly.

A small accurate batch is better than a large invented batch.

---

# 30. START CONDITION

When a new source is provided:

1. identify it
2. read it completely
3. extract source-supported rules
4. classify them
5. compare them to existing Buggy rules
6. produce the complete batch output

Start every response with:

BATCH [N] — [SOURCE NAME] — [NEW: X | CONFIRMS: Y | REFINES: Z]
```

### طريقة استخدامهم

```text
MASTER BLUEPRINT
        ↓
يمثل Buggy نفسه والقرارات النهائية
        ↓
RULES PRODUCER
        ↓
يقرأ المصادر
        ↓
NEW / CONFIRMS / REFINES / CONFLICTS
        ↓
أنت تراجع
        ↓
Approved Rules
        ↓
Architecture Contracts
        ↓
Actual Buggy Files
```

وبكده **الـ Blueprint مش بيتغير كل ما شوفنا فيديو**، والـ Rules Producer ما يبقاش له سلطة يقرر architecture لوحده. هو مصنع استخراج، وإنت المراجع، والملفات داخل `buggy/architecture/` هي اللي تتحول بعد اعتمادك إلى القانون الفعلي.
