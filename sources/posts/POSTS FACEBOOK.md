ركزت فيه، ومش هخلطه تلقائيًا مع الفيديوهات السابقة كأن كل الكلام حقيقة واحدة. ده **Post / تجربة شخصية** وهنفصله عن الـ source material التاني.

أهم الحاجات اللي سجلتها منه:

- **Automated Recon loop:** subdomain enumeration من مصادر متعددة → dedup → DB → scheduled re-fetch → مقارنة الجديد بالقديم → إرسال الـ fresh assets للـ hunting.
    
- **Subdomain change monitoring:** تخزين `status-code / size / tech / title` ثم إعادة الفحص دوريًا لاكتشاف تغيّر الـ content أو رجوع subdomain للحياة.
    
- **URL collection:** جمع URLs من الـ live subs باستخدام `katana` و`wayback` ثم تخزين ومراقبة الجديد.
    
- **Agent كـ filtering layer:** لما عدد الـ targets/changes يكبر، الـ Agent يفلتر الـ promising assets بدل ما الإنسان يراجع كل شيء.
    
- **Skills تتطور من التجربة:** صاحب الـ post يقول إنه استخدم Agents + Skills لفترة، والـ skills نفسها كانت بتتعدل مع الوقت بناءً على النتائج.
    
- **Human-in-the-loop مهم جدًا:** هو نفسه ذكر أن الاعتماد الكامل على الـ AI خلاه ينسى جزءًا من معرفته، وأن الـ AI مجرد مساعد وليس بديلًا. دي نقطة متوافقة جدًا مع الفيديوهات السابقة.
    
- **Context overload / context rot:** إضافة context أكثر لم تحسن كل النماذج، بل جعلت Gemini أسوأ في تجربته. والـ takeaway الذي سجله هو: **Bigger Context ≠ Smarter Agent**.
    
- **Instruction dilution:** كثرة الـ instructions ممكن تشتت الـ Agent عن المهمة الحالية. المثال الذي ذكره كان إضافة مراحل كثيرة قبل الـ hunting، ثم لما ظهرت XSS مشكوك فيها أصبح الـ Agent أقل تركيزًا عليها.
    
- **RAG / selective loading:** بدل تحميل 50 Skill، يتم تحميل الـ Skills المتعلقة بالمهمة فقط.
    

وأهم حاجة: **الأرقام والنتائج المذكورة مثل 12 bugs / 7 triage / 1 NA هي تجربة صاحب الـ post نفسه، وليست benchmark مستقل.**

هنعتبر البوست ده **مصدر إضافي** للسلسلة، خصوصًا في جزئية **automation + context management + skill loading**، ومش هعمل منه architecture جديدة إلا لما تبعت باقي الـ posts ونشوف الصورة كاملة.



أيوه، البوست ده **مهم جدًا** لأنه مش مجرد “جربت Agent وطلع Bug”، لكنه بيركز على الجزء اللي كان ناقص في البوست اللي قبله: **إدارة الـAgent نفسه، الـExecution Flow، الـDelegation، وتقليل الـContext/Token waste**. وهنفصله عن البوست السابق بدل ما نعمل سلطة معمارية من كل الكلام مرة واحدة 😭.

# Post 2 — Agent Execution, Methodology & Human-in-the-Loop

## 1. Main Idea

الفكرة الأساسية عند صاحب البوست:

> **الـAgent لو شغال من غير توجيه ومراجعة منهجية، هيستهلك فلوس، يفوّت Bugs، ويطلع Duplicates.**

وهو يشدد إن استخدام Agent في الـBug Hunting مش معناه إنك ترمي عليه Target وتستنى النتيجة، لكن لازم تكون فاهم:

- الـFlow بتاعه
    
- الـOrchestrator
    
- هو بيمر على إيه
    
- إيه اللي فاته
    
- وإزاي تقارن شغله بالـManual Hunting
    

وده مذكور بوضوح في أول جزء من البوست.

---

# 2. أهم نقطة في البوست: لا تعتمد على Skills جاهزة فقط

صاحب البوست بيقول إنك ماينفعش تجمع شوية Skills من GitHub وتعتبرهم تلقائيًا “الـMethodology بتاعتك”.

المهم عنده هو إنك **تتابع Execution Flow من أوله لآخره** وتاخد Notes عن:

- إيه اللي اتعمل
    
- إيه اللي اتفوت
    
- فين حصل Dismiss
    
- ليه حصل Miss
    
- وإزاي سلوك الـAgent اختلف عن الـManual Hunting
    

وكمان يشترط وجود **Methodology خاصة بيك** تساعد الـAgent على اكتشاف:

- Scenarios جديدة
    
- Anomalous behavior
    
- حالات غير مباشرة
    

بدل ما يكتفي بأنه يقول:  
`Dismissed` → وينتهي الموضوع.

### دي نقطة مهمة جدًا لنا

وده متوافق جدًا مع اللي شفناه قبل كده في المصادر السابقة:

```text
Skill ≠ Methodology كاملة
```

الـSkill هنا أداة تنفيذ لمنهجية أكبر، مش بديل عنها.

---

# 3. Non-deterministic Nature of LLMs

دي من أهم أفكار البوست.

صاحب البوست يقترح إنك تشغل نفس الـAgent على نفس الـTarget أكثر من مرة.

مثاله:

```text
Run 1 → Bugs A B C
Run 2 → Bugs A D
Run 3 → Bugs B E F
```

وبالتالي لو كل Run بيطلع حاجات مختلفة، فهذا بالنسبة له دليل عملي على إن الـLLM **مش deterministic** وإنه بيفوّت حاجات في كل مرة.

وهو ذكر ملاحظة إضافية من تجربته:

> قد ينجح في Server-Side bugs ويسقط كثيرًا من Client-Side bugs.

كما ذكر مثالًا لشخص يركز فقط على P1/P2 وRCE وLLM Bugs، باعتباره استخدامًا مقصودًا للـMethodology بدل محاولة اكتشاف كل شيء.

### الاستنتاج الهندسي من كلامه

مش:

```text
One Agent → Scan Everything
```

لكن أقرب إلى:

```text
Methodology
     ↓
Focused Objective
     ↓
Repeated / Independent Runs
     ↓
Compare Coverage
     ↓
Human Review
```

وده مهم جدًا للـarchitecture بتاعتنا.

---

# 4. Human-in-the-Loop

البوست واضح جدًا في النقطة دي.

هو لا يتعامل مع الـAI كبديل للـHunter.

بالعكس، بيقول إن لازم:

```text
Agent Hunting
+
Manual Hunting
+
Comparison
```

لأنك تحتاج تعرف:

> "إيه اللي عدى من الـAgent وأنا لوحدي كنت هعمله؟"

وفي نفس الوقت:

> "إيه اللي أنا شوفته يدويًا وهو ماشفهوش؟"

وده بيخلي الـAgent نفسه مصدر Feedback لتحسين الـMethodology.

---

# 5. OpenCode + Burp + MCP

ده الجزء العملي في البوست.

الـStack عنده كان:

```text
OpenCode
   ↓
AI Model
   ↓
Bug Hunting Skills
   ↓
MCP
   ↓
Burp Suite
   ↓
HTTP History / Requests
```

هو يشرح الفرق بين:

### AI Assistant

زي ChatGPT:

```text
أنت تجيب Request
↓
ترسله للـAI
↓
AI يحلله
↓
أنت تنفذ
```

### AI Agent

```text
Agent
 ↓
Tools
 ↓
Environment
 ↓
Execution
```

يعني الـAgent يقدر يتعامل مع البيئة وينفذ الـWorkflow بدل مجرد إعطائك نصائح.

---

# 6. Burp MCP Workflow

الـWorkflow اللي وصفه:

```text
Target
   ↓
Browser browsing
   ↓
Burp captures requests
   ↓
HTTP History
   ↓
OpenCode
   ↓
XSS Skill
   ↓
Analyze requests
   ↓
Send modified requests through Burp
   ↓
Finding
   ↓
Exploit
   ↓
Report
```

هو ذكر تجربة على موقع XSS تجريبي، والـAgent استخدم الـrequests الموجودة في Burp History، استدعى Skills خاصة بالـXSS، وجرب Requests مختلفة، وبعد حوالي دقيقتين أخرج Reflected XSS مع exploit وreport.

### ودي نقطة شديدة الصلة ببيئتك الحالية

أنت أصلًا عامل:

```text
OpenCode
  ↓
Dahl
  ↓
MCP
  ↓
Burp
```

فده مش مجرد concept عندنا، ده بالضبط نفس النوع من integration اللي البوست بيستخدمه.

---

# 7. أهم تجربة: هل الـAgent يكتشف Bug أنت تعرفها بالفعل؟

دي أحسن تجربة في البوست.

صاحب البوست كان يعرف Open Redirect موجودة يدويًا، لكنه لم يخبر الـAgent بالـtrick.

عمل:

```text
Target
↓
"Find Open Redirect"
↓
Agent
↓
Discovery
↓
Validation
↓
Chain exploration
```

والـAgent قدر يوصل لنفس الثغرة تقريبًا.

وده بالنسبة له demonstration إن الـAgent قادر على تنفيذ methodology على Target حقيقي، مش مجرد Lab.

---

# 8. الـOpen Redirect Technique المذكورة في المصدر

صاحب البوست يصف Pattern يدوي:

```text
Authenticated URL
      ↓
Logout
      ↓
Open same URL
      ↓
Redirect to Login
      ↓
Observe redirect parameter
```

ثم لاحظ:

```text
redirectingParam = Base64(path)
```

وفك الـBase64 فظهر:

```text
/orders/order-list
```

بعدها جرّب استبدال القيمة بعنوان خارجي Base64-encoded، وبعد تسجيل الدخول وOTP وجد أن التطبيق يحوله إلى الـexternal URL.

المهم هنا بالنسبة لنا **مش الـpayload نفسه**، لكن طريقة التفكير:

```text
Normal authenticated flow
        ↓
Logout
        ↓
Observe unauthenticated redirect behavior
        ↓
Inspect redirect representation
        ↓
Decode hidden value
        ↓
Alter destination
        ↓
Re-authenticate
        ↓
Observe final redirect
        ↓
Assess impact / chaining
```

ودي Methodology قابلة للتحويل إلى Skill بشكل مباشر.

---

# 9. Agent ليس فقط "Bug Finder"

في التجربة دي الـAgent حاول أيضًا يشوف هل الـOpen Redirect ممكن تعمل Chain مع:

- ATO
    
- SSRF
    

حتى لو الـImpact الأعلى لم يكن موجودًا في النهاية، صاحب البوست بيعتبر المحاولة نفسها مفيدة لأنها تفتح باب للـimpact analysis بدل مجرد:

```text
Open Redirect → Found → Done
```

---

# 10. أين يرى صاحب البوست قيمة الـAgent؟

هو يرى إن الـAgent مناسب أكثر للحاجات التي:

- تحتاج Repetition
    
- تحتاج تكرار Test Patterns
    
- تحتاج Coverage أكبر
    

بحيث يوفر وقت الـHunter ليركز على:

- Business Logic
    
- Complex application behavior
    
- الأشياء التي تحتاج فهم بشري للتطبيق
    

وده فرق مهم جدًا:

```text
Agent
→ Repetition / Coverage / Pattern-based hunting

Human
→ Business Logic / Context / Weird behavior / Strategy
```

---

# 11. الـAgent لازم يسأل Human

في جزء آخر من البوست، صاحب البوست يقترح كتابة Skills متخصصة جدًا، مثل:

```text
SQLi Skill
SSRF Skill
```

ويقول إنه يفضل استخدام `AskUserQuestion` بحيث الـAgent **يسأل المستخدم أثناء اتخاذ بعض القرارات**.

يعني:

```text
Agent
 ↓
Observation
 ↓
Decision Point
 ↓
Ask Human
 ↓
Continue
```

وده بيقربنا من نموذج:

**Human-guided Agent**

وليس:

**Fully Autonomous Agent**.

---

# 12. Browser Interaction

المصدر يقترح استخدام:

- Playwright
    
- BrowserMCP
    

خصوصًا مع:

- Broken Auth
    
- IDOR
    
- SPA
    
- Browser Interaction
    

وده منطقي داخل architecture لأن بعض الثغرات لا تظهر بشكل جيد من HTTP requests فقط، بل تحتاج state وتفاعل مع المتصفح.

---

# 13. ركز على Vulnerability واحدة أو اثنتين

دي نقطة تكررت في المصادر السابقة لكن هنا اتقالت بشكل مباشر جدًا:

> ركز على ثغرة أو اثنين بالكتير.

وصاحب البوست ذكر أنه شخصيًا يركز على:

```text
SSRF
SQLi
```

بدل ما يعمل Agent يحاول يفهم الكون كله في جلسة واحدة.

### وده مرتبط مباشرة بـ:

```text
Context Rot
Instruction Dilution
Completion Bias
```

اللي شفناهم في المصادر السابقة.

---

# 14. Delegation مشكلة حقيقية

في الجزء الأخير، صاحب البوست يتكلم عن Agent عنده:

```text
Primary Agent
      ↓
23 Sub-agents
      ↓
MCPs
      ↓
Database / Knowledge
```

لكن حصلت مشكلة في:

- `Agents.md`
    
- Delegation
    
- Duplicate instructions
    
- Wrong paths
    

وكان الـPrimary Agent بدل ما يوزع المهام:

```text
Primary
  ↓
يحاول يعمل كل حاجة بنفسه
```

وده أدى إلى:

```text
Context window ↑
Token usage ↑
Efficiency ↓
```

وصاحب البوست قال إن الأداء وصل تقريبًا إلى **50–55%** في الحالة التي وصفها، ثم بعد تعديل الـKnowledge ومشاركة المعلومات والـDelegation قال إن استهلاك الـTokens تحسن والـContext Window أصبحت أقل امتلاءً.

**الأرقام هنا self-reported من صاحب البوست، وليست benchmark مستقل.**

---

# 15. Knowledge Sharing + Delegation

دي من أهم الحاجات الجديدة بالنسبة للـArchitecture بتاعتنا.

عنده تصور أقرب إلى:

```text
Primary Agent
      │
      ├── Delegate Task A
      ├── Delegate Task B
      ├── Delegate Task C
      │
      ↓
 Shared Knowledge
      ↓
Results / Summaries
      ↓
Primary Agent
```

المشكلة ليست فقط في وجود Subagents.

المشكلة في:

> **كيف الـPrimary Agent يقرر مين يعمل إيه، وإزاي المعلومات ترجع له بدون ما كل واحد يغرق الـcontext الخاص به.**

وده فرق كبير جدًا بين:

```text
23 Agents
```

و

```text
23 useful workers
```

---

# 16. Database / Learning

في الجزء الأخير، صاحب البوست يقول إنه ربط الـAgent بقاعدة بيانات، وجرب إعطاءه حوالي **1000 تقرير من GitHub**، وقال إن الـAgent:

- فهمها
    
- خزّنها في الـDatabase
    
- وبعدها قدر يستفيد من سيناريوهات لم يكن يعرفها سابقًا
    

لكن مهم جدًا نكتبها عندنا كالتالي:

> **Claim by source:** استخدم 1000 report وأفاد منها Agent.

وليس:

> "إذًا 1000 report = agent متعلم."

لأن المصدر لا يوضح بالتفصيل:

- شكل الـDatabase
    
- retrieval strategy
    
- chunking
    
- indexing
    
- embeddings أو عدمها
    
- كيف يقرر أي reports يسترجع
    
- هل المعلومات تتحدث فعليًا أم مجرد retrieval
    

ودي كلها **غير محددة في البوست**.

---

# 17. الجديد الذي أضافه البوست للـArchitecture

بعد البوست ده، في رأيي النسخة الحالية لازم تحتوي على layer إضافية واضحة:

```text
                        HUMAN
                          │
                          ▼
                        SCOPE
                          │
                          ▼
                ROOT / PRIMARY AGENT
                          │
                ┌─────────┴─────────┐
                ▼                   ▼
          TASK / OBJECTIVE     HUMAN QUESTION
                │
                ▼
          DELEGATION LAYER
                │
      ┌─────────┼─────────┐
      ▼         ▼         ▼
   Worker A  Worker B  Worker C
      │         │         │
    Skills    Skills    Skills
      │         │         │
      └─────────┼─────────┘
                ▼
             RUNTIME
       Burp / Browser / HTTP
                │
                ▼
           OBSERVATIONS
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
         KNOWLEDGE / MEMORY
                │
        ┌───────┴────────┐
        ▼                ▼
   Target State      Methodology
```

والإضافة المهمة جدًا:

```text
       ┌─────────────────────┐
       │  RUN COMPARISON     │
       │                     │
       │ Run 1 vs Run 2      │
       │ Run 2 vs Run 3      │
       │ Missed Areas        │
       │ New Findings        │
       └─────────────────────┘
                 │
                 ▼
          METHODOLOGY UPDATE
```

وده **مش مجرد inference من فراغ**، بل مبني على كلام المصدر عن تشغيل نفس الـTarget عدة مرات وملاحظة اختلاف الـoutputs والـmisses.

---

# 18. مقارنة البوست ده بالبوست السابق

الاثنين مع بعض بدأوا يرسموا صورة أوضح:

|البوست السابق|البوست الحالي|
|---|---|
|Recon automation|Execution داخل الـTarget|
|New subdomains|Agent methodology|
|Changes monitoring|Run-to-run variation|
|URL feeds|Burp integration|
|Agent filtering|Human-in-the-loop|
|Reduce false positives|Delegation|
|Context overload|Context/token optimization|
|Selective Skills|Focused vulnerability skills|
|Continuous recon|Active testing|

فالبوست الأول كان أكثر في اتجاه:

```text
Recon Pipeline
```

والبوست ده أكثر في اتجاه:

```text
Agentic Execution Pipeline
```

---

# 19. الحاجات التي **لم يحددها المصدر**

دي مهمة جدًا عشان ما نبدأش نخترع architecture من عندنا وننسبها لصاحب البوست:

### غير محدد:

- شكل الـOrchestrator الداخلي
    
- كيف تتم الـDelegation فعليًا
    
- كيف يتعامل مع فشل Subagent
    
- كيف يتم اختيار الـSkill
    
- كيف تتم الـSkill triggering
    
- كيف يتم تخزين الـFindings
    
- شكل الـDatabase
    
- Retrieval mechanism للـ1000 reports
    
- كيف يعمل الـValidator بالتفصيل
    
- كيف يقيس الـ50–55%
    
- كيف تتم مقارنة الـRuns
    
- كيف يمنع الـDuplicate findings بين الـAgents
    
- كيف يطبق Scope enforcement
    

يعني البوست يعطينا **مبادئ وتجارب** أكثر مما يعطينا implementation كامل.

---

# 20. أهم Takeaways عندنا

أنا هسجل البوست ده عندنا بالشكل ده:

```text
1. Don't outsource methodology to GitHub Skills.

2. Observe the entire execution flow.

3. Log misses, dismissals, and skipped scenarios.

4. LLM runs are non-deterministic.
   → Repeat runs and compare coverage.

5. Human + Agent > blindly autonomous Agent.

6. Narrow vulnerability focus improves usefulness.

7. Burp + MCP turns the Agent into an active tester.

8. Browser tooling matters for stateful/client-side bugs.

9. Primary Agent must delegate instead of doing everything.

10. Bad delegation increases context + token waste.

11. Knowledge sharing must be structured.

12. Persistent knowledge/database can extend agent capability.

13. Agent is especially useful for repetitive coverage.

14. Human should retain complex business-logic reasoning.

15. AskUserQuestion can preserve human guidance at decision points.
```

---

## أهم إضافة للـArchitecture بعد البوست ده

الـArchitecture عندنا لم تعد فقط:

```text
Coordinator
→ Skills
→ Runtime
→ Validator
```

لكن أصبحت أقرب إلى:

```text
Coordinator
    ↓
Task Decomposition
    ↓
Delegation
    ↓
Focused Workers
    ↓
Runtime
    ↓
Observation
    ↓
Validation
    ↓
Human Review
    ↓
Memory
    ↓
Methodology Improvement
    ↓
Next Run
```

والجزء الأخير مهم جدًا:

**الـAgent لا يجب أن يتعلم فقط "ما هي الثغرة"، بل النظام كله يجب أن يتعلم "أين ولماذا يفشل الـAgent".**

وده تقريبًا الخط الفاصل بين **Agent بيعمل شوية requests** وبين **Hunting System فعلي بيتحسن مع الوقت**.








ركزت فيه كويس، وده **مش بوست واحد أصلًا**. الملف فيه تقريبًا **4 أفكار/تجارب مختلفة**، وكل واحدة فيهم تضيف حاجة مهمة للـarchitecture اللي بنبنيها. ومش هخلط كلام أصحاب البوستات ببعض، لأن ده بالضبط النوع من اللخبطة اللي إحنا بنحاول نتفاداها.

# Post 3A — SLM متخصص في XSS بدل LLM عام

المشروع الأول فكرته مختلفة عن كل اللي شفناه قبل كده.

### الفكرة

صاحب البوست بدأ من سؤال:

> ليه أستخدم Model ضخم عندي فيه قدرات أنا مش محتاجها أصلًا؟

ففكر في:

```text
General LLM
   ↓
Knowledge / Specialization
   ↓
Small Language Model
   ↓
Application Security
   ↓
Specific vulnerability
   ↓
XSS
```

ووصل لتجربة فيها:

- **Qwen3-8B** كـbase model
    
- **RAG**
    
- **LoRA / QLoRA**
    
- **KEV-4B** كـDecision Model يقرر هل المعرفة الجديدة تستحق الدخول في عملية التعلم أم لا.
    

### لكن أهم جزء مش الـModel

المشروع بنى **Live XSS Assessment System**:

```text
Target
  ↓
Crawling
  ↓
Inputs
  ↓
Contexts
  ↓
Harmless Markers
  ↓
Context-aware Probes
  ↓
Headless Chromium
  ↓
Real Browser Execution
  ↓
Evidence
```

### والـRule هنا ممتازة جدًا

الـAI **ممنوع عليه يقول CONFIRMED** مهما كانت ثقته.

الـBrowser هو صاحب القرار النهائي.

في Reflected XSS مثلًا، لازم يحصل:

```text
Payload
   ↓
Browser
   ↓
Real JavaScript Execution
   ↓
Evidence
   ↓
CONFIRMED
```

مش:

```text
LLM: "99% XSS"
→ CONFIRMED
```

وده من أقوى الأشياء اللي شوفناها لحد دلوقتي في كل المصادر، لأنه بيفصل:

```text
AI = Hypothesis
Runtime = Proof
```

صاحب البوست ذكر **93% success rate** وحجم نموذج أقل من **50MB**، لكن دي أرقام تجريبية معلنة من صاحب المشروع نفسه، والبوست لا يوضح benchmark أو dataset أو طريقة حساب النسبة بالتفصيل.

---

# Post 3B — HunterStick

ده مشروع تاني مختلف.

صاحبه أصلًا بيقول:

> HunterStick **مش autonomous scanner** ومش recon framework.

هو عبارة عن **workspace**.

الفكرة مبنية حول:

```text
Focused Skills
+
Persistent Target Context
+
Execution Harness
+
Bounded Loops
```

ودي نقطة مهمة جدًا.

لأنها بتحل مشكلتين ذكرهم:

### 1. Focused Task

هو لاحظ إن الميزة الحقيقية للـAgent بتيجي من:

```text
Focused objective
```

وليس:

```text
"Go hunt this target"
```

ويشير لأسلوب James Kettle، حيث الـAI يحصل على **context جديد بناءً على output السابق** ويمشي خطوة بخطوة.

يعني:

```text
Task
 ↓
Observation
 ↓
New Context
 ↓
Next Task
 ↓
Observation
 ↓
...
```

ودي مهمة جدًا عندنا.

---

# 2. Persistent Target Context

صاحب البوست عنده اعتراض مهم:

> الـAI chat لا يصلح أن يكون Target Memory.

لأن الـHunter البشري مع الوقت يبني **صورة كاملة عن الـTarget**:

```text
Hosts
+
Subdomains
+
Endpoints
+
Technologies
+
Relationships
+
Behavior
+
Previous observations
```

والمشكلة أن المعلومات دي متفرقة وبتتراكم أثناء الـHunt.

عشان كده HunterStick هدفه الأساسي دعم:

```text
Persistent Target Context
```

### ودي إضافة مباشرة للـarchitecture

إحنا كنا عاملين:

```text
TARGET STATE
```

وده البوست بيدينا سبب أقوى لوجوده:

```text
Target State ≠ logs فقط

Target State =
العقل الخارجي للتارجت
```

---

# Post 3C — اقرأ كل الـWriteups والـReports قبل ما تبدأ

ده بوست مختلف تمامًا عن الأولين، وأعتقد إن ده من أهم الأجزاء بالنسبة لنا.

الفكرة:

> قبل ما تبدأ test على Program، شوف الـdisclosed writeups/reports الخاصة به.

السبب عنده:

```text
Program reports
   ↓
Understand vulnerabilities
   ↓
Understand report patterns
   ↓
Understand developer mistakes
   ↓
Look for repeated patterns
```

وهو يضع قاعدة:

> لو الـDeveloper غلط غلطة، وارد جدًا الغلطة تتكرر في مكان تاني.

دي مش قاعدة علمية عامة، لكنها **heuristic صريحة من صاحب البوست**.

---

# طريقة جمع الـReports عنده

هو يذكر 3 طرق:

### A. Search يدوي بـ dorks

مثل البحث داخل:

- HackerOne
    
- Medium
    

### B. Python script

يجمع النتائج ويرسلها إلى:

- Discord
    
- Telegram
    
- وغيرهم
    

### C. AI Search

وهو يفضل هذا في الفترة الأخيرة، مع أنه يقول إنه يسبقه أحيانًا ببحث بسيط.

---

# الجزء اللي شدني هنا فعلًا: الـAI لا يبحث فقط، بل ينظم المعرفة

هو يقول إنه يطلب من الـModel:

> يرتب الـreports والـwriteups ويديهاله في Markdown template.

وكمان يخليه يرجع للـGitHub repository كل فترة كمرجع.

يعني:

```text
Raw Reports
   ↓
Search
   ↓
Collect
   ↓
Normalize
   ↓
Structured Markdown
   ↓
Repository
   ↓
Persistent Reference
```

وده قريب جدًا من فكرة **External Memory** عندنا.

---

# الـPrompt نفسه أصبح Artifact

دي نقطة مهمة جدًا.

هو لا يكتب Prompt مرة واحدة وخلاص.

بل يعمل:

```text
AI
 ↓
"اكتبلي Prompt"
 ↓
نقاش
 ↓
تعديل
 ↓
اختبار
 ↓
Result
 ↓
Save Prompt
```

ثم إذا الـPrompt أعطى نتيجة جيدة، يحتفظ به ويستخدمه على برامج أخرى.

دي في architecture عندنا ممكن تبقى:

```text
PROMPT LIBRARY
```

لكن مش كبديل للـSkills، بل كطبقة أخف:

```text
Prompt = task framing
Skill = methodology
Target State = target-specific memory
```

---

# بعد كده: Program Knowledge Pack

ودي من أقوى أفكار البوست كله.

صاحب البوست يقترح إعطاء الـAI ملف يحتوي على **كل resources الخاصة بالبرنامج**، مثل:

- Writeups
    
- Reports
    
- Technologies
    
- Subdomain technology mapping
    

ويذكر أن جدول الـtechnologies مهم حتى في **Service Misconfiguration**.

إذن بدل:

```text
AI + Target URL
```

نصبح:

```text
AI
+
Program Knowledge Pack
```

والـKnowledge Pack يحتوي مثلًا:

```text
program/
├── reports/
├── writeups/
├── subdomains/
├── technologies.md
├── services.md
└── business.md
```

---

# والأفضل: الـAI يناقشك في الـProgram

المصدر يطرح أمثلة كثيرة، لكن جوهرها:

```text
Human ↔ AI
```

حول:

- CVEs
    
- POCs
    
- Existing reports
    
- Similar vulnerability patterns
    
- Business model
    
- Technology-specific issues
    
- Service misconfiguration
    
- Related research
    

وده مهم لأنه **Research Mode** وليس Hunt Mode.

---

# Research Mode

أعتقد لازم نفصلها صراحة في architecture:

```text
RESEARCH
   ↓
Understand Program
   ↓
Study Reports
   ↓
Study Technologies
   ↓
Study Known Bugs
   ↓
Generate Hypotheses
```

وبعدين:

```text
HUNT
   ↓
Test Hypotheses
```

يعني قبل الـscanner، عندك **Intelligence Phase**.

---

# مثال صاحب البوست عن الـInjection

هو يقترح إنك لما تجد Injection معينة، تسأل الـAI:

> هل هناك أخطاء مشابهة في البرنامج؟

ثم:

```text
Known Finding
    ↓
Pattern abstraction
    ↓
Related places
    ↓
Different injection types
    ↓
New hypotheses
```

وده بالضبط مفهوم **Pattern Generalization**.

ودي حاجة أقوى من مجرد RAG.

---

# مثال الـMail Servers

الـworkflow اللي اقترحه:

```text
Found many mail servers
       ↓
Ask AI for common misconfigurations
       ↓
Collect:
writeups
reports
posts
Reddit
videos
POCs
       ↓
Study
       ↓
Discuss
       ↓
Build workflow
       ↓
Test
```

يعني:

> **Observation → Research → Hypothesis → Workflow → Testing**

ودي بصراحة من أهم الـloops اللي ظهرت في المصادر كلها.

---

# Post 3D — Skills = Instructions للـAgent

آخر جزء في الملف ينتقل لفكرة مختلفة أكثر autonomous.

صاحب البوست يقول إن الـSkills عبارة عن:

> ملفات Instructions للـAI عشان يخليه يعمل الـHunt.

والـAgent حسب وصفه:

```text
Hunt
↓
يشتغل ساعات
↓
بدون تدخل
↓
يرجع Bugs
↓
Reports
```

وده **أكثر autonomous بكثير** من HunterStick.

فلازم مانخلطش التصميمين:

```text
HunterStick
→ Human-guided workspace

آخر جزء
→ Autonomous agent execution
```

---

# أهم شيء جديد ظهر في البوست ده

بعد كل البوستات اللي فاتت، عندنا دلوقتي 5 طبقات معرفية مختلفة:

```text
                    HUNTING SYSTEM
                         │
        ┌────────────────┼─────────────────┐
        │                │                 │
        ▼                ▼                 ▼
  METHODOLOGY      TARGET MEMORY      EXTERNAL KNOWLEDGE
        │                │                 │
     Skills         Target State       Writeups/Reports
        │                │                 │
        └────────────────┼─────────────────┘
                         ▼
                    RESEARCH LOOP
                         │
                         ▼
                    HYPOTHESIS
                         │
                         ▼
                    EXECUTION
                         │
                         ▼
                     EVIDENCE
                         │
                         ▼
                    VALIDATION
```

لكن ظهر **شيء رابع مهم جدًا**:

```text
Runtime Proof
```

خصوصًا من مشروع XSS:

```text
AI says possible
       ↓
Browser executes
       ↓
Evidence exists
       ↓
Confirmed
```

---

# والآن أعتقد عندنا فصل مهم جدًا بين نوعين من الـAI

## النوع الأول: Generative Hunting Agent

```text
LLM
+
Skills
+
Browser/Burp
+
Memory
=
Agent
```

يقدر:

- يفكر
    
- يخطط
    
- يقرر
    
- يختبر
    
- يكتب report
    

لكن عنده مشكلة:

**hallucination / nondeterminism / context problems**

---

## النوع الثاني: Specialized Detector

زي مشروع XSS:

```text
Small Model
+
Specific Task
+
Deterministic Runtime
+
Proof Gate
=
Detector
```

وده لا يحتاج بالضرورة إلى:

```text
Chat
Reasoning
Long Context
Huge Agent
```

هو فقط:

```text
INPUT
 ↓
TEST
 ↓
TRUE / FALSE
```

ودي فكرة قوية جدًا.

---

# الـArchitecture عندنا بعد البوست ده

أنا **مش هغير الـcanonical architecture بالكامل لسه**، لأن باقي البوستات لسه بتدخل علينا، لكن هضيف له هذه الطبقة المفاهيمية:

```text
                           HUMAN
                             │
                             ▼
                           SCOPE
                             │
                             ▼
                     ROOT COORDINATOR
                             │
            ┌────────────────┼────────────────┐
            ▼                ▼                ▼
        RESEARCH         TARGET STATE      METHODOLOGY
            │                │                │
  Reports/Writeups      Hosts/Techs/IDs     Skills
  CVEs/POCs             Findings/History    Playbooks
  Program Intel         Business Context   Prompts
            │                │                │
            └────────────────┼────────────────┘
                             ▼
                      HYPOTHESIS ENGINE
                             │
                    ┌────────┴─────────┐
                    ▼                  ▼
             GENERATIVE AGENT     SPECIALIZED
                    │              DETECTOR
                    │                  │
                 Burp/Browser       Runtime
                    │                  │
                    └────────┬─────────┘
                             ▼
                          EVIDENCE
                             │
                        PROOF GATE
                             │
                         VALIDATOR
                             │
                           FINDING
                             │
                    REPORT / LEARNING
```

### والأهم من كل ده:

**الـWriteups هنا لم تعد مجرد "RAG".**

هي ممكن تخدم 4 وظائف مختلفة:

```text
Writeups
├── Research
├── Pattern extraction
├── Hypothesis generation
└── Skill / Methodology improvement
```

وده فرق مهم جدًا عن فكرة:

> "عندي 24 ألف Writeup، حطيتهم في Vector DB، خلاص بقيت أذكى."

لأ. **القيمة الحقيقية في طريقة استخراج المعرفة واستخدامها.**

وبرضه خلّي بالك من تفصيلة من المصدر: كلامه عن رفع ملفات البرنامج، مناقشة الـreports، استخراج الـworkflow، ثم تعديله يدويًا، كله بيأكد إن الـAI عنده هنا **research partner** قبل ما يكون autonomous hunter.

**والجزء الأخير فقط** ينتقل إلى Agent يعمل ساعات بدون تدخل ويرجع findings/reports.

ده مهم جدًا لأننا لو خلطنا النموذجين، هنبني Frankenstein عنده 70 Skill و23 Agent وذاكرة الكون، وبعد ساعتين يقرر إن `/favicon.ico` هو RCE. മനുഷ്യர்கள் يحبون التعقيد لدرجة تثير الإعجاب أحيانًا.
