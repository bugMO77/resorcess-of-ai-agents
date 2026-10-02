glm 3
# 📦 Godbug Blueprint v2 — النسخة المكتملة من كل المصادر

مدموج فيها الـ 5 ناقصين + تصحيح التكرارات + تحويل الـ graph لـ TODO. دي **الـ canonical version**:

---

```text
hunting/
│
├── README.md
│   # تعريف المشروع، الهدف، طريقة التشغيل العامة.
│   # لا يحتوي methodology تفصيلية أو target-specific state.
│
├── AGENTS.md
│   # Constitution: scope، workflow، state، evidence، validation،
│   # human-in-the-loop، والحدود العامة.
│   # يظل قصيرًا - steering مش encyclopedia.
│
├── prioritization.md
│   # impact / likelihood / attack surface / confidence / effort.
│   # Golden line: ممنوع severity من vulnerability type فقط.
│
├── opencode.json
│   # MCP servers، permissions، agents، models/defaults.
│
├── package.json
│   # Node deps (Playwright / runtime scripts).
│
│
├── .opencode/
│   │
│   ├── skills/                    # ← canonical skills (OpenCode native)
│   │   │
│   │   ├── scope-bootstrap/
│   │   │   └── SKILL.md           # Target جديد: scope → identities →
│   │   │                          # state → coverage → gaps.
│   │   │
│   │   ├── program-research/
│   │   │   └── SKILL.md           # Research Mode: reports/writeups/
│   │   │                          # technologies/business → hypotheses.
│   │   │
│   │   ├── recon/
│   │   │   └── SKILL.md           # asset discovery، DNS، HTTP،
│   │   │                          # crawling، tech discovery.
│   │   │
│   │   ├── js-discovery/
│   │   │   └── SKILL.md           # اكتشاف وتحميل JS فقط (micro).
│   │   │
│   │   ├── js-analyze/
│   │   │   └── SKILL.md           # تحليل JS: sources/sinks، endpoints،
│   │   │                          # secrets، interesting flows.
│   │   │
│   │   ├── identities/
│   │   │   └── SKILL.md           # 🆕 Identity/Session Ledger:
│   │   │                          # ربط الحسابات بالـ requests والـ
│   │   │                          # authorization context. الـ Agent
│   │   │                          # مبنيخترش cookies/IDs - يرجع هنا.
│   │   │
│   │   ├── session-search/
│   │   │   └── SKILL.md           # 🆕 (أصلي - الشرح الصح):
│   │   │                          # grep/rg على session logs القديمة.
│   │   │                          # "اتكلمنا امبارح عن X فين؟"
│   │   │
│   │   ├── validator/
│   │   │   └── SKILL.md           # Proof Gate + FP Gate + Evidence +
│   │   │                          # skeptical validation + chaining gate.
│   │   │
│   │   ├── report-writer/
│   │   │   └── SKILL.md           # Finding مثبت → structured report.
│   │   │                          # Match هيكل أحسن reports بتاعتك.
│   │   │
│   │   ├── regression/
│   │   │   └── SKILL.md           # 🆕 Regression Bot: known fix →
│   │   │                          # bypass attempts → periodic check.
│   │   │
│   │   ├── xss/
│   │   │   └── SKILL.md           # discovery → context → probes →
│   │   │                          # browser proof → evidence.
│   │   │
│   │   └── access-control/
│   │       └── SKILL.md           # IDOR/BAC/auth boundaries،
│   │                              # cross-account، ownership.
│   │
│   ├── agents/
│   │   # Project-specific OpenCode agents. يبدأ فارغًا.
│   │   # Solver/Validator كـ subagent يتعرف هنا عند الحاجة الفعلية.
│   │   # (2-3 max في الـ long-run مش 23)
│   │
│   └── commands/
│       ├── hunt.md                # focused hunting loop.
│       ├── recon.md               # تشغيل/استكمال recon workflow.
│       ├── monitor.md             # 🆕 تشغيل/فحص monitoring cycle.
│       ├── update.md              # تحديث state/briefing/lessons.
│       ├── validate.md            # validation على candidate/finding.
│       ├── report.md              # report من finding مثبت.
│       ├── research.md            # Research Mode على برنامج.
│       ├── understand.md          # بناء application understanding.
│       ├── compare-runs.md        # 🆕 مقارنة runs و miss analysis.
│       └── mindmap.md             # علاقات hosts/features/endpoints.
│
│
├── automation/                        # 🆕 (كان ناقص بالكامل)
│   │
│   ├── README.md
│   │   # فلسفة v1 المحافظة:
│   │   # monitor → diff → notify فقط.
│   │   # auto-scan لكل asset جديد = v2 بعد ما يثبت الـ workflow.
│   │
│   ├── monitor.sh                     # cron entrypoint: recon cycle كامل
│   ├── snapshot.py                    # subdomain snapshot: (status/size/
│   │                                  # tech/title) لكل live sub
│   ├── diff.py                        # قديم vs جديد:
│   │                                  # - new subdomain?
│   │                                  # - content changed?
│   │                                  # - subdomain رجع للحياة?
│   │                                  # - URLs جديدة (wayback/katana)
│   ├── notify.py                      # إرسال التنبيهات (Telegram/local)
│   │
│   └── state/
│       └── <root-domain>/
│           ├── subdomains-prev.json   # آخر snapshot
│           └── urls-prev.json
│
│
├── knowledge/
│   │   # المعرفة الخارجية. كل مصدر معه provenance/URL.
│   │
│   ├── writeups/
│   ├── reports/                   # disclosed reports لبرامج محددة
│   ├── blogs/
│   ├── posts/
│   ├── tips-tricks/               # heuristics/tricks/commands/edge cases
│   ├── research/
│   ├── docs/                      # documentation → testable constraints
│   ├── podcasts/
│   ├── videos/
│   │
│   └── processed/
│       # Structured extraction من كل المصادر:
│       # patterns / cases / techniques / prerequisites /
│       # validation / impact / FP / provenance
│
│
├── concepts/
│   │   # المعرفة المركبة بعد دمج مصادر متعددة.
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
│   ├── attack-patterns/           # patterns عبر أكثر من vuln class
│   ├── false-positives/
│   └── prioritization/
│
│
├── memory/
│   │   # ما تعلمناه من OUR hunts (مش خارجي).
│   │
│   ├── FALSE_POSITIVE_DB.md       # FP patterns من اختباراتنا الفعلية
│   ├── patterns.md                # attack patterns أثبتتها تجاربنا
│   ├── methodology.md             # تعديلات من النتائج الواقعية
│   ├── report-lessons.md
│   ├── CONTEXT_LEDGER.md          # قرارات وسياق عبر التشغيل
│   │
│   └── lessons/                   # 🆕 (تصحيح: كان "targets" بوصف غلط)
│       # lessons خاصة بـ vulnerability class أو pattern محدد.
│       ├── idor.md
│       ├── xss.md
│       └── ...
│
│
├── learning_engine/
│   │   # تحويل مصادر ونتائج Hunt إلى معرفة قابلة للتقييم والاسترجاع.
│   │   # ⚠️ بناء تدريجي - مش كل مكون لازم يكون فعال من أول يوم.
│   │
│   ├── engine.py                  # public API/orchestration
│   │
│   ├── core/
│   │   ├── config.py
│   │   ├── schema.py              # sources/cases/lessons schemas
│   │   ├── validation.py          # structural + quality validation
│   │   └── storage.py
│   │
│   ├── ingestion/
│   │   ├── finding.py             # findings → learning signals
│   │   └── triage.py              # 🟢 KEV-style: إيه اللي يستحق التعلم؟
│   │                              # (مش كل finding/FP يستحق)
│   │
│   ├── scoring/
│   │   ├── engine.py              # quality / confidence / novelty
│   │   └── signals.json
│   │
│   ├── run_comparison/            # 🆕 (كان مذكور نصياً بلا home)
│   │   ├── compare.py             # Run1 vs Run2 vs Run3:
│   │   │                          # coverage diff + findings diff
│   │   └── miss_analysis.py       # ليه الـ agent فشل/فاته؟
│   │                              # → methodology improvement
│   │
│   ├── research/                  # 🔄 (كان "graph" - محدش حدد تفاصيله)
│   │   └── TODO.md                # Knowledge graph = open research.
│   │                              # العلاقات بين vulns/techs/patterns
│   │                              # فكرة قوية لكن كل المصادر تركتها
│   │                              # غير محددة - لا نبنيها من الخيال.
│   │
│   ├── scripts/
│   │   └── audit_learning_engine.py
│   │
│   └── memory/
│       └── learning.db
│
│
├── retrieval/                         # ← 🔄 مصدر وحيد للحقيقة
│   │   # واجهة استرجاع المعرفة. الـ learning_engine يشاور هنا،
│   │   # مش العكس. فصل "أين وكيف نبحث" عن الـ knowledge.
│   │
│   ├── search/                    # keyword / metadata / semantic
│   ├── metadata/                  # tags/topics/confidence/provenance
│   └── index/
│
│
├── runtime/
│   │
│   ├── registry.json              # mapping: skills/aliases/commands/
│   │                              # agents/execution routes
│   ├── tool-manifest.md           # كل tool: ماذا يفعل/inputs/outputs/
│   │                              # قيود/إمتى يستخدم + **إيه اللي
│   │                              # مينفعش يتخبر بالطريقة دي**
│   │                              # (مثل Burp Collaborator بالـ curl)
│   ├── model-routing.md           # 🔄 (الوحيد - الـ architecture/
│   │                              # version يشاور هنا) قواعد اختيار
│   │                              # model حسب نوع المهمة
│   ├── redaction.md               # LOCAL REDACTION → cloud →
│   │                              # LOCAL RESTORATION rules
│   │
│   └── opencode/
│       ├── loader.md              # bootstrap للـ workspace
│       ├── ORCHESTRATION.md       # coordinator → objective → execution
│       └── manifest.md            # capabilities المتاحة للـ OpenCode
│
│
├── scripts/
│   │
│   ├── update.sh                  # state/briefing/indexes refresh
│   ├── runtime_preflight.py       # Boot gate: scope + tools + MCP +
│   │                              # workspace readiness (مفيش تشغيل
│   │                              # من غير scope جاهز)
│   ├── skill_registry.py          # scenario → candidate skills/routing
│   ├── fetch/                     # 🔄 (مجلدات بوضوح)
│   ├── process/                   # normalize + extract
│   ├── index/                     # بناء/تحديث indexes
│   ├── learn/                     # تشغيل learning pipeline
│   ├── test_skill_registry.py
│   └── test_runtime_contract.py
│
│
├── templates/
│   │
│   ├── ledger.md
│   ├── notes-funnel.md            # Notes → Leads → Hypotheses → Findings
│   ├── coverage-map.md            # Endpoint × Param × Vuln Class ×
│   │                              # Auth Context
│   ├── target-config.md           # scope + identities + technologies
│   ├── hypothesis.md
│   ├── finding.md
│   ├── run-report.md              # 🆕 snapshot لكل run
│   │                              # (what I tried/worked/failed/skipped)
│   │
│   └── prompts/                   # Prompt Library (task framing)
│
│
├── testing/
│   └── <root-domain>/
│       │
│       ├── scope.md               # Human-defined scope lock
│       ├── agent-briefing.md      # "ماذا نعرف الآن؟ أين توقفنا؟"
│       ├── target-config.md       # tech + accounts + environment
│       ├── ledger.md              # canonical target ledger
│       ├── notes-funnel.md
│       ├── coverage-map.md
│       │
│       ├── identities/
│       │   ├── anonymous.md
│       │   ├── user-a.md          # user_id/cookies/tokens + كيف يوصل
│       │   ├── user-b.md          # للـ endpoint (auth مشكلة مستقلة!)
│       │   └── admin.md
│       │
│       ├── research/              # Program Knowledge Pack
│       │   ├── reports/
│       │   ├── writeups/
│       │   ├── technologies.md
│       │   ├── subdomains.md
│       │   └── business.md
│       │
│       ├── recon/                 # assets/DNS/HTTP/ports/snapshots
│       ├── app-map/               # features/endpoints/JS/Swagger/
│       │                          # flows/trust boundaries
│       │                          # (Swagger ≠ كل الـ endpoints!)
│       ├── endpoints/             # endpoint registry:
│       │                          # Host+Scheme+Path+Method+Params+
│       │                          # Auth+Source+Status
│       ├── urls/
│       ├── subdomains/
│       │
│       ├── runs/                  # 🆕 (non-determinism home)
│       │   ├── run-001/
│       │   │   ├── run-report.md  # tried/worked/failed/skipped
│       │   │   └── coverage-snapshot.md
│       │   ├── run-002/
│       │   │   └── ...
│       │   └── comparison.md       # diffs + miss analysis
│       │
│       ├── oob/                   # 🆕 (async events home)
│       │   ├── callbacks/          # كل callback مع identifier (/xxe1)
│       │   └── correlation.md      # ربط الـ callback بالـ request/
│       │                           # solver اللي سببه
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
│               ├── candidate.md        # hypothesis الأولية
│               ├── validation-report.md
│               ├── finding.md
│               ├── finding.json
│               ├── poc/                # reproducible للـ triager
│               └── regression.md       # 🆕 known fix → bypass
│                                          # attempts → دوري
│
│
├── wordlists/
├── logs/
│
├── architecture/
│   │
│   ├── agent-architecture-rules.md    # v6 rules (المصدر الدستوري)
│   ├── file-contracts.md
│   ├── knowledge-flow.md              # Source→extraction→synthesis→
│   │                                   # concepts→retrieval
│   ├── learning-flow.md               # Finding/FP/miss→lesson→memory→
│   │                                   # methodology improvement
│   ├── validation-model.md            # Candidate→Proof→FP→Validator→Human
│   ├── model-routing.md               # 🔄 بيشاور على runtime/ (الوحيد
│   │                                   # في runtime/model-routing.md)
│   └── agent-security.md              # prompt injection / indirect
│                                       # injection / tool poisoning /
│                                       # MCP trust / exfiltration
│
└── .github/
    └── workflows/
        # CI للـ contracts + learning engine + skill registry +
        # schema validation
```

---

# 📋 ملخص التغييرات عن v1:

| # | التغيير | السبب |
|---|---|---|
| 1 | 🆕 `automation/` كامل (monitor/snapshot/diff/notify) | البوست الأول - scheduled recon + change detection |
| 2 | 🆕 `testing/<t>/runs/` + `compare-runs.md` command | non-determinism + miss analysis |
| 3 | 🆕 `testing/<t>/oob/` + correlation | async callbacks من فيديو الـ Coordinator |
| 4 | 🆕 `regression/` skill + `regression.md` في كل finding | من Hermes + micro-agents video |
| 5 | 🆕 `monitor.md` command | تشغيل الـ automation من الـ agent |
| 6 | 🔄 فصل `identities/` عن `session-search/` | كانوا مدموجين غلط - حاجتين مختلفين |
| 7 | 🔄 `learning_engine/graph/` → `research/TODO.md` | محدش حدد تفاصيل الـ graph - ممنوع نبنيه من الخيال |
| 8 | 🔄 `learning_engine/run_comparison/` | كان نص بس - بقى component فعلي |
| 9 | 🔄 `retrieval/` = مصدر وحيد، الـ learning engine يشاور عليه | إزالة التكرار |
| 10 | 🔄 `model-routing` واحد في `runtime/`، والـ `architecture/` يشاور عليه | إزالة التكرار |
| 11 | 🔄 `memory/targets/` → `memory/lessons/` | الاسم كان غلط عن الوصف |
| 12 | 🔄 `scripts/fetch|process|index|learn` = مجلدات واضحة | كانت بلا نوع |
| 13 | ➕ notes في `tool-manifest.md` عن **قيود الأدوات نفسها** | من فيديو الـ AGENTS.md (Collaborator بالـ curl ❌) |
| 14 | ➕ `run-report.md` template | "قارن process مش output بس" |
| 15 | ➕ `automation/README.md` بفلسفة v1 المحافظة | monitor→notify فقط قبل auto-scan |

---

# 🚫 "إيه اللي مش موجود" (محدث):

```text
❌ Fine-tuning required
❌ One agent per vulnerability
❌ Load all skills/knowledge at startup
❌ Autonomous forever hunting كافتراض
❌ 23 subagents
❌ Raw writeups كـ training data
❌ Confirmation على LLM confidence
❌ Knowledge graph مبني قبل ما يتعرف (TODO لحد ما)
❌ Auto-scan لكل asset جديد (v1 = notify فقط)
❌ VPS/Telegram infrastructure كـ core requirement (اختياري لاحقاً)
```

---

# 🏁 الخطوة الجاية:

الـ Blueprint v2 ده **كامل ومطابق لكل الـ 12 مصدر**. من هنا الترتيب المنطقي للبناء:

**Phase 1 (اليوم):** `AGENTS.md` + `scope-bootstrap` + الـ templates
**Phase 2:** `program-research` + `validator` + أول vuln skill
**Phase 3:** `automation/` v1 + `runs/` workflow
**Phase 4:** learning engine (تدريجي)

**نبدأ Phase 1 دلوقتي وأطلعلك `AGENTS.md
` الفعلي + أول skill مكتوبة بالكامل؟*


        chat gpt 

        أيوه، المراجعة دي في محلها جدًا. بعد دمج الـ 5 مكونات وتصحيح التكرارات، دي تكون **الـ canonical Blueprint** بدل ما نسيب أجزاء مهمة معلقة في "بعدها نشوف". البشر اخترعوا Git عشان يمنعوا الفوضى دي، ثم اخترعوا 17 نسخة من نفس الـ architecture. خلينا نمنع المصيبة من البداية.

# Hunting System — Canonical Blueprint v2

```text
hunting/
│
├── README.md
│   # System overview, philosophy, usage, lifecycle
│
├── AGENTS.md
│   # Constitution:
│   # scope rules, safety, human-in-loop, proof requirements,
│   # context rules, agent behavior, non-destructive defaults
│
├── prioritization.md
│   # Global prioritization logic and impact signals
│
├── opencode.json
│   # OpenCode runtime configuration only
│
├── package.json
│   # Project/runtime dependencies where needed
│
│
├── .opencode/
│   │
│   ├── skills/
│   │   │
│   │   ├── scope-bootstrap/
│   │   │   └── SKILL.md
│   │   │      # Initialize target scope and enforce scope boundaries
│   │   │
│   │   ├── program-research/
│   │   │   └── SKILL.md
│   │   │      # Research bug bounty program, technologies,
│   │   │      # previous disclosures, attack surface and constraints
│   │   │
│   │   ├── recon/
│   │   │   └── SKILL.md
│   │   │      # Recon methodology and deterministic recon workflows
│   │   │
│   │   ├── js-discovery/
│   │   │   └── SKILL.md
│   │   │      # Discover JS assets, sources, endpoints, references
│   │   │
│   │   ├── js-analyze/
│   │   │   └── SKILL.md
│   │   │      # Analyze JS for endpoints, auth logic, sinks,
│   │   │      # feature flags, hidden functionality
│   │   │
│   │   ├── session-search/
│   │   │   └── SKILL.md
│   │   │      # Search previous hunting/session logs and historical notes
│   │   │      # NOT identity management
│   │   │
│   │   ├── identities/
│   │   │   └── SKILL.md
│   │   │      # Work with identity/session matrix:
│   │   │      # user-a, user-b, admin, unauthenticated, etc.
│   │   │
│   │   ├── validator/
│   │   │   └── SKILL.md
│   │   │      # Skeptical validation, evidence requirements,
│   │   │      # false-positive elimination
│   │   │
│   │   ├── report-writer/
│   │   │   └── SKILL.md
│   │   │      # Convert validated findings into reports
│   │   │
│   │   ├── regression/
│   │   │   └── SKILL.md
│   │   │      # Re-test known findings/fixes and search for bypasses
│   │   │
│   │   ├── xss/
│   │   │   └── SKILL.md
│   │   │      # XSS methodology
│   │   │
│   │   └── access-control/
│   │       └── SKILL.md
│   │          # IDOR / privilege / authorization methodology
│   │
│   ├── agents/
│   │   ├── coordinator.md
│   │   │   # Selects next objective and delegates focused work
│   │   │
│   │   ├── detector.md
│   │   │   # Specialized/narrow detector worker
│   │   │
│   │   ├── validator.md
│   │   │   # Independent skeptical validation
│   │   │
│   │   └── reporter.md
│   │       # Reporting worker
│   │
│   └── commands/
│       ├── hunt.md
│       │   # Main hunting workflow
│       ├── recon.md
│       │   # Recon workflow
│       ├── research.md
│       │   # Program research workflow
│       ├── understand.md
│       │   # Application understanding / mapping
│       ├── validate.md
│       │   # Explicit validation workflow
│       ├── regression.md
│       │   # Regression testing workflow
│       ├── report.md
│       │   # Reporting workflow
│       ├── update.md
│       │   # Refresh target state / indexes / knowledge
│       └── mindmap.md
│           # Build/refresh application and attack-surface mental map
│
│
├── knowledge/
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
│       # Normalized/extracted source material
│       # after ingestion
│
│
├── concepts/
│   │
│   ├── methodology/
│   │   # Synthesized hunting methodology
│   │
│   ├── heuristics/
│   │   # Reusable reasoning patterns / indicators
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
│   │   └── access-control/
│   │
│   ├── attack-patterns/
│   │   # Cross-vulnerability attack patterns
│   │
│   ├── false-positives/
│   │   # Known non-issues and misleading signals
│   │
│   └── prioritization/
│       # Which signals justify deeper investigation
│
│
├── memory/
│   │
│   ├── FALSE_POSITIVE_DB.md
│   │   # Accumulated false-positive knowledge
│   │
│   ├── patterns.md
│   │   # Patterns learned from actual hunts
│   │
│   ├── methodology.md
│   │   # Lessons about what works / fails operationally
│   │
│   ├── report-lessons.md
│   │   # Lessons extracted from submitted/rejected reports
│   │
│   ├── CONTEXT_LEDGER.md
│   │   # Persistent important context and decisions
│   │
│   └── lessons/
│       # Target-independent lessons learned from actual work
│       # Separate from target-specific state
│
│
├── learning_engine/
│   │
│   ├── engine.py
│   │   # Learning pipeline entry point
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
│   │      # Extract patterns from actual hunting history
│   │
│   ├── retrieval/
│   │   └── README.md
│   │      # Interface to root retrieval system
│   │      # No second independent retrieval backend
│   │
│   ├── research/
│   │   ├── graph/
│   │   │   └── README.md
│   │   │      # Future/research area for knowledge graph
│   │   │      # No implementation assumed yet
│   │   │
│   │   └── scoring/
│   │       └── README.md
│   │          # Research area for learning/scoring signals
│   │          # Not treated as mandatory core infrastructure
│   │
│   ├── scripts/
│   │   └── audit_learning_engine.py
│   │
│   └── memory/
│       └── learning.db
│
│
├── retrieval/
│   │
│   ├── search/
│   │   # Search interface / retrieval logic
│   │
│   ├── metadata/
│   │   # Source metadata, provenance, quality, confidence
│   │
│   └── index/
│       # Search indexes
│
│
├── runtime/
│   │
│   ├── registry.json
│   │   # Available runtime capabilities/tools
│   │
│   ├── tool-manifest.md
│   │   # Tool contracts and intended use
│   │
│   ├── redaction.md
│   │   # Secret/privacy redaction before external model calls
│   │
│   ├── model-routing.md
│   │   # SINGLE source of truth for model selection/routing
│   │
│   └── opencode/
│       ├── loader.md
│       │   # Loading rules for skills/context
│       │
│       ├── ORCHESTRATION.md
│       │   # Coordinator/orchestration behavior
│       │
│       └── manifest.md
│           # OpenCode runtime capability manifest
│
│
├── monitoring/
│   │
│   ├── README.md
│   │   # Scheduled monitoring architecture
│   │
│   ├── schedules/
│   │   ├── subdomains.yaml
│   │   ├── alive-assets.yaml
│   │   └── urls.yaml
│   │
│   ├── snapshots/
│   │   # Historical asset states
│   │
│   ├── diff/
│   │   └── engine.py
│   │      # Compare previous vs current state
│   │
│   ├── collectors/
│   │   ├── subdomains.py
│   │   ├── http.py
│   │   └── metadata.py
│   │      # status / size / title / tech / etc.
│   │
│   └── notify/
│       ├── rules.md
│       │   # What changes deserve notification
│       └── notifier.py
│           # New asset / changed asset / dead→alive / etc.
│
│
├── scripts/
│   ├── update.sh
│   ├── runtime_preflight.py
│   ├── skill_registry.py
│   ├── fetch.py
│   ├── process.py
│   ├── index.py
│   ├── learn.py
│   ├── test_skill_registry.py
│   └── test_runtime_contract.py
│
│
├── templates/
│   │
│   ├── ledger.md
│   ├── notes-funnel.md
│   ├── coverage-map.md
│   ├── target-config.md
│   ├── hypothesis.md
│   ├── finding.md
│   ├── regression.md
│   └── prompts/
│
│
├── testing/
│   │
│   └── <root-domain>/
│       │
│       ├── scope.md
│       │   # Explicit scope / exclusions / third-party boundaries
│       │
│       ├── agent-briefing.md
│       │   # Compact target context
│       │
│       ├── target-config.md
│       │   # Target-specific runtime/config
│       │
│       ├── ledger.md
│       │   # Persistent target state
│       │
│       ├── notes-funnel.md
│       │   # notes → leads → primitives → findings → reports
│       │
│       ├── coverage-map.md
│       │   # endpoint × parameter × vuln class coverage
│       │
│       ├── identities/
│       │   ├── user-a.md
│       │   ├── user-b.md
│       │   ├── admin.md
│       │   └── unauthenticated.md
│       │   # Identity/session definitions and capabilities
│       │
│       ├── research/
│       │   ├── reports/
│       │   ├── writeups/
│       │   ├── technologies.md
│       │   ├── subdomains.md
│       │   ├── business.md
│       │   └── hypotheses.md
│       │       # Program-specific intelligence
│       │
│       ├── recon/
│       │   # Raw + processed recon output
│       │
│       ├── app-map/
│       │   # Application structure/features
│       │
│       ├── endpoints/
│       │   # Discovered endpoint inventory
│       │
│       ├── requests/
│       │   # Raw/relevant HTTP requests
│       │
│       ├── responses/
│       │   # Raw/relevant HTTP responses
│       │
│       ├── screenshots/
│       │
│       ├── scripts/
│       │
│       ├── scans/
│       │
│       ├── urls/
│       │
│       ├── subdomains/
│       │
│       ├── wordlists/
│       │
│       ├── hypotheses/
│       │
│       ├── evidence/
│       │
│       ├── runs/
│       │   ├── run-001/
│       │   │   ├── coverage.md
│       │   │   ├── findings.md
│       │   │   ├── misses.md
│       │   │   └── state.json
│       │   ├── run-002/
│       │   ├── run-003/
│       │   └── comparison.md
│       │       # Compare repeated runs and identify non-deterministic misses
│       │
│       ├── oob/
│       │   ├── callbacks/
│       │   │   # OOB events with correlation identifiers
│       │   ├── payloads/
│       │   └── correlation.md
│       │       # Map callbacks back to requests / solver / iteration
│       │
│       ├── regression/
│       │   ├── registry.md
│       │   │   # Known findings/fixes to re-test
│       │   ├── schedules.md
│       │   └── history/
│       │       # Regression runs and bypass attempts
│       │
│       └── findings/
│           └── <finding-id>/
│               ├── candidate.md
│               ├── validation-report.md
│               ├── finding.md
│               ├── finding.json
│               ├── regression.md
│               └── poc/
│                   # Reproduction / proof artifacts
│
│
├── wordlists/
│   # Global reusable wordlists
│
├── logs/
│   # Runtime/session/system logs
│
│
├── architecture/
│   │
│   ├── agent-architecture-rules.md
│   │   # Coordinator / worker / detector boundaries
│   │
│   ├── file-contracts.md
│   │   # What each artifact/file is allowed to contain
│   │
│   ├── knowledge-flow.md
│   │   # source → processed → concepts → retrieval
│   │
│   ├── learning-flow.md
│   │   # finding/run/report → lesson → memory
│   │
│   ├── validation-model.md
│   │   # Know / Prove / Suggest + proof gate
│   │
│   └── agent-security.md
│       # Prompt injection, tool poisoning, MCP trust,
│       # response attacks, secret exposure
│
│
└── .github/
    └── workflows/
        # CI / validation / repository checks
```

# أهم التصحيحات في v2

### 1. Monitoring بقى subsystem حقيقي

مش مجرد `update.md`.

الـ flow بقى:

```text
Scheduler
   ↓
Collect current state
   ↓
Snapshot
   ↓
Diff
   ↓
Classify change
   ↓
Notify
   ↓
Optional target investigation
```

وده يغطي:

```text
new subdomain
dead → alive
status change
title change
size change
technology change
new URL/features
```

يعني الـ recon أصبح **static + continuous observation**.

---

### 2. Run Comparison بقى جزء أساسي من Target State

```text
Run 001
   ↓
Coverage / Findings / Misses

Run 002
   ↓
Coverage / Findings / Misses

Run 003
   ↓
Coverage / Findings / Misses

        ↓

comparison.md
```

وده مهم لأن:

> نفس الـ prompt + نفس الـ target ≠ نفس النتيجة بالضرورة.

فإحنا مش هنفترض إن أول run خلص الكون كله، لأن الـ LLM أحيانًا ينسى حاجة كان شايفها قبل خمس دقائق، وهي مهارة بشرية قديمة جدًا.

---

### 3. OOB بقى له correlation model

مش مجرد:

```text
interactsh
```

لكن:

```text
solver
  ↓
iteration-id
  ↓
request-id
  ↓
payload-id
  ↓
OOB callback
  ↓
correlation
  ↓
validation
```

مثلًا:

```text
/xss-142
/xxe-031
/ssrf-204
```

والـ callback نعرف بالضبط جه من أنهي محاولة.

---

### 4. Regression أصبح مفهوم مستقل

عندنا فرق بين:

```text
Finding
```

و:

```text
Finding was fixed
```

ثم:

```text
Did the fix actually remove the class?
Can another path bypass it?
Did they fix one endpoint but leave another?
```

لذلك عندنا:

```text
regression/
```

على مستوى الـ target، و:

```text
findings/<id>/regression.md
```

على مستوى الـ finding نفسه.

---

### 5. `session-search` و `identities` منفصلين تمامًا

وده تصحيح مهم جدًا.

```text
session-search
    ↓
"أنا شفت حاجة شبه دي قبل كده فين؟"
```

بينما:

```text
identities
    ↓
"أنا بدخل كـ مين؟"
"إيه الـ privileges بتاعة كل account؟"
"إيه الـ session المرتبطة بيه؟"
```

الاتنين قريبين في الاسم، لكن مختلفين في الوظيفة.

---

# التعديلات المعمارية النهائية

## Model Routing

مصدر واحد:

```text
runtime/model-routing.md
```

وأي مكان تاني محتاجه يشاور عليه بدل النسخ.

---

## Retrieval

مصدر واحد:

```text
retrieval/
```

والـ learning engine يعمل:

```text
learning_engine/
└── retrieval/
    └── README.md
```

يعني:

```text
Learning Engine
      ↓
Root Retrieval
```

مش نظامين بحث مستقلين يتخانقوا على الحقيقة.

---

## Knowledge Graph

مش هنبني حاجة لسه المصادر نفسها ما حددتش شكلها.

بدل:

```text
graph/builder.py
```

أصبح:

```text
learning_engine/
└── research/
    └── graph/
        └── README.md
```

وده يثبت إن:

> Knowledge graph = research direction
> وليس core dependency.

---

## Scoring

نفس الفكرة.

مش هنعتبر:

```text
scoring engine
```

حقيقة مفروغ منها من البداية.

بقى:

```text
learning_engine/research/scoring/
```

عشان نقدر نستكشفه بعدين بدون ما نربط الـ architecture كلها بحاجة لسه مش محددة.

---

# الـ System Logic النهائي

الـ tree لوحده مش أهم حاجة. المهم العلاقات:

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
          ┌───────────┴───────────┐
          ▼                       ▼
      MONITORING                RECON
          │                       │
          └───────────┬───────────┘
                      ▼
                 APP MAP
                      │
                      ▼
                COVERAGE MAP
                      │
                      ▼
                COORDINATOR
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
       DETECTOR    GENERATIVE   SPECIALIZED
                     AGENT       WORKFLOW
          │           │           │
          └───────────┼───────────┘
                      ▼
                 SKILLS
                      │
                      ▼
                   RUNTIME
          ┌───────────┼────────────┐
          ▼           ▼            ▼
        Burp       Browser       CLI/OOB
          │           │            │
          └───────────┼────────────┘
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
              ┌───────┴────────┐
              ▼                ▼
           FINDING          REJECTED
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
      ┌───────┴─────────┐
      ▼                 ▼
   MEMORY            CONCEPTS
      │                 │
      └───────┬─────────┘
              ▼
          RETRIEVAL
              │
              └──────────→ next hunt
```

وده بيخلي النظام **closed-loop** بدل ما يكون مجرد مجموعة Skills جنب بعض.

## Canonical separation

```text
AGENTS.md
    = Constitution

.opencode/
    = Operational behavior

knowledge/
    = Raw external sources

concepts/
    = Synthesized knowledge

memory/
    = Lessons learned from actual work

retrieval/
    = How knowledge is found

testing/<target>/
    = Everything specific to one target

runtime/
    = Execution layer

monitoring/
    = Continuous observation

learning_engine/
    = How the system learns from its own history

architecture/
    = Contracts and system design
```

وده في رأيي الشكل اللي نثبت عليه الـ blueprint. **مش محتاجين نضيف subsystem جديدة لمجرد إننا نقدر نضيف folders**. الحاجات اللي المصادر دعمتها موجودة، والحاجات اللي كانت مجرد أفكار مستقبلية مثل الـ knowledge graph والـ scoring اتفصلت رسميًا كـ research بدل ما نعاملها كأنها بنية مثبتة.

**دي النسخة اللي أعتبرها canonical من ناحية التصميم قبل ما نبدأ نبني الملفات فعليًا.**
