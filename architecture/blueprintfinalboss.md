تمام. دي **النسخة النهائية الـ Canonical Blueprint** بعد دمج مراجعة الـ v2، وإضافة كل الـ 5 الناقصين، وتصحيح التكرارات، وإبقاء الحاجات غير المحددة كـ research بدل ما نخترع لها implementation. اعتمد دي كـ **source of truth** للتنفيذ. الملف المرفوع نفسه يثبت معظم الهيكل والتغييرات الأساسية. 

# 🧠 Godbug / Hunting System — Final Canonical Blueprint

```text
hunting/
│
├── README.md
│   # تعريف المشروع، الهدف، طريقة التشغيل العامة.
│   # لا يحتوي methodology تفصيلية أو target-specific state.
│
├── AGENTS.md
│   # Constitution:
│   # scope، workflow، state، evidence، validation،
│   # human-in-the-loop، boundaries، non-destructive defaults.
│   # قصير ومركز، وليس encyclopedia.
│
├── prioritization.md
│   # impact / likelihood / attack surface / confidence / effort.
│   # لا يتم استنتاج severity من vulnerability type فقط.
│
├── opencode.json
│   # OpenCode runtime configuration:
│   # MCP servers، permissions، agents، models/defaults.
│
├── package.json
│   # Runtime dependencies عند الحاجة.
│
│
├── .opencode/
│   │
│   ├── skills/
│   │   │
│   │   ├── scope-bootstrap/
│   │   │   └── SKILL.md
│   │   │      # target initialization:
│   │   │      # scope → identities → state → coverage → gaps
│   │   │
│   │   ├── program-research/
│   │   │   └── SKILL.md
│   │   │      # Research Mode:
│   │   │      # reports / writeups / technologies /
│   │   │      # business / program intelligence → hypotheses
│   │   │
│   │   ├── recon/
│   │   │   └── SKILL.md
│   │   │      # asset discovery، DNS، HTTP، crawling،
│   │   │      # ports، technology discovery
│   │   │
│   │   ├── js-discovery/
│   │   │   └── SKILL.md
│   │   │      # JS discovery / download فقط
│   │   │      # micro-skill
│   │   │
│   │   ├── js-analyze/
│   │   │   └── SKILL.md
│   │   │      # sources / sinks / endpoints / secrets /
│   │   │      # auth logic / interesting flows
│   │   │
│   │   ├── identities/
│   │   │   └── SKILL.md
│   │   │      # identity/session ledger:
│   │   │      # accounts ↔ requests ↔ authorization context
│   │   │
│   │   ├── session-search/
│   │   │   └── SKILL.md
│   │   │      # grep/rg على session logs والـ historical context
│   │   │      # "اتكلمنا قبل كده عن X فين؟"
│   │   │
│   │   ├── validator/
│   │   │   └── SKILL.md
│   │   │      # Proof Gate + FP Gate + Evidence +
│   │   │      # skeptical validation + chaining validation
│   │   │
│   │   ├── report-writer/
│   │   │   └── SKILL.md
│   │   │      # validated finding → structured report
│   │   │
│   │   ├── regression/
│   │   │   └── SKILL.md
│   │   │      # known fix → bypass attempts → periodic checks
│   │   │
│   │   ├── xss/
│   │   │   └── SKILL.md
│   │   │      # discovery → context → probes →
│   │   │      # browser proof → evidence
│   │   │
│   │   └── access-control/
│   │       └── SKILL.md
│   │          # IDOR / BAC / auth boundaries /
│   │          # cross-account / ownership
│   │
│   ├── agents/
│   │   # Project-specific OpenCode agents.
│   │   # يبدأ فارغًا.
│   │   # نضيف Solver / Validator / Coordinator عند الحاجة الفعلية.
│   │   # التصميم يستوعب عددًا قليلًا من المتخصصين، وليس agent لكل vuln.
│   │
│   └── commands/
│       ├── hunt.md
│       │   # focused hunting loop
│       ├── recon.md
│       │   # recon workflow
│       ├── monitor.md
│       │   # monitoring cycle
│       ├── update.md
│       │   # state / briefing / lessons refresh
│       ├── validate.md
│       │   # candidate/finding validation
│       ├── report.md
│       │   # confirmed finding → report
│       ├── research.md
│       │   # program Research Mode
│       ├── understand.md
│       │   # application understanding
│       ├── compare-runs.md
│       │   # run comparison + miss analysis
│       └── mindmap.md
│           # hosts / features / endpoints / trust relationships
│
│
├── automation/
│   # Continuous recon/change monitoring.
│   # ليس autonomous hunting.
│   │
│   ├── README.md
│   │   # فلسفة التشغيل:
│   │   # monitor → diff → notify
│   │   # auto-scan لكل asset جديد ليس default.
│   │
│   ├── monitor.sh
│   │   # scheduler/cron entrypoint
│   │
│   ├── snapshot.py
│   │   # subdomain/asset snapshot:
│   │   # status / size / title / technology / metadata
│   │
│   ├── diff.py
│   │   # previous state vs current state:
│   │   # new asset / changed content / dead→alive / new URLs
│   │
│   ├── notify.py
│   │   # local / Telegram / other notification layer
│   │
│   └── state/
│       └── <root-domain>/
│           ├── subdomains-prev.json
│           └── urls-prev.json
│
│
├── knowledge/
│   # External knowledge.
│   # كل مصدر يحتفظ بـ provenance / source URL / metadata.
│   │
│   ├── writeups/
│   ├── reports/
│   ├── blogs/
│   ├── posts/
│   ├── tips-tricks/
│   ├── research/
│   ├── docs/
│   ├── podcasts/
│   ├── videos/
│   │
│   └── processed/
│       # Structured extraction:
│       # patterns / cases / techniques / prerequisites /
│       # validation / impact / FP / provenance
│
│
├── concepts/
│   # Synthesized knowledge بعد دمج مصادر متعددة.
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
│   │   ├── xss/
│   │   └── access-control/
│   │
│   ├── attack-patterns/
│   │   # cross-vulnerability patterns
│   │
│   ├── false-positives/
│   │   # known misleading signals / non-issues
│   │
│   └── prioritization/
│       # prioritization heuristics
│
│
├── memory/
│   # What OUR actual hunts taught the system.
│   # ليست external knowledge.
│   │
│   ├── FALSE_POSITIVE_DB.md
│   │   # false positives from real hunting
│   │
│   ├── patterns.md
│   │   # patterns validated by our own experiments
│   │
│   ├── methodology.md
│   │   # operational lessons / process improvements
│   │
│   ├── report-lessons.md
│   │   # accepted / rejected / weak report lessons
│   │
│   ├── CONTEXT_LEDGER.md
│   │   # persistent decisions + important context
│   │
│   └── lessons/
│       # reusable lessons extracted from actual work
│       ├── idor.md
│       ├── xss.md
│       └── ...
│
│
├── learning_engine/
│   # Convert source material + actual hunt outcomes
│   # into structured learning signals.
│   │
│   ├── engine.py
│   │   # public orchestration/API
│   │
│   ├── core/
│   │   ├── config.py
│   │   ├── schema.py
│   │   │   # sources / cases / findings / lessons schemas
│   │   ├── validation.py
│   │   │   # structural + quality validation
│   │   └── storage.py
│   │
│   ├── ingestion/
│   │   ├── finding.py
│   │   │   # findings → learning signals
│   │   └── triage.py
│   │       # decide what is worth learning from
│   │
│   ├── analytics/
│   │   └── patterns.py
│   │       # extract patterns from hunting history
│   │
│   ├── run_comparison/
│   │   ├── compare.py
│   │   │   # Run1 vs Run2 vs Run3
│   │   │   # coverage diff + finding diff
│   │   └── miss_analysis.py
│   │       # why did the agent miss it?
│   │       # → methodology improvement
│   │
│   ├── retrieval/
│   │   └── README.md
│   │       # points to ROOT retrieval system.
│   │       # no second retrieval backend.
│   │
│   ├── research/
│   │   │
│   │   ├── graph/
│   │   │   └── TODO.md
│   │   │      # Knowledge Graph = open research area.
│   │   │      # No implementation assumed.
│   │   │
│   │   └── scoring/
│   │       └── TODO.md
│   │          # Future scoring/learning signals research.
│   │          # Not core infrastructure yet.
│   │
│   ├── scripts/
│   │   └── audit_learning_engine.py
│   │
│   └── memory/
│       └── learning.db
│
│
├── retrieval/
│   # SINGLE source of truth for knowledge retrieval.
│   │
│   ├── search/
│   │   # keyword / metadata / future semantic retrieval
│   │
│   ├── metadata/
│   │   # tags / provenance / quality / confidence
│   │
│   └── index/
│       # indexes
│
│
├── runtime/
│   │
│   ├── registry.json
│   │   # mapping:
│   │   # skills / commands / agents / aliases / execution routes
│   │
│   ├── tool-manifest.md
│   │   # لكل tool:
│   │   # purpose / inputs / outputs / limits / correct usage
│   │   # + forbidden or invalid usage patterns
│   │
│   ├── model-routing.md
│   │   # SINGLE source of truth for model selection/routing
│   │
│   ├── redaction.md
│   │   # local redaction → external model → local restoration
│   │
│   └── opencode/
│       ├── loader.md
│       │   # workspace/bootstrap loading rules
│       │
│       ├── ORCHESTRATION.md
│       │   # coordinator → objective → execution
│       │
│       └── manifest.md
│           # OpenCode capabilities
│
│
├── scripts/
│   │
│   ├── update.sh
│   │   # state / briefing / index refresh
│   │
│   ├── runtime_preflight.py
│   │   # scope + tools + MCP + workspace readiness
│   │   # no execution when scope/preflight fails
│   │
│   ├── skill_registry.py
│   │   # scenario → candidate skills / routing
│   │
│   ├── fetch/
│   │   # source fetching
│   │
│   ├── process/
│   │   # normalization + extraction
│   │
│   ├── index/
│   │   # index build/update
│   │
│   ├── learn/
│   │   # learning pipeline execution
│   │
│   ├── test_skill_registry.py
│   └── test_runtime_contract.py
│
│
├── templates/
│   │
│   ├── ledger.md
│   ├── notes-funnel.md
│   │   # Notes → Leads → Hypotheses → Findings
│   ├── coverage-map.md
│   │   # Endpoint × Parameter × Vuln Class × Auth Context
│   ├── target-config.md
│   ├── hypothesis.md
│   ├── finding.md
│   ├── run-report.md
│   │   # what was tried / worked / failed / skipped
│   └── prompts/
│       # Prompt library
│
│
├── testing/
│   └── <root-domain>/
│       │
│       ├── scope.md
│       │   # Human-defined scope lock
│       │
│       ├── agent-briefing.md
│       │   # what we know / where we stopped
│       │
│       ├── target-config.md
│       │   # technology / accounts / environment
│       │
│       ├── ledger.md
│       │   # canonical target ledger
│       │
│       ├── notes-funnel.md
│       │
│       ├── coverage-map.md
│       │   # endpoint × param × vuln class × auth context
│       │
│       ├── identities/
│       │   ├── anonymous.md
│       │   ├── user-a.md
│       │   ├── user-b.md
│       │   └── admin.md
│       │   # identity / privileges / auth path / relevant sessions
│       │
│       ├── research/
│       │   # Program Knowledge Pack
│       │   ├── reports/
│       │   ├── writeups/
│       │   ├── technologies.md
│       │   ├── subdomains.md
│       │   └── business.md
│       │
│       ├── recon/
│       │   # assets / DNS / HTTP / ports / snapshots
│       │
│       ├── app-map/
│       │   # features / endpoints / JS / Swagger /
│       │   # workflows / trust boundaries
│       │
│       ├── endpoints/
│       │   # Host + Scheme + Path + Method +
│       │   # Params + Auth + Source + Status
│       │
│       ├── urls/
│       ├── subdomains/
│       │
│       ├── runs/
│       │   # Non-determinism home
│       │   │
│       │   ├── run-001/
│       │   │   ├── run-report.md
│       │   │   └── coverage-snapshot.md
│       │   │
│       │   ├── run-002/
│       │   │   └── ...
│       │   │
│       │   ├── run-003/
│       │   │   └── ...
│       │   │
│       │   └── comparison.md
│       │       # coverage differences + findings + misses
│       │
│       ├── oob/
│       │   # async callback infrastructure
│       │   │
│       │   ├── callbacks/
│       │   │   # callback artifacts with correlation identifiers
│       │   │
│       │   └── correlation.md
│       │       # callback ↔ request ↔ payload ↔ iteration/solver
│       │
│       ├── regression/
│       │   # target-level regression lifecycle
│       │   │
│       │   ├── registry.md
│       │   │   # findings/fixes that need retesting
│       │   │
│       │   ├── schedules.md
│       │   │   # when/what should be checked
│       │   │
│       │   └── history/
│       │       # regression runs + bypass attempts
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
│               │   # initial hypothesis
│               │
│               ├── validation-report.md
│               │   # validation evidence / FP analysis
│               │
│               ├── finding.md
│               │   # confirmed finding
│               │
│               ├── finding.json
│               │   # structured representation
│               │
│               ├── poc/
│               │   # reproducible proof artifacts
│               │
│               └── regression.md
│                   # finding-specific regression history
│
│
├── wordlists/
│   # Global reusable wordlists
│
├── logs/
│   # runtime / session / execution logs
│
│
├── architecture/
│   │
│   ├── agent-architecture-rules.md
│   │   # coordinator / worker / detector boundaries
│   │   # + v6 design rules
│   │
│   ├── file-contracts.md
│   │   # artifact ownership and allowed contents
│   │
│   ├── knowledge-flow.md
│   │   # Source → Extraction → Synthesis → Concepts → Retrieval
│   │
│   ├── learning-flow.md
│   │   # Finding / FP / Miss → Lesson → Memory → Methodology
│   │
│   ├── validation-model.md
│   │   # Candidate → Proof → FP Gate → Validator → Human
│   │
│   ├── model-routing.md
│   │   # reference only:
│   │   # canonical rules live in runtime/model-routing.md
│   │
│   └── agent-security.md
│       # prompt injection / indirect injection /
│       # tool poisoning / MCP trust / response attacks /
│       # secret exposure
│
│
└── .github/
    └── workflows/
        # CI:
        # contracts / schema validation /
        # skill registry / learning engine
```

## 🔗 الـ System Relationships

```text
                         HUMAN
                           │
                           ▼
                    PROGRAM RESEARCH
                           │
                           ▼
                       SCOPE LOCK
                           │
                           ▼
                     TARGET STATE
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
         AUTOMATION                    RECON
         MONITORING                      │
             │                           │
             └─────────────┬─────────────┘
                           ▼
                        APP MAP
                           │
                           ▼
                     COVERAGE MAP
                           │
                           ▼
                      COORDINATOR
                           │
                  focused objective
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
         DETECTOR       GENERATIVE    SPECIALIZED
                           AGENT        WORKFLOW
             │             │             │
             └─────────────┼─────────────┘
                           ▼
                         SKILLS
                           │
                           ▼
                        RUNTIME
                 ┌─────────┼─────────┐
                 ▼         ▼         ▼
                BURP     BROWSER    CLI/OOB
                 │         │         │
                 └─────────┼─────────┘
                           ▼
                      OBSERVATIONS
                           │
                  KNOW / PROVE / SUGGEST
                           │
                           ▼
                       PROOF GATE
                           │
                           ▼
                    FALSE POSITIVE GATE
                           │
                           ▼
                        VALIDATOR
                           │
                           ▼
                          HUMAN
                           │
                  ┌────────┴────────┐
                  ▼                 ▼
               FINDING           REJECTED
                  │
                  ▼
                REPORT
                  │
                  ▼
             REGRESSION
                  │
                  ▼
               LEARNING
                  │
          ┌───────┴────────┐
          ▼                ▼
       MEMORY          CONCEPTS
          │                │
          └───────┬────────┘
                  ▼
              RETRIEVAL
                  │
                  └──────────→ NEXT OBJECTIVE
```

## 🧩 الـ Architectural Separation النهائي

```text
AGENTS.md
    = Constitution

.opencode/
    = OpenCode operational layer

knowledge/
    = External raw knowledge

knowledge/processed/
    = Extracted structured source knowledge

concepts/
    = Synthesized reusable knowledge

memory/
    = Lessons learned from OUR hunting

retrieval/
    = Knowledge retrieval interface

testing/<target>/
    = Persistent target state

automation/
    = Scheduled observation / change detection

runtime/
    = Execution + tools + models

learning_engine/
    = Learning from sources + runs + findings

architecture/
    = Contracts + system rules
```

## 🚫 حاجات مش جزء من الـ Core

```text
❌ Fine-tuning كشرط
❌ Agent لكل vulnerability
❌ تحميل كل الـ skills والـ knowledge مرة واحدة
❌ Autonomous forever hunting
❌ 23 subagents
❌ Raw writeups كـ training data
❌ اعتبار LLM confidence دليلًا
❌ Knowledge graph implementation حاليًا
❌ Scoring engine مفروض من البداية
❌ Auto-scan لكل asset جديد كـ default
❌ VPS/Telegram كـ core requirement
```

## 🎯 القاعدة الذهبية للنظام

```text
AI generates hypotheses.
Runtime generates observations.
Proof produces evidence.
Validator attacks the claim.
Human makes the final judgment.
Memory learns from what actually happened.
```

والـ **single source of truth** للحاجات اللي كانت متكررة محسوم كده:

```text
Model Routing
    → runtime/model-routing.md

Retrieval
    → retrieval/

Knowledge Graph
    → learning_engine/research/graph/TODO.md

Scoring
    → learning_engine/research/scoring/TODO.md

Target State
    → testing/<root-domain>/

Actual Hunt Lessons
    → memory/
```

دي النسخة النهائية التي نعتمدها. والنسخة الأصلية المرفوعة أكدت صراحة إضافة `automation`، `runs`، `oob`، `regression`، وفصل `identities` عن `session-search`، وإزالة الـ graph implementation المبكر، وتوحيد retrieval وmodel-routing. 
