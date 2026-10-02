أيوه. بصراحة **إحنا وصلنا لنقطة كفاية جدًا من ناحية المحتوى النظري**. آخر كام فيديو وبوستات كرروا نفس المحاور بأسماء مختلفة: Skills، Persistent Context، Orchestrator، Validation، MCP، RAG، Delegation، Human-in-the-loop. فلو فضلنا نجمع مصادر أكتر دلوقتي، احتمال كبير نقضي الأسبوع الجاي بنعيد اكتشاف العجلة وهي واقفة في مكانها.

خلينا **نجمّد Architecture v1** ونبدأ التنفيذ.  
والـArchitecture دي **Synthesis مني من كل المصادر اللي قرأناها، وليست architecture نقلها مصدر واحد حرفيًا**.

---

# AI Bug Hunting System — Architecture v1

```text
                                      ┌──────────────┐
                                      │    HUMAN     │
                                      │ Hunter /     │
                                      │ Decision     │
                                      └──────┬───────┘
                                             │
                                    Scope / Goals / Input
                                             │
                                             ▼
┌──────────────────────────────────────────────────────────────────────────────┐
│                         TARGET WORKSPACE / STATE                             │
│                                                                              │
│  Scope  │  Hosts  │  Endpoints  │  Technologies  │  Accounts  │  Sessions  │
│  Recon  │  App Map│  Findings   │  Notes         │  Coverage  │  History   │
└──────────────────────────────────────┬───────────────────────────────────────┘
                                       │
                                       ▼
┌──────────────────────────────────────────────────────────────────────────────┐
│                         KNOWLEDGE / CONTEXT ENGINE                           │
│                                                                              │
│ Target Context    Program Reports    Writeups / Research    Previous Runs   │
│ False Positives   Primitives         Patterns                Known Chains    │
└──────────────────────────────────────┬───────────────────────────────────────┘
                                       │
                                       ▼
                              ┌─────────────────┐
                              │ ROOT /         │
                              │ ORCHESTRATOR   │
                              └────────┬────────┘
                                       │
                         Task selection / decomposition
                                       │
                         ┌─────────────┴─────────────┐
                         │                           │
                         ▼                           ▼
                Focused Task / Skill          Delegated Task
                         │                           │
                         │                    ┌──────┼──────┐
                         │                    ▼      ▼      ▼
                         │                 Worker  Worker  Worker
                         │                    A      B      C
                         │
                         └──────────────┬──────────────┘
                                        ▼
                              ┌───────────────────┐
                              │      SKILLS       │
                              │                   │
                              │ Recon             │
                              │ JS                │
                              │ Access Control     │
                              │ XSS               │
                              │ SSRF              │
                              │ SQLi              │
                              │ Business Logic    │
                              │ Browser           │
                              │ Validation        │
                              └─────────┬─────────┘
                                        │
                                        ▼
                              ┌───────────────────┐
                              │ EXECUTION RUNTIME  │
                              │                   │
                              │ Burp MCP          │
                              │ Browser MCP       │
                              │ HTTP              │
                              │ CLI / Scripts     │
                              │ VPS               │
                              │ OOB               │
                              └─────────┬─────────┘
                                        │
                                        ▼
                              ┌───────────────────┐
                              │   OBSERVATIONS     │
                              │                   │
                              │ Requests          │
                              │ Responses         │
                              │ Browser Events    │
                              │ DOM / JS          │
                              │ DNS / OOB         │
                              │ Errors            │
                              └─────────┬─────────┘
                                        │
                                        ▼
                              ┌───────────────────┐
                              │ HYPOTHESIS LAYER  │
                              │                   │
                              │ Possible vuln     │
                              │ Interesting       │
                              │ Pattern / chain   │
                              │ Attack scenario   │
                              └─────────┬─────────┘
                                        │
                                        ▼
                              ┌───────────────────┐
                              │     VALIDATOR     │
                              │                   │
                              │ Deterministic     │
                              │ Browser proof     │
                              │ Re-test           │
                              │ Diff / Compare    │
                              │ FP checks         │
                              └─────────┬─────────┘
                                        │
                         ┌──────────────┴──────────────┐
                         │                             │
                         ▼                             ▼
                    REJECTED                       CONFIRMED
                         │                             │
                    FP / Noise                    Evidence
                         │                             │
                         └──────────────┐              ▼
                                        │      ┌─────────────────┐
                                        │      │    FINDING      │
                                        │      │                 │
                                        │      │ Impact          │
                                        │      │ Preconditions   │
                                        │      │ PoC             │
                                        │      │ Raw Evidence    │
                                        │      └────────┬────────┘
                                        │               │
                                        │               ▼
                                        │      ┌─────────────────┐
                                        │      │     REPORT      │
                                        │      └────────┬────────┘
                                        │               │
                                        └───────────────┤
                                                        ▼
                                      ┌──────────────────────────┐
                                      │     LEARNING / MEMORY    │
                                      │                          │
                                      │ Target State             │
                                      │ FP DB                    │
                                      │ Patterns                 │
                                      │ New Primitives           │
                                      │ Skill Improvements       │
                                      │ Successful Workflows    │
                                      └────────────┬─────────────┘
                                                   │
                                                   ▼
                                           NEXT HUNTING CYCLE
```

---

# 1. HUMAN

الإنسان مش "زر تشغيل".

دوره في النظام:

```text
Scope
Goal
Priority
Target knowledge
Decision points
Final validation
```

المصادر اللي جمعناها كلها تقريبًا بتصب في النقطة دي، من Human-in-the-loop إلى استخدام `AskUserQuestion` إلى فكرة إن الـAI يزيد قدرة الـHunter ولا يستبدله.

---

# 2. TARGET WORKSPACE

**دي أهم قطعة في Architecture كلها.**

الفيديو بتاع HunterStick وضّح إن الـworkspace هو اللي يحفظ Target Context ويخليه مستقل عن الـModel والـSession.

يبقى عندنا:

```text
target/
├── scope/
├── recon/
├── app-map/
├── technologies/
├── accounts/
├── sessions/
├── endpoints/
├── parameters/
├── notes/
├── hypotheses/
├── findings/
├── reports/
├── evidence/
├── requests/
├── responses/
├── screenshots/
├── logs/
└── state.md
```

**مهم:** ده Target State، مش مجرد folder للـlogs.

الـstate لازم يجاوب:

> ماذا نعرف؟  
> ماذا اختبرنا؟  
> ماذا لم نختبر؟  
> ماذا وجدنا؟  
> ماذا رفضنا؟  
> أين توقفنا؟

وده متسق مع فكرة endpoint/application ledger والـworkspace state اللي ظهرت في المصادر الأولى.

---

# 3. KNOWLEDGE / CONTEXT ENGINE

هنا لازم نفصل 3 أنواع معرفة:

### A. Target Knowledge

```text
What we know about THIS target
```

### B. External Knowledge

```text
Writeups
Reports
Research
CVEs
POCs
Blogs
```

### C. Learned Hunting Knowledge

```text
Patterns
False positives
Successful workflows
Primitives
New bypasses
```

البوست الأخير وضح إن الـProgram Reports وWriteups ممكن تستخدم لفهم الـProgram، والـtechnologies، وتوليد hypotheses، مش مجرد حشو Context.

---

# 4. ORCHESTRATOR

ده **العقل الإداري**.

مش مهمته يعمل كل الاختبارات بنفسه.

مهمته:

```text
What should happen next?
```

يعني مثلًا:

```text
Target has 8,000 hosts
         ↓
Don't test everything
         ↓
Prioritize interesting surface
         ↓
Choose task
         ↓
Load relevant skill
         ↓
Execute
         ↓
Review result
         ↓
Choose next task
```

وده يطابق فكرة Coordinator/Workers ورفض وجود Agent مستقل لكل vulnerability، مع استخدام Skills كمنهجيات متخصصة.

---

# 5. TASK DECOMPOSITION / DELEGATION

دي طبقة لازم تكون **واضحة ومحدودة**.

بدل:

```text
Primary Agent
→ do everything
```

يبقى:

```text
Primary
├── Recon task
├── JS analysis
├── Access-control task
└── Validation task
```

لكن **مش 23 Agent من أول يوم**.

لأن المصادر نفسها بينت إن delegation السيئ يؤدي إلى:

```text
Context ↑
Tokens ↑
Duplication ↑
Performance ↓
```

وده ظهر بشكل صريح في تجربة Agents.md والـSubagents.

---

# 6. SKILLS

دي **الـMethodology on demand**.

```text
skills/
├── recon/
├── endpoint-analysis/
├── js/
├── access-control/
├── xss/
├── ssrf/
├── sqli/
├── business-logic/
├── browser/
├── validation/
└── reporting/
```

القاعدة:

```text
Skill metadata first
↓
Relevant skill selected
↓
Full methodology loaded
```

مش:

```text
Load 70 skills
↓
Dump all into context
↓
Pray
```

لأن Context Rot وInstruction Dilution وCompletion Bias اتكررت بوضوح في المصادر والبوسطات.

---

# 7. EXECUTION RUNTIME

هنا الـAI بيتحول من "كلام" إلى **فعل**.

```text
Burp
Browser
HTTP
Scripts
VPS
OOB
```

في بيئتك حاليًا:

```text
OpenCode
   ↓
Dahl
   ↓
MCP
   ↓
Burp
   +
Browser
```

وده بالفعل نفس نمط الـexecution اللي ظهر في HunterStick وتجارب OpenCode + Burp.

---

# 8. OBSERVATION LAYER

دي نقطة مهمة جدًا، لأننا **مش عايزين الـLLM يتعامل مع raw data بلا نهاية**.

بدل:

```text
10,000 HTTP responses
```

نعمل:

```text
Parser
↓
Interesting observations
↓
Structured records
```

مثلًا:

```json
{
  "endpoint": "/api/orders",
  "method": "GET",
  "parameter": "id",
  "auth_required": true,
  "technology": "graphql",
  "interesting": true
}
```

وده متسق مع أفكار response parsers وsmall deterministic tools وتقليل كمية الـcontext الداخلة للنموذج.

---

# 9. HYPOTHESIS LAYER

دي طبقة ضرورية.

الـAI يقول:

```text
Potential IDOR
```

مش:

```text
CONFIRMED IDOR
```

أو:

```text
Potential XSS
```

وليس:

```text
CONFIRMED XSS
```

الفيديو بتاع XSS كان صريحًا جدًا:

> الـAI ممنوع يقرر CONFIRMED، والـBrowser هو اللي يثبت الـexecution.

إذن:

```text
LLM
=
Hypothesis Generator
```

---

# 10. VALIDATION / PROOF GATE

**دي واحدة من أهم طبقات النظام.**

الـValidator لازم يكون أكثر تشككًا من الـAgent.

```text
Candidate
 ↓
Validation checklist
 ↓
Deterministic test
 ↓
Evidence
 ↓
Confirmed / Rejected
```

وده اتكرر في:

- False Positive Gate
    
- Evidence Standard
    
- Browser validation
    
- Separate validators
    
- Review skills
    
- Deterministic checks
    

---

# 11. FINDING OBJECT

أي Candidate أو Finding لازم يتحول لـobject منظم.

مثلاً:

```text
finding/
├── summary.md
├── finding.json
├── request.txt
├── response.txt
├── evidence/
├── screenshots/
├── reproduction.md
└── validation.json
```

ويحتوي:

```text
Title
Asset
Endpoint
Preconditions
Account context
Steps
Expected
Actual
Impact
Evidence
Validation status
```

وده متسق مع فكرة الاحتفاظ بالـraw request/response/account context، وليس مجرد summary من الـLLM.

---

# 12. REPORTING

التقرير **آخر خطوة**.

مش:

```text
LLM thinks bug
↓
Write report
```

لكن:

```text
Hypothesis
↓
Validation
↓
Evidence
↓
Impact
↓
Report
```

---

# 13. MEMORY / LEARNING

هنا عندنا ثلاث مستويات:

```text
Level 1
Target State

Level 2
Hunting Memory

Level 3
Methodology Improvement
```

### Target State

```text
What happened to target X
```

### Hunting Memory

```text
This pattern usually fails
This endpoint pattern is interesting
This bypass worked before
```

### Methodology

```text
Our skill missed this scenario
↓
Update methodology
```

وده بالضبط اللي ظهر في أفكار:

- False Positive DB
    
- self-improving CLAUDE.md
    
- notes → leads → primitives → findings
    
- learned instructions
    
- target-specific state.
    

---

# 14. RESEARCH LOOP

دي إضافة مهمة جدًا من البوستات الأخيرة.

مش كل معلومة تدخل أثناء الـHunt.

ممكن:

```text
Observation
   ↓
"Need more knowledge"
   ↓
Research
   ↓
Reports
Writeups
CVEs
POCs
Reddit
Docs
   ↓
Synthesize
   ↓
New hypothesis
   ↓
Return to Hunt
```

وده بالضبط النوع اللي صاحب البوست كان يقترحه مع Program-specific reports والـtechnologies والـworkflow research.

---

# 15. PROGRAM INTELLIGENCE

دي حاجة أنا شايف لازم تبقى layer مستقلة.

قبل Hunting قوي على Target:

```text
Program
├── Historical reports
├── Known technologies
├── Subdomains
├── Product areas
├── Business model
├── Known patterns
└── Public research
```

ثم:

```text
Program Intelligence
↓
Hypothesis Generation
```

مش لازم تعملها كاملة من أول يوم، لكن architecture لازم تستوعبها.

---

# 16. COVERAGE ENGINE

دي إضافة مهمة جدًا من المصادر السابقة:

```text
Endpoint × Parameter × Vulnerability Class
```

مثلاً:

|Endpoint|Param|XSS|IDOR|SQLi|SSRF|
|---|---|---|---|---|---|
|`/orders`|`id`|✓|✓|?|-|
|`/search`|`q`|?|-|✓|-|
|`/fetch`|`url`|-|-|-|✓|

ده يمنع مشكلة:

> "الـAgent اشتغل كتير"

من غير ما نعرف:

> "اشتغل على إيه؟ وساب إيه؟"

وفكرة coverage matrix ظهرت بوضوح في المصدر الخاص بالـAI-assisted hunting.

---

# 17. RUN COMPARISON

بسبب الـnon-determinism:

```text
Same target
Same objective
Run 1
Run 2
Run 3
```

قد يعطي نتائج مختلفة. صاحب البوست اقترح تشغيل نفس الـTarget أكثر من مرة لملاحظة الـmisses الناتجة عن طبيعة الـLLM غير الحتمية.

إذن نحتاج:

```text
RUN LOG
↓
What was tested?
What was missed?
What was found?
What differed?
```

لكن **دي مرحلة لاحقة**، ليست أول feature.

---

# 18. SPECIALIZED DETECTORS

دي الفكرة اللي ظهرِت في مشروع الـXSS.

مش لازم كل شيء يكون Agent ذكي.

ممكن:

```text
Specialized Detector
        ↓
Specific vulnerability
        ↓
Deterministic proof
```

مثلاً:

```text
XSS Detector
SSRF Detector
Open Redirect Detector
```

والـAgent يستخدمهم كأدوات.

يعني:

```text
Agent
  ↓
"Test XSS"
  ↓
XSS detector
  ↓
Browser proof
  ↓
Result
```

وده أفضل من إن الـLLM نفسه يكون مسؤول عن الحكم النهائي.

---

# 19. MCP SECURITY

دي موجودة في المصادر، لكن **ليست جزءًا من أول MVP**.

بما إن الـAgent بيتعامل مع:

```text
MCP
Tools
Plugins
Browser
Burp
Files
```

يبقى فيه Attack Surface مستقل:

```text
Prompt Injection
Indirect Prompt Injection
Tool Poisoning
Tool Response Attacks
Resource Access
Plugin Trust
Unicode / Smuggling
```

المصدر ده كان واضح جدًا إن الـMCP نفسه جزء من الـattack surface.

لكن هنحطها في:

```text
security-of-the-hunting-system/
```

مش نخلطها مع الـHunting Engine.

---

# الشكل النهائي كموديولات

```text
ai-hunt/
│
├── AGENTS.md
├── orchestrator/
│   ├── planner/
│   ├── delegation/
│   ├── task_state/
│   └── routing/
│
├── skills/
│   ├── recon/
│   ├── app-mapping/
│   ├── js/
│   ├── access-control/
│   ├── xss/
│   ├── ssrf/
│   ├── sqli/
│   ├── business-logic/
│   ├── browser/
│   ├── validation/
│   └── reporting/
│
├── knowledge/
│   ├── program-intel/
│   ├── writeups/
│   ├── reports/
│   ├── research/
│   ├── primitives/
│   ├── patterns/
│   └── false-positives/
│
├── targets/
│   └── <target>/
│       ├── scope/
│       ├── recon/
│       ├── app-map/
│       ├── technologies/
│       ├── endpoints/
│       ├── parameters/
│       ├── sessions/
│       ├── notes/
│       ├── hypotheses/
│       ├── coverage/
│       ├── findings/
│       ├── reports/
│       ├── evidence/
│       ├── requests/
│       ├── responses/
│       ├── screenshots/
│       ├── logs/
│       └── state.md
│
├── runtime/
│   ├── burp/
│   ├── browser/
│   ├── http/
│   ├── oob/
│   └── scripts/
│
├── validators/
│   ├── xss/
│   ├── ssrf/
│   ├── access-control/
│   └── generic/
│
├── memory/
│   ├── false-positive-db/
│   ├── learned-patterns/
│   ├── methodology/
│   └── run-history/
│
└── system/
    ├── scope-policy/
    ├── safety/
    ├── mcp/
    └── observability/
```

---

# الحاجات اللي **مش هنبنيها الآن**

وده أهم جزء عشان ما نتحولش لمجموعة مهندسين بتكتب نظام تشغيل قبل ما تعمل أول request.

### مؤجل:

```text
❌ 20+ subagents
❌ Agent Teams
❌ Fine-tuning
❌ QLoRA
❌ 24K writeup pipeline
❌ Vector DB ضخمة
❌ Self-training
❌ Telegram bot
❌ Dashboard
❌ 24/7 autonomous hunting
❌ Automatic report submission
❌ Distributed VPS fleet
```

كل دي **امتدادات** architecture، مش foundation.

---

# V1 الفعلية التي سنبنيها

أول نسخة محتاجة فقط:

```text
        HUMAN
          │
          ▼
       TARGET
          │
          ▼
   TARGET WORKSPACE
          │
          ▼
      ORCHESTRATOR
          │
          ▼
      ONE SKILL
          │
          ▼
     BURP + BROWSER
          │
          ▼
     OBSERVATIONS
          │
          ▼
     VALIDATOR
          │
          ▼
      FINDING
          │
          ▼
   UPDATE TARGET STATE
```

**بس.**

وأنا أميل نبدأ بـ:

```text
Recon/App Mapping
        ↓
XSS
        ↓
Validation
```

لسبب محدد: الـXSS عندنا له **Proof Gate واضح جدًا** عبر Browser/JavaScript execution، وده يخلي أول نسخة قابلة للقياس بدل ما نبدأ من Business Logic التي تحتاج حكمًا بشريًا كبيرًا. فكرة فصل الـAI عن الـproof ظهرت صراحة في مشروع Live XSS.

بعد ما الـpipeline دي تشتغل:

```text
XSS
→ Access Control
→ IDOR
→ SSRF
→ SQLi
→ Business Logic
```

---

# الخلاصة النهائية

كل المحتوى اللي قرأناه تقريبًا اتجمع في **9 أفكار فقط**:

```text
1. Scope
2. Persistent Target Context
3. Focused Tasks
4. Skills
5. Orchestration / Delegation
6. Runtime Tools
7. Hypothesis → Validation
8. Structured Findings / Memory
9. Human-guided Improvement
```

والباقي كله variations وتطبيقات على التسع دول.

**فآه، أنا متفق معاك: إحنا قربنا جدًا من مرحلة "كفاية نظريات".**

من هنا أي فيديو جديد مش هيدخل الـArchitecture تلقائيًا. لازم يثبت إنه يضيف **Primitive جديد فعليًا**، وإلا يتسجل Reference فقط ونكمّل شغل.

والـMVP عندنا الآن واضح: **Target Workspace + Persistent State + Orchestrator بسيط + Skill واحدة + Burp/Browser + Validator + Evidence.**  
ده هو المكان الصح اللي نبدأ منه بدل ما نعمل Agent عنده ذاكرة الكون ثم نكتشف أنه لا يعرف أين حفظ الـscope.
