أيوه. كده هنثبت **Blueprint كامل واحد للنظام النهائي**، مش V1/V2/V3 منفصلين. كل المكونات المهمة مكانها من الآن، والتنفيذ نفسه ممكن يحصل واحدة واحدة حسب الحاجة. وهنراعي إن OpenCode هو الـruntime الأساسي: `AGENTS.md` للتعليمات الدائمة، `opencode.json` للـconfig، و`.opencode/skills/*/SKILL.md` للـSkills الأصلية التي تُحمّل عند الحاجة. `.agents/skills` مجرد compatibility path في OpenCode، مش نسخة ثانية نعملها يدويًا. ([OpenCode][1])

# Godbug — Final Architecture Blueprint

```text
hunting/
│
├── README.md
│   # Description:
│   # تعريف المشروع، الهدف، طريقة التشغيل العامة، والمكونات الرئيسية.
│   # لا يحتوي methodology تفصيلية أو target-specific state.
│
├── AGENTS.md
│   # Description:
│   # Constitution الخاصة بالمشروع.
│   # قواعد الـscope، الـworkflow، state، evidence، validation،
│   # human-in-the-loop، والحدود العامة للـAgent.
│   # يجب أن يظل قصيرًا نسبيًا ولا يتحول إلى encyclopedia.
│
├── prioritization.md
│   # Description:
│   # Framework ترتيب الأولويات:
│   # impact / likelihood / attack surface / confidence / effort.
│   # يحدد كيف يختار النظام ما الذي يستحق الانتباه أولًا.
│
├── opencode.json
│   # Description:
│   # OpenCode project configuration.
│   # MCP servers، permissions، agents، models/defaults،
│   # وأي instructions/references إضافية نحتاجها.
│
├── package.json
│   # Description:
│   # Node dependencies الخاصة بالـBrowser/Playwright أو
│   # أي runtime scripts تعتمد على Node.
│
│
├── .opencode/
│   │
│   ├── skills/
│   │   # Description:
│   │   # الـcanonical operational methodology الخاصة بـOpenCode.
│   │   # كل Skill عبارة عن directory مستقل + SKILL.md.
│   │   # OpenCode يكتشف الـSkills ويعرض metadata ثم يحملها
│   │   # عند الحاجة بدل إدخال كل المنهجيات في الـcontext. :contentReference[oaicite:1]{index=1}
│   │
│   │   ├── scope-bootstrap/
│   │   │   └── SKILL.md
│   │   │       # Description:
│   │   │       # يبدأ Target جديد:
│   │   │       # scope → identities → state → coverage → gaps.
│   │
│   │   ├── program-research/
│   │   │   └── SKILL.md
│   │   │       # Description:
│   │   │       # Research Mode:
│   │   │       # reports / writeups / technologies / business /
│   │   │       # known patterns → hypotheses.
│   │
│   │   ├── recon/
│   │   │   └── SKILL.md
│   │   │       # Description:
│   │   │       # asset discovery، DNS، HTTP، crawling،
│   │   │       # technology discovery، وربط النتائج بالـTarget State.
│   │
│   │   ├── js-discovery/
│   │   │   └── SKILL.md
│   │   │       # Description:
│   │   │       # اكتشاف وتحميل JavaScript فقط.
│   │   │       # لا يحتوي تحليل JS العميق.
│   │
│   │   ├── js-analyze/
│   │   │   └── SKILL.md
│   │   │       # Description:
│   │   │       # تحليل JS، sources/sinks، endpoints،
│   │   │       # secrets، client-side behavior والـinteresting flows.
│   │
│   │   ├── session-search/
│   │   │   └── SKILL.md
│   │   │       # Description:
│   │   │       # بناء/قراءة Identity وSession Ledger وربط
│   │   │       # الحسابات بالـrequests والـauthorization context.
│   │
│   │   ├── validator/
│   │   │   └── SKILL.md
│   │   │       # Description:
│   │   │       # Proof Gate + False Positive Gate + Evidence
│   │   │       # + skeptical validation + exploit/chaining gate.
│   │
│   │   ├── report-writer/
│   │   │   └── SKILL.md
│   │   │       # Description:
│   │   │       # تحويل Finding مثبت إلى structured report.
│   │
│   │   ├── xss/
│   │   │   └── SKILL.md
│   │   │       # Description:
│   │   │       # XSS methodology:
│   │   │       # discovery → context → controlled probes →
│   │   │       # browser proof → evidence.
│   │
│   │   └── access-control/
│   │       └── SKILL.md
│   │           # Description:
│   │           # IDOR/BAC/Auth boundary testing،
│   │           # identities، object ownership، alternate paths،
│   │           # validation.
│   │
│   ├── agents/
│   │   # Description:
│   │   # Project-specific OpenCode agents.
│   │   # قد يبدأ فارغًا؛ لا يعني أننا نطلق swarm من أول يوم.
│   │   # عندما نحتاج Solver/Validator/Researcher فعليًا،
│   │   # يتم تعريفه هنا أو في opencode.json. OpenCode يدعم
│   │   # project agents كـMarkdown داخل .opencode/agents/. :contentReference[oaicite:2]{index=2}
│   │
│   └── commands/
│       # Description:
│       # أوامر عالية المستوى لتشغيل workflows متكررة.
│
│       ├── hunt.md
│       │   # Description:
│       │   # تشغيل focused hunting loop.
│       │
│       ├── recon.md
│       │   # Description:
│       │   # تشغيل/استكمال Recon workflow.
│       │
│       ├── update.md
│       │   # Description:
│       │   # تحديث target state / briefing / knowledge / lessons.
│       │
│       ├── validate.md
│       │   # Description:
│       │   # تشغيل validation workflow على Candidate/Finding.
│       │
│       ├── report.md
│       │   # Description:
│       │   # إنتاج structured report من Finding مثبت.
│       │
│       ├── research.md
│       │   # Description:
│       │   # تشغيل Research Mode على برنامج أو موضوع.
│       │
│       ├── understand.md
│       │   # Description:
│       │   # بناء/تحديث Application Understanding قبل الاختبارات.
│       │
│       └── mindmap.md
│           # Description:
│           # بناء/عرض العلاقات بين hosts، features،
│           # endpoints، identities، technologies، findings.
│
│
├── knowledge/
│   # Description:
│   # External Knowledge.
│   # المعرفة القادمة من العالم الخارجي وليست ذاكرة الـAgent الخاصة.
│   # كل مصدر يحتفظ بـURL/source metadata/provenance.
│
│   ├── writeups/
│   │   # Description:
│   │   # Bug bounty writeups التي جمعناها أو لخصناها.
│   │
│   ├── reports/
│   │   # Description:
│   │   # Disclosed reports، خصوصًا التقارير الخاصة ببرامج محددة.
│   │
│   ├── blogs/
│   │   # Description:
│   │   # مقالات ومنهجيات وتقنيات منشورة على blogs.
│   │
│   ├── posts/
│   │   # Description:
│   │   # Posts / threads / community knowledge.
│   │
│   ├── tips-tricks/
│   │   # Description:
│   │   # Heuristics صغيرة، tricks، observations،
│   │   # useful commands، edge cases.
│   │
│   ├── research/
│   │   # Description:
│   │   # أبحاث تقنية، دراسات، security research، papers.
│   │
│   ├── docs/
│   │   # Description:
│   │   # Documentation التي يمكن تحويلها إلى constraints
│   │   # أو expected behavior لاختباره.
│   │
│   ├── podcasts/
│   │   # Description:
│   │   # Knowledge extracted من podcasts/audio.
│   │
│   ├── videos/
│   │   # Description:
│   │   # Structured notes المستخلصة من الفيديوهات.
│   │
│   └── processed/
│       # Description:
│       # Structured extraction من المصادر:
│       # patterns / cases / techniques / prerequisites /
│       # validation / impact / FP / provenance.
│
│
├── concepts/
│   # Description:
│   # المعرفة المركبة بعد دمج عدة مصادر.
│   # هنا تتحول "معلومات منفصلة" إلى reusable understanding.
│
│   ├── methodology/
│   │   # Description:
│   │   # منهجيات عامة للحunting والتفكير والتنفيذ.
│
│   ├── heuristics/
│   │   # Description:
│   │   # Hunting heuristics المستخلصة من التجارب والمصادر.
│
│   ├── vulnerability-classes/
│   │   # Description:
│   │   # المعرفة الخاصة بكل vulnerability class.
│   │
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
│   │   │   └── ...
│   │   │
│   │   └── access-control/
│   │       └── ...
│   │
│   ├── attack-patterns/
│   │   # Description:
│   │   # Patterns قابلة لإعادة الاستخدام عبر أكثر من vulnerability.
│   │
│   ├── false-positives/
│   │   # Description:
│   │   # حالات تبدو vulnerable لكنها ليست كذلك + طريقة تمييزها.
│   │
│   └── prioritization/
│       # Description:
│       # Patterns تساعد في اختيار أين نبدأ وماذا نتجاهل مؤقتًا.
│
│
├── memory/
│   # Description:
│   # ما تعلمناه من الـHunting الفعلي الخاص بنا.
│   # مختلف عن knowledge الخارجية.
│
│   ├── FALSE_POSITIVE_DB.md
│   │   # Description:
│   │   # FP patterns المكتشفة فعليًا أثناء الاختبار.
│
│   ├── patterns.md
│   │   # Description:
│   │   # Attack patterns التي أثبتتها تجاربنا.
│
│   ├── methodology.md
│   │   # Description:
│   │   # تعديلات على methodology بناءً على النتائج الواقعية.
│
│   ├── report-lessons.md
│   │   # Description:
│   │   # lessons المستخلصة من reports/writeups بعد مراجعتها.
│
│   ├── CONTEXT_LEDGER.md
│   │   # Description:
│   │   # سجل قرارات وملاحظات وسياق مهم عبر التشغيل.
│
│   └── targets/
│       # Description:
│       # Lessons خاصة بنوع vulnerability أو pattern محدد.
│
│
├── learning_engine/
│   # Description:
│   # النظام المسؤول عن تحويل مصادر ونتائج الـHunt إلى معرفة
│   # قابلة للتقييم والاسترجاع والتطور.
│
│   ├── engine.py
│   │   # Description:
│   │   # Public API / orchestration للـlearning pipeline.
│
│   ├── core/
│   │   ├── config.py
│   │   │   # Description: إعدادات الـlearning pipeline.
│   │   ├── schema.py
│   │   │   # Description: schemas للـsources/cases/lessons.
│   │   ├── validation.py
│   │   │   # Description: structural + quality validation.
│   │   └── storage.py
│   │       # Description: storage abstraction.
│   │
│   ├── ingestion/
│   │   ├── finding.py
│   │   │   # Description: تحويل findings إلى learning signals.
│   │   └── triage.py
│   │       # Description: تقييم ما يستحق التعلم وما لا يستحق.
│   │
│   ├── scoring/
│   │   ├── engine.py
│   │   │   # Description: scoring للـquality / confidence /
│   │   │   # novelty / usefulness.
│   │   └── signals.json
│   │       # Description: إشارات scoring وقواعدها.
│   │
│   ├── retrieval/
│   │   └── search.py
│   │       # Description: استرجاع المعرفة ذات الصلة بالمهمة الحالية.
│   │
│   ├── analytics/
│   │   └── patterns.py
│   │       # Description: تحليل patterns، repetition، novelty،
│   │       # وcoverage gaps.
│   │
│   ├── graph/
│   │   └── builder.py
│   │       # Description:
│   │       # بناء العلاقات بين vulnerabilities، technologies،
│   │       # techniques، endpoints، patterns، findings.
│   │
│   ├── scripts/
│   │   └── audit_learning_engine.py
│   │       # Description: health/integrity checks للـlearning system.
│   │
│   └── memory/
│       └── learning.db
│           # Description:
│           # persistent structured learning storage.
│
│
├── retrieval/
│   # Description:
│   # واجهة استرجاع المعرفة.
│   # تفصل "أين وكيف نبحث" عن الـknowledge نفسها.
│
│   ├── search/
│   │   # Description: keyword / metadata / semantic retrieval.
│   ├── metadata/
│   │   # Description: source tags, topics, confidence, quality,
│   │   # provenance, timestamps.
│   └── index/
│       # Description: indexes التي يستخدمها الـretriever.
│
│
├── runtime/
│   # Description:
│   # طبقة التنفيذ المشتركة بين الـAgent والمكونات الخارجية.
│
│   ├── registry.json
│   │   # Description:
│   │   # canonical mapping بين skills / aliases / commands /
│   │   # agents / execution routes.
│
│   ├── tool-manifest.md
│   │   # Description:
│   │   # ماذا يفعل كل tool، inputs، outputs، القيود، ومتى يستخدم.
│
│   ├── model-routing.md
│   │   # Description:
│   │   # قواعد اختيار model حسب نوع المهمة:
│   │   # extraction / classification / reasoning / validation...
│
│   ├── redaction.md
│   │   # Description:
│   │   # قواعد إزالة secrets/PII/auth material قبل إرسال
│   │   # البيانات إلى cloud models عند الحاجة.
│
│   └── opencode/
│       # Description:
│       # OpenCode-specific runtime contract.
│       ├── loader.md
│       │   # Description: كيف يتم bootstrap للـworkspace.
│       ├── ORCHESTRATION.md
│       │   # Description: coordinator → objective → execution flow.
│       └── manifest.md
│           # Description: runtime capabilities المتاحة للـOpenCode.
│
│
├── scripts/
│   # Description:
│   # deterministic automation التي لا تحتاج LLM.
│
│   ├── update.sh
│   │   # Description:
│   │   # تحديث state / briefing / indexes / learning artifacts.
│
│   ├── runtime_preflight.py
│   │   # Description:
│   │   # Boot gate:
│   │   # scope + tools + MCP + workspace + runtime readiness.
│
│   ├── skill_registry.py
│   │   # Description:
│   │   # Scenario → candidate skills / routing / coverage.
│   │   # ليس بديل OpenCode skill loader.
│
│   ├── fetch
│   │   # Description: جلب المصادر/المواد المطلوبة.
│
│   ├── process
│   │   # Description: normalize + extract.
│
│   ├── index
│   │   # Description: بناء/تحديث indexes.
│
│   ├── learn
│   │   # Description: تشغيل learning pipeline.
│
│   ├── test_skill_registry.py
│   │   # Description: tests للـskill routing contract.
│
│   └── test_runtime_contract.py
│       # Description: tests للـruntime contract.
│
│
├── templates/
│   # Description:
│   # قوالب ثابتة تمنع الـAgent من اختراع formats مختلفة كل مرة.
│
│   ├── ledger.md
│   │   # Description: target state/decision ledger.
│   ├── notes-funnel.md
│   │   # Description: Notes → Leads → Hypotheses → Findings.
│   ├── coverage-map.md
│   │   # Description: Endpoint × Parameter × Vulnerability Class.
│   ├── target-config.md
│   │   # Description: scope + identities + technologies.
│   ├── hypothesis.md
│   │   # Description: hypothesis structure.
│   ├── finding.md
│   │   # Description: confirmed finding/report structure.
│   └── prompts/
│       # Description:
│       # Prompt Library / reusable task framing.
│
│
├── testing/
│   # Description:
│   # Target-specific persistent state.
│   # كل Program/Target يحتفظ بسياقه بعيدًا عن الـglobal knowledge.
│
│   └── <root-domain>/
│       │
│       ├── scope.md
│       │   # Description:
│       │   # Human-defined scope lock.
│
│       ├── agent-briefing.md
│       │   # Description:
│       │   # compressed current operational context.
│       │   # ماذا نعرف الآن؟ أين توقفنا؟ ما التالي؟
│
│       ├── target-config.md
│       │   # Description:
│       │   # technologies + accounts + identities + environment.
│
│       ├── ledger.md
│       │   # Description:
│       │   # canonical target ledger.
│
│       ├── notes-funnel.md
│       │   # Description:
│       │   # observations → leads → hypotheses → findings.
│
│       ├── coverage-map.md
│       │   # Description:
│       │   # ما تم اختباره وما لم يتم.
│
│       ├── identities/
│       │   # Description:
│       │   # Account/identity context للـauthorization testing.
│       │   ├── anonymous.md
│       │   ├── user-a.md
│       │   ├── user-b.md
│       │   └── admin.md
│       │
│       ├── research/
│       │   # Description:
│       │   # Program Knowledge Pack الخاص بالهدف.
│       │
│       │   ├── reports/
│       │   ├── writeups/
│       │   ├── technologies.md
│       │   ├── subdomains.md
│       │   └── business.md
│       │
│       ├── recon/
│       │   # Description:
│       │   # assets / subdomains / DNS / HTTP / ports / snapshots.
│       │
│       ├── app-map/
│       │   # Description:
│       │   # application understanding:
│       │   # features / endpoints / JS / Swagger / flows / trust boundaries.
│       │
│       ├── endpoints/
│       │   # Description:
│       │   # endpoint registry + parameters + source + method + notes.
│       │
│       ├── requests/
│       │   # Description: important captured/raw requests.
│       ├── responses/
│       │   # Description: important captured/raw responses.
│       ├── screenshots/
│       │   # Description: browser/runtime evidence.
│       ├── scripts/
│       │   # Description: target-specific helper scripts.
│       ├── scans/
│       │   # Description: deterministic scanner outputs.
│       ├── urls/
│       │   # Description: discovered URL inventories.
│       ├── subdomains/
│       │   # Description: subdomain inventories + diffs.
│       ├── wordlists/
│       │   # Description: target-scoped wordlists.
│       ├── hypotheses/
│       │   # Description: active hypotheses and decisions.
│       ├── evidence/
│       │   # Description: proof artifacts.
│       │
│       └── findings/
│           # Description:
│           # candidate → validation → confirmed finding.
│
│           └── <finding-id>/
│               ├── candidate.md
│               │   # Description: initial hypothesis.
│               ├── validation-report.md
│               │   # Description: skeptical validation results.
│               ├── finding.md
│               │   # Description: final validated finding.
│               ├── finding.json
│               │   # Description: machine-readable finding object.
│               └── poc/
│                   # Description: reproducible proof material.
│
│
├── wordlists/
│   # Description:
│   # Global reusable wordlists.
│   # Target-specific copies/derived lists live under testing/<target>.
│
├── logs/
│   # Description:
│   # runtime/event/decision logs.
│
├── architecture/
│   # Description:
│   # Design contracts التي تشرح لماذا النظام بالشكل ده.
│
│   ├── agent-architecture-rules.md
│   │   # Description:
│   │   # v6 architecture rules / constitutional design.
│   │
│   ├── file-contracts.md
│   │   # Description:
│   │   # لكل ملف: owner، readers، writers، inputs، outputs،
│   │   # source-of-truth، وما الذي لا يجب أن يحتويه.
│   │
│   ├── knowledge-flow.md
│   │   # Description:
│   │   # Source → extraction → synthesis → concepts → retrieval.
│   │
│   ├── learning-flow.md
│   │   # Description:
│   │   # Finding/FP/miss → lesson → memory → methodology improvement.
│   │
│   ├── validation-model.md
│   │   # Description:
│   │   # Candidate → Proof Gate → FP Gate → Validator → Human.
│   │
│   ├── model-routing.md
│   │   # Description:
│   │   # routing/cost/reasoning policy.
│   │
│   └── agent-security.md
│       # Description:
│       # Prompt injection / indirect injection / tool poisoning /
│       # MCP trust / data exfiltration / secret exposure.
│
│
└── .github/
    └── workflows/
        # Description:
        # CI للـcontracts، learning engine، skill registry،
        # schema validation، وعدم كسر الـrepository.
```

---

# العلاقات الأساسية بين الطبقات

وده أهم من شكل الفولدر نفسه:

```text
                         HUMAN
                           │
                           ▼
                    ┌─────────────┐
                    │   RESEARCH  │
                    └──────┬──────┘
                           │
                Program Knowledge Pack
                           │
                           ▼
                         SCOPE
                           │
                           ▼
                    TARGET STATE
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
          IDENTITIES    COVERAGE     APP MAP
              │            │            │
              └────────────┼────────────┘
                           ▼
                      OBJECTIVE
                           │
                           ▼
                    ORCHESTRATOR
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
      GENERATIVE AGENT            SPECIALIZED DETECTOR
             │                           │
        SKILL + KNOWLEDGE               │
             │                           │
             └─────────────┬─────────────┘
                           ▼
                         RUNTIME
                Burp / Browser / OOB / CLI
                           │
                           ▼
                     OBSERVATIONS
                           │
                  KNOW / PROVE / SUGGEST
                           │
                           ▼
                       PROOF GATE
                           │
                           ▼
                         FP GATE
                           │
                           ▼
                       VALIDATOR
                           │
                           ▼
                     HUMAN REVIEW
                           │
                  ┌────────┴────────┐
                  ▼                 ▼
               FINDING           REJECTED
                  │                 │
                  ▼                 ▼
               REPORT           FP LESSON
                  │                 │
                  └────────┬────────┘
                           ▼
                        LEARNING
                           │
              ┌────────────┼─────────────┐
              ▼            ▼             ▼
           MEMORY       CONCEPTS      SKILLS
              │            │             │
              └────────────┴─────────────┘
                           ↺
```

---

# أهم الـBoundaries

دي لازم تبقى **قوانين معمارية**:

```text
AGENTS.md
    = كيف يتصرف النظام

opencode.json
    = كيف يعمل OpenCode

.opencode/skills/
    = كيف ننفذ methodology معينة

knowledge/
    = ماذا تعلمنا من العالم الخارجي

concepts/
    = ماذا استخلصنا وعممناه

memory/
    = ماذا تعلمنا من Hunts الخاصة بنا

testing/<target>/
    = ماذا نعرف عن Target محدد

learning_engine/
    = كيف يتحول كل ذلك إلى learning/retrieval

runtime/
    = كيف تُنفذ الأفعال فعليًا
```

والـSkills في `.opencode/skills` هي الـnative choice عندنا، وتُحمّل عند الحاجة، بينما `.agents/skills` مجرد مسار compatibility مدعوم من OpenCode، ولذلك **لن نعمل نسختين من نفس الـSkill بلا سبب**. ([OpenCode][1])

---

# المعرفة: كيف تدخل للنظام؟

وده تحديدًا الجزء اللي اتفقنا عليه بخصوص الـ100 writeup والـblogs والـtips:

```text
Writeups
Reports
Blogs
Posts
Research
Docs
Tips & Tricks
Videos
Podcasts
        │
        ▼
     Ingestion
        │
        ▼
    Structured Cases
        │
        ▼
    Dedup / Cluster
        │
        ▼
     Synthesis
        │
        ▼
     concepts/
        │
        ├──────────────► Skills
        │
        ├──────────────► Heuristics
        │
        └──────────────► Retrieval
```

و**مش لازم كل مصدر يتحول Skill**.

مصدر ممكن ينتج:

```text
Pattern
Scenario
Heuristic
False Positive
Validation technique
Research question
Skill improvement
```

وده أقرب للاستفادة الحقيقية من الـknowledge الكبيرة من مجرد حشرها في prompt أو Vector DB. فكرة انتقاء الـcontext والـskills وعدم إغراق الـAgent كانت متكررة في المصادر التي راجعناها. 

---

# Research Mode

```text
Program
 ↓
Known reports
 ↓
Known technologies
 ↓
Business model
 ↓
Known attack patterns
 ↓
Research questions
 ↓
Hypotheses
 ↓
Focused test
```

وده **قبل الـExecution الواسع**.

والمعرفة الخاصة بالبرنامج تعيش هنا:

```text
testing/<target>/research/
```

بينما الـknowledge العامة تعيش في:

```text
knowledge/
```

---

# Proof Model

ده ثابت ومش هنغيره:

```text
LLM
 ↓
Hypothesis
 ↓
Runtime
 ↓
Evidence
 ↓
Validator
 ↓
Human
```

مش:

```text
LLM: 98%
 ↓
CONFIRMED
```

المشروع اللي درسناه للـXSS استخدم نفس الفصل بوضوح: الـAI لا يقرر confirmation، والـbrowser/runtime هو الذي يثبت execution. 

---

# Specialized Detector

الـGenerative Agent مش لازم يعمل كل حاجة.

```text
Agent
 ↓
"Need XSS validation"
 ↓
Specialized Detector
 ↓
Browser
 ↓
True / False + Evidence
```

وبكده:

```text
Generative AI
= strategy / reasoning / hypotheses

Detector
= narrow deterministic task
```

الفكرة ظهرت بوضوح في مشروع الـSLM/XSS المتخصص. 

---

# الـLearning Engine

وهو **جزء من الـArchitecture النهائية فعلًا**، لكن مش معناه إن كل مكوناته لازم تكون فعالة من أول ساعة.

الهدف:

```text
Sources
   ↓
Knowledge

Hunt
   ↓
Observations
   ↓
Findings / FP / Misses
   ↓
Learning Signals
   ↓
Patterns / Lessons
   ↓
Methodology / Skills
```

والـRun Comparison يدخل هنا:

```text
Run 1
Run 2
Run 3
   ↓
Coverage difference
   ↓
Miss analysis
   ↓
Why did the Agent fail?
   ↓
Methodology improvement
```

وده مبني على فكرة الـnon-deterministic runs واستخدام المقارنة لمعرفة الـmisses، وليس مجرد إعادة التشغيل بلا هدف. 

---

# وأخيرًا: إيه اللي **مش** في الـBlueprint؟

لا يوجد:

```text
❌ Fine-tuning required
❌ Model-specific architecture
❌ One agent per vulnerability
❌ Load all skills at startup
❌ Load all knowledge at startup
❌ Autonomous forever hunting كافتراض
❌ 23 subagents لمجرد أن العدد يبدو ذكيًا
❌ Raw writeups باعتبارها training data مباشرة
❌ Confirmation based on LLM confidence
```

النظام مصمم بحيث **الموديل قابل للتبديل**، والـKnowledge والـTarget State والـSkills يفضلوا خارج الـmodel نفسه. OpenCode يسمح بتعريف Agents بmodels/permissions مختلفة، لكن الـV1 عندنا لن تفترض swarm من البداية. ([OpenCode][2])

---

## الخلاصة

**ده هو الـBlueprint اللي كنت تقصده:**

```text
             KNOWLEDGE
                  │
        ┌─────────┴─────────┐
        ▼                   ▼
     CONCEPTS             RESEARCH
        │                   │
        ▼                   ▼
     SKILLS             TARGET INTEL
        │                   │
        └─────────┬─────────┘
                  ▼
              TARGET STATE
                  │
                  ▼
             ORCHESTRATOR
                  │
             OBJECTIVE
                  │
        ┌─────────┴─────────┐
        ▼                   ▼
     AGENT              DETECTOR
        │                   │
        └─────────┬─────────┘
                  ▼
               RUNTIME
                  │
                  ▼
             OBSERVATION
                  │
                  ▼
              PROOF GATE
                  │
                  ▼
              VALIDATOR
                  │
                  ▼
             HUMAN REVIEW
                  │
                  ▼
              FINDING
                  │
                  ▼
              LEARNING
                  │
                  └──────────────↺
```

والـTree ده **هو الـbuild target الكامل**. مش مطلوب نخلص كل ملف النهارده، لكن **مش هنخلق architecture ثانية لاحقًا**. أي تنفيذ من هنا هيملأ أجزاء هذا الـBlueprint، وأي فكرة جديدة تظهر لازم نعرف أولًا هل هي تحسين لمكون موجود ولا فعلًا تحتاج مكونًا جديدًا.

ده، في رأيي، وصلنا أخيرًا من مرحلة "نجمع أفكار عن AI Bug Hunting" إلى **تصميم نظام محدد نقدر نبنيه ونقيسه ونفشل فيه بشكل مفيد بدل ما نفشل بشكل عشوائي**.

[1]: https://opencode.ai/docs/skills?utm_source=chatgpt.com "Agent Skills | OpenCode"
[2]: https://opencode.ai/docs/agents/?utm_source=chatgpt.com "Agents | OpenCode"
