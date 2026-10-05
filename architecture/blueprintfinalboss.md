معاك حق، وأنا كنت بتعامل مع الـ **decision sheet** كأننا خلصنا تحديث الـ Blueprint نفسه، وده مش صحيح. إحنا كان عندنا **Final Canonical Blueprint**، وبعده ظهرت إضافات جديدة من مقارنة `Agentic-Bug-Hunter`، لكن ما عملناش **نسخة Blueprint نهائية مدموجة** بكل الإضافات.

خلينا نقفل الموضوع صح دلوقتي.

# 🧠 Buggy — Final Canonical Blueprint v3

دي النسخة اللي تعتمدها من هنا.  
**الـ architecture الأساسي ثابت**، والإضافات الجديدة اتدمجت داخله.

```text
buggy/
│
├── README.md
│   # Project overview, purpose, high-level workflow.
│   # No detailed methodology.
│
├── AGENTS.md
│   # Constitution / persistent steering.
│   # Scope, workflow, state, evidence, validation,
│   # human-in-the-loop, safety, non-destructive defaults.
│
├── prioritization.md
│   # Hunting prioritization:
│   # impact / likelihood / attack surface /
│   # confidence / effort / business context.
│   # Severity is never inferred from vulnerability type alone.
│
├── opencode.json
│   # OpenCode runtime configuration.
│   # MCP, permissions, models/defaults, agent configuration.
│
├── package.json
│   # Runtime dependencies where needed.
│
│
├── .opencode/
│   │
│   ├── skills/
│   │   │
│   │   ├── scope-bootstrap/
│   │   │   └── SKILL.md
│   │   │      # Initialize target:
│   │   │      # scope → identities → state → coverage → gaps
│   │   │
│   │   ├── program-research/
│   │   │   └── SKILL.md
│   │   │      # Research Mode:
│   │   │      # program intelligence → hypotheses
│   │   │
│   │   ├── recon/
│   │   │   └── SKILL.md
│   │   │      # Assets / DNS / HTTP / crawling /
│   │   │      # ports / technologies
│   │   │
│   │   ├── js-discovery/
│   │   │   └── SKILL.md
│   │   │      # Discover/download JS only.
│   │   │
│   │   ├── js-analyze/
│   │   │   └── SKILL.md
│   │   │      # sources / sinks / endpoints /
│   │   │      # auth logic / feature flags / flows
│   │   │
│   │   ├── identities/
│   │   │   └── SKILL.md
│   │   │      # Identity/session ledger:
│   │   │      # accounts ↔ requests ↔ authorization context
│   │   │
│   │   ├── session-search/
│   │   │   └── SKILL.md
│   │   │      # Historical/session search.
│   │   │
│   │   ├── validator/
│   │   │   └── SKILL.md
│   │   │      # Proof Gate + 7-question validation gate +
│   │   │      # FP analysis + evidence + skeptical validation
│   │   │
│   │   ├── report-writer/
│   │   │   └── SKILL.md
│   │   │      # Validated finding → structured report
│   │   │
│   │   ├── regression/
│   │   │   └── SKILL.md
│   │   │      # Known fix → retest → bypass → regression
│   │   │
│   │   ├── xss/
│   │   │   └── SKILL.md
│   │   │      # Discovery → context → probes →
│   │   │      # browser proof → evidence
│   │   │
│   │   └── access-control/
│   │       └── SKILL.md
│   │          # IDOR / BAC / ownership /
│   │          # horizontal / vertical / cross-account
│   │
│   ├── agents/
│   │   # Intentionally sparse.
│   │   # Few agents only when isolated context is justified.
│   │   # No agent-per-vulnerability.
│   │
│   └── commands/
│       ├── hunt.md
│       │   # Focused hunting loop
│       │
│       ├── recon.md
│       │   # Recon workflow
│       │
│       ├── monitor.md
│       │   # Monitoring cycle
│       │
│       ├── update.md
│       │   # State / briefing / memory refresh
│       │
│       ├── validate.md
│       │   # Candidate/finding validation
│       │
│       ├── report.md
│       │   # Confirmed finding → report
│       │
│       ├── research.md
│       │   # Program Research Mode
│       │
│       ├── understand.md
│       │   # Application understanding
│       │
│       ├── compare-runs.md
│       │   # Run comparison + miss analysis
│       │
│       ├── mindmap.md
│       │   # Hosts / features / endpoints / relationships
│       │
│       ├── scope-check.md
│       │   # Fast in-scope / out-of-scope /
│       │   # third-party / unknown decision
│       │
│       ├── triage.md
│       │   # Fast go/no-go candidate triage
│       │
│       └── pickup.md
│           # Resume previous work:
│           # untested → partial → unresolved
│
│
├── automation/
│   │
│   ├── README.md
│   │   # Monitor → snapshot → diff → notify.
│   │   # Not autonomous forever-hunting.
│   │
│   ├── monitor.sh
│   │   # Scheduler entrypoint
│   │
│   ├── snapshot.py
│   │   # Asset state:
│   │   # status / size / title / technology / metadata
│   │
│   ├── diff.py
│   │   # New asset / changed content /
│   │   # dead→alive / new URLs
│   │
│   ├── notify.py
│   │   # Notification layer
│   │
│   └── state/
│       └── <root-domain>/
│           ├── subdomains-prev.json
│           └── urls-prev.json
│
│
├── knowledge/
│   # External knowledge / source material.
│   # Every source retains provenance / URL / metadata.
│   │
│   ├── writeups/
│   ├── reports/
│   ├── blogs/
│   ├── tips-tricks/
│   ├── research/
│   ├── docs/
│   │
│   └── processed/
│       # Structured extraction:
│       # patterns / cases / techniques /
│       # prerequisites / validation / impact /
│       # false positives / provenance
│
│
├── concepts/
│   # Synthesized knowledge.
│   # Derived from multiple sources / validated understanding.
│   │
│   ├── methodology/
│   ├── heuristics/
│   │
│   ├── vulnerability-classes/
│   │   ├── idor/
│   │   │   ├── methodology.md
│   │   │   ├── patterns.md
│   │   │   ├── scenarios.md
│   │   │   ├── edge-cases.md
│   │   │   ├── false-positives.md
│   │   │   ├── validation.md
│   │   │   └── sources.md
│   │   │
│   │   ├── xss/
│   │   │   ├── methodology.md
│   │   │   ├── patterns.md
│   │   │   ├── scenarios.md
│   │   │   ├── edge-cases.md
│   │   │   ├── false-positives.md
│   │   │   ├── validation.md
│   │   │   └── sources.md
│   │   │
│   │   └── access-control/
│   │       ├── methodology.md
│   │       ├── patterns.md
│   │       ├── scenarios.md
│   │       ├── edge-cases.md
│   │       ├── false-positives.md
│   │       ├── validation.md
│   │       └── sources.md
│   │
│   ├── attack-patterns/
│   ├── false-positives/
│   └── prioritization/
│
│
├── memory/
│   # Lessons learned from OUR real hunts.
│   │
│   ├── FALSE_POSITIVE_DB.md
│   │   # Actual false-positive patterns
│   │
│   ├── patterns.md
│   │   # Patterns validated through experience
│   │
│   ├── methodology.md
│   │   # Process improvements
│   │
│   ├── report-lessons.md
│   │   # Accepted / rejected / weak reports
│   │
│   ├── CONTEXT_LEDGER.md
│   │   # Persistent system/hunting context
│   │
│   └── lessons/
│       # Reusable learned lessons
│
│
├── learning_engine/
│   │
│   ├── engine.py
│   │   # Learning orchestration
│   │
│   ├── core/
│   │   ├── config.py
│   │   ├── schema.py
│   │   ├── validation.py
│   │   └── storage.py
│   │
│   ├── ingestion/
│   │   ├── finding.py
│   │   └── triage.py
│   │
│   ├── analytics/
│   │   └── patterns.py
│   │
│   ├── run_comparison/
│   │   ├── compare.py
│   │   └── miss_analysis.py
│   │
│   ├── retrieval/
│   │   └── README.md
│   │      # References root retrieval layer.
│   │
│   ├── research/
│   │   ├── graph/
│   │   │   └── TODO.md
│   │   │      # Knowledge graph research only.
│   │   │
│   │   └── scoring/
│   │       └── TODO.md
│   │          # Future scoring research.
│   │
│   ├── scripts/
│   │   └── audit_learning_engine.py
│   │
│   └── memory/
│       └── learning.db
│
│
├── retrieval/
│   # Single source of truth for knowledge retrieval.
│   │
│   ├── search/
│   ├── metadata/
│   └── index/
│
│
├── runtime/
│   │
│   ├── registry.json
│   │   # Skills / commands / agents /
│   │   # detectors / execution routes
│   │
│   ├── tool-manifest.md
│   │   # Tool:
│   │   # purpose / inputs / outputs / limits /
│   │   # correct use / forbidden use /
│   │   # optional vs mandatory
│   │   #
│   │   # Gated Tools:
│   │   # optional missing → skip + record + continue
│   │   # mandatory missing → blocked/precondition
│   │
│   ├── model-routing.md
│   │   # Single source of truth for model routing
│   │
│   ├── redaction.md
│   │   # Local redaction → external model →
│   │   # local restoration
│   │
│   └── opencode/
│       ├── loader.md
│       ├── ORCHESTRATION.md
│       └── manifest.md
│
│
├── scripts/
│   │
│   ├── update.sh
│   │   # state / indexes / briefing refresh
│   │   # + memory maintenance / GC
│   │
│   ├── runtime_preflight.py
│   │   # Scope + tools + MCP + workspace checks
│   │
│   ├── skill_registry.py
│   │   # Scenario → skills / routing
│   │
│   ├── fetch/
│   ├── process/
│   ├── index/
│   ├── learn/
│   │
│   ├── test_skill_registry.py
│   └── test_runtime_contract.py
│
│
├── templates/
│   │
│   ├── ledger.md
│   │   # Generic reusable ledger template
│   │
│   ├── notes-funnel.md
│   │   # NOTES → LEADS → PRIMITIVES →
│   │   # FINDINGS → REPORTS
│   │
│   ├── coverage-map.md
│   │   # Endpoint × Param × Vuln Class × Auth Context
│   │
│   ├── target-config.md
│   ├── hypothesis.md
│   ├── finding.md
│   ├── run-report.md
│   │   # tried / worked / failed / skipped
│   │
│   └── prompts/
│       └── vulnerability-skill-template.md
│           # Canonical schema for vulnerability Skills
│
│
├── testing/
│   └── <root-domain>/
│       │
│       ├── scope.md
│       │   # Human-defined scope lock
│       │
│       ├── agent-briefing.md
│       │   # Current known state / stopping point
│       │
│       ├── target-config.md
│       │
│       ├── ledger.md
│       │   # Canonical target ledger
│       │
│       ├── notes-funnel.md
│       ├── coverage-map.md
│       │
│       ├── identities/
│       │   ├── anonymous.md
│       │   ├── user-a.md
│       │   ├── user-b.md
│       │   └── admin.md
│       │
│       ├── research/
│       │   ├── reports/
│       │   ├── writeups/
│       │   ├── technologies.md
│       │   ├── subdomains.md
│       │   └── business.md
│       │
│       ├── recon/
│       ├── app-map/
│       ├── endpoints/
│       ├── urls/
│       ├── subdomains/
│       │
│       ├── runs/
│       │   ├── run-001/
│       │   │   ├── run-report.md
│       │   │   └── coverage-snapshot.md
│       │   ├── run-002/
│       │   ├── run-003/
│       │   └── comparison.md
│       │
│       ├── oob/
│       │   ├── callbacks/
│       │   └── correlation.md
│       │
│       ├── regression/
│       │   ├── registry.md
│       │   ├── schedules.md
│       │   └── history/
│       │
│       ├── requests/
│       ├── responses/
│       ├── screenshots/
│       ├── scripts/
│       ├── scans/
│       ├── wordlists/
│       ├── hypotheses/
│       ├── evidence/
│       │
│       └── findings/
│           └── <finding-id>/
│               ├── candidate.md
│               ├── validation-report.md
│               ├── finding.md
│               ├── finding.json
│               ├── poc/
│               └── regression.md
│
│
├── wordlists/
│
├── logs/
│
├── architecture/
│   │
│   ├── agent-architecture-rules.md
│   │   # Model / Agent / Skill / Command /
│   │   # Coordinator / Solver / Detector boundaries
│   │
│   ├── file-contracts.md
│   │   # Ownership / lifecycle / allowed contents
│   │
│   ├── knowledge-flow.md
│   │   # Source → Extraction → Concepts →
│   │   # Retrieval → Skills
│   │
│   ├── learning-flow.md
│   │   # Finding / FP / Miss / Run →
│   │   # Lesson → Memory → Methodology improvement
│   │
│   ├── validation-model.md
│   │   # Candidate → Proof → 7-question gate →
│   │   # Validator → Human
│   │   #
│   │   # 7 questions:
│   │   # 1. Scope
│   │   # 2. Reproducibility
│   │   # 3. Authorization Context
│   │   # 4. Security Boundary
│   │   # 5. Evidence
│   │   # 6. Alternative Explanation
│   │   # 7. Impact
│   │
│   ├── model-routing.md
│   │   # Reference only.
│   │   # Canonical routing lives in runtime/model-routing.md
│   │
│   └── agent-security.md
│       # Prompt injection /
│       # indirect injection /
│       # tool poisoning /
│       # MCP trust /
│       # response attacks /
│       # secret exposure
│
│
└── .github/
    └── workflows/
```

# الحاجات الجديدة اللي اتثبتت في النسخة دي

### 1. Commands جديدة

```text
scope-check
triage
pickup
```

دي بقت **جزء رسمي من architecture**، مش اقتراح جانبي.

### 2. Validation Gate

الـ `7-question gate` بقى جزء رسمي من:

```text
architecture/validation-model.md
```

وليس مجرد فكرة داخل الـ Validator.

### 3. Gated Tools

بقى جزء رسمي من:

```text
runtime/tool-manifest.md
```

القاعدة:

```text
Optional tool missing
→ skip
→ record
→ continue

Mandatory tool missing
→ blocked
→ record
```

### 4. Memory GC

مش subsystem جديد.

يعيش داخل:

```text
scripts/update.sh
```

والـ policy تتكتب في:

```text
memory/
```

عند الحاجة.

### 5. Vulnerability Skill Template

أضفناه رسميًا هنا:

```text
templates/prompts/vulnerability-skill-template.md
```

وده مهم جدًا عشان `access-control` و`xss` وأي Skills مستقبلية تتبني من **نفس schema**، بدل كل Skill تخترع شكلها الخاص.

### 6. Detector

**لم نضف له folder مستقل.**

الـ Detector مفهوم runtime capability:

```text
Skill
↓
Detector
↓
Runtime
↓
Evidence
```

وممكن implementation بتاعه يعيش في `scripts/` أو ضمن target-specific scripts حسب نوعه.

### 7. Coordinator / Solver

مفيش `coordinator-agent.md` أو `solver-agent.md` مفروضين في الـ tree.

الـ Coordinator = **orchestration role**.

والـ Solver = **isolated/ephemeral execution context لما يكون مبرر**.

وده يحافظ على مبدأ:

```text
Few agents, many skills
```

---

# 🔒 حاجات اتقفلت نهائيًا

```text
Project name        = Buggy
GitHub              = bugMO77/buggy
Local               = ~/buggy

Target               = Web + HTTP APIs
Primary proxy        = Burp + Burp MCP
Browser              = Playwright/Chromium
OOB                  = interactsh

Initial vuln skills  = Access Control + XSS

Research Mode        = Core workflow
Human review         = Mandatory
LLM confidence       ≠ proof

Knowledge:
writeups
reports
blogs
tips-tricks
research
docs
processed

NO:
posts
podcasts
videos
```

والـ three ledgers تفضل منفصلة:

```text
templates/ledger.md
testing/<target>/ledger.md
memory/CONTEXT_LEDGER.md
```

وده متسق مع الفصل الموجود أصلًا في الـ blueprint بين target state وmemory. نص واحد ملصق&#x20; نص واحد ملصق&#x20;

## والأهم جدًا

**الـ tree اللي عندك حاليًا على Kali قريب جدًا من النسخة دي، لكن فيه ملف جديد واحد رسمي دلوقتي مش موجود عندك:**

```text
templates/prompts/vulnerability-skill-template.md
```

وباقي الإضافات الجديدة (`scope-check`, `triage`, `pickup`) أنت بالفعل أنشأتها في الـ skeleton.

فـ **ما تغيرش حاجة دلوقتي**. خليك على الشجرة الحالية، ونضيف الملف الجديد فقط لما نبدأ مرحلة الـ templates.

دي من هنا **النسخة canonical اللي نبني عليها الـ Intake والـ Master Builder**.
