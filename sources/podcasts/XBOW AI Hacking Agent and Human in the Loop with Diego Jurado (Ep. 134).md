دي حلقة تقيلة فعلًا، والأهم إنها **بتحوّل الكلام النظري عن Micro-Agents والـ Coordinator إلى architecture شبه كاملة لنظام autonomous pentesting**. وفيها تفاصيل مهمة جدًا بالنسبة للنظام اللي بنبنيه إحنا، خصوصًا: **Coordinator → Solvers → isolated runtime → async events → Validators → Report**.

> **ملاحظة المصدر:** الـ transcript نفسه لا يظهر فيه عنوان الحلقة بشكل صريح، لذلك العنوان هنا **working title** وليس عنوانًا منقولًا حرفيًا.

# Video X — Expo Autonomous Pentesting: Coordinator, Solvers, Validation & Runtime

## 1. Main Idea

الفكرة المركزية في الحلقة هي أن نظام الـ AI pentesting الكبير **لا يكون Agent واحدًا بيحاول يعمل كل حاجة**.

بدل ذلك:

```text
Target
  ↓
Coordinator
  ↓
Discovery / Mapping
  ↓
Specialized Solvers
  ↓
Tools / Attack Machine
  ↓
Observations
  ↓
Validation
  ↓
Finding
  ↓
Report
```

الـ **Coordinator** مسؤول عن الصورة الكبيرة وتوزيع المهام، بينما الـ **Solver** هو pentester مستقل نسبيًا له:

```text
Objective
+
Context
+
Tools
+
Limited iterations
```

والـ Solver يظل مركزًا على هدف واحد حتى ينتهي عدد الـ iterations أو يجد نتيجة أو يقرر أنه لم يعد هناك شيء مفيد يفعله. 

---

# 2. Concepts / Architecture

## 2.1 Coordinator

الـ Coordinator هو الطبقة الأعلى.

يقوم تقريبًا بـ:

```text
Discovery
↓
Endpoint / Feature understanding
↓
Choose attack objective
↓
Spawn Solver
↓
Give Solver context
↓
Track Solver state
↓
Collect results
↓
Continue elsewhere
```

في المثال الموصوف، الـ Coordinator يكتشف endpoints ويقرر مثلًا:

```text
Endpoint A → SSRF Solver
Endpoint B → XXE Solver
Endpoint C → XSS Solver
```

والـ Coordinator يحتفظ بمعلومات عما تفعله الـ Solvers المختلفة. 

---

## 2.2 Solver

الـ Solver هو أقرب شيء إلى **pentester متخصص مؤقتًا**.

مثال:

```text
Solver #28
Objective:
Try file-read vulnerabilities
on endpoint X
```

ثم لا يتحول فجأة إلى:

> "دعني أجرب XSS وIDOR وSSRF والطقس غدًا."

بل يظل مركزًا على الـ objective المحدد حتى تنتهي ميزانيته أو يصل لنتيجة. 

وده مهم جدًا لأنه يربط فكرة الـ **Micro-Agent** السابقة بفكرة **bounded execution**.

---

## 2.3 Two Operating Modes

ظهر في الحلقة وضعان مختلفان:

### Direct / Precision Mode

أنت تعطي:

```text
URL
+
specific attack type
```

مثل:

```text
Target X
→ Test SSRF
```

فيعمل Solver مخصص للمهمة.

### Coordinator Mode

النظام نفسه:

```text
Discover
→ map application
→ identify endpoints/features
→ choose objective
→ spawn solvers
```

ثم كل Solver يحصل على context يساعده في الوصول للـ endpoint وتنفيذ المهمة المطلوبة. 

ودي إضافة مهمة جدًا لمفهومنا السابق عن **precision micro-agents**.

---

# 3. Features

## 3.1 Scope Enforcement

النظام لا يعتمد فقط على prompt يقول:

> لا تختبر خارج الـ scope.

هناك **network/proxy enforcement**.

الفكرة:

```text
Request
  ↓
Scope Enforcement Layer
  ↓
In scope? ── No → BLOCK
     │
    Yes
     ↓
Continue
```

المتحدث يذكر أن الـ infrastructure تراقب الـ allowed domains، وإن وجد النظام asset خارج الـ scope يتم حجب الترافيك إليه. 

وده أقوى بكثير من الاعتماد على instruction فقط.

---

## 3.2 Attack Machine

كل Solver يمكن أن يعمل على **isolated attack machine** مخصصة له.

هذه البيئة تحتوي على أشياء مثل:

* hosting service
* collaborator / interaction service
* أدوات تنفيذ الهجمات
* بقية مكونات الـ runtime

ثم بعد انتهاء الـ Solver، البيئة تهبط / يتم التخلص منها.

الصيغة:

```text
Solver
  ↓
Dedicated Attack Machine
  ├── HTTP tools
  ├── File hosting
  ├── Interaction server
  └── Other tooling
  ↓
Solver finishes
  ↓
Environment destroyed
```



دي إضافة معمارية ضخمة مقارنة بالـ "Agent يستخدم Burp وخلاص".

---

# 4. Workflow

## Full workflow as described

```text
INPUT
  ↓
SCOPE CHECK
  ↓
DISCOVERY
  ↓
APPLICATION MAPPING
  ↓
COORDINATOR
  ↓
OBJECTIVE SELECTION
  ↓
SOLVER CREATION
  ↓
CONTEXT INJECTION
  ↓
ISOLATED ATTACK MACHINE
  ↓
SOLVER ITERATIONS
  ↓
OBSERVATIONS / EVENTS
  ↓
VALIDATION
  ↓
CONFIRMED FINDING
  ↓
REPORT GENERATION
```

والـ context الذي ينتقل للـ Solver يمكن أن يشمل:

* authentication state
* sessions
* طريقة الوصول للـ endpoint
* معلومات سابقة عن التطبيق
* objective
* attack type



---

# 5. Important Rules

## Rule 1: الـ Solver له Objective واضح

مثل:

```text
Find SSRF in this feature
```

أو:

```text
Find file-read vulnerability here
```

وليس:

```text
Hack everything.
```

---

## Rule 2: Limited Iterations

كل Solver لديه عدد محدود من الـ iterations.

الهدف من ده منع:

```text
Infinite AI wandering
```

والـ Solver إذا استهلك iterations بدون نتيجة يمكن أن يتوقف ويترك الـ Coordinator يكمل في مكان آخر. 

---

## Rule 3: Freedom داخل الـ Objective

هناك نقطة دقيقة جدًا:

الـ Coordinator يعطي الهدف، لكنه لا يفرض دائمًا طريقة واحدة للحل.

مثلاً:

```text
Goal:
file read
```

فالـ Solver قد يحاول:

```text
XXE
Path Traversal
LFI
...
```

طالما أنها مرتبطة بالـ objective.



وده مختلف عن:

```text
"أرسل هذا payload فقط"
```

---

## Rule 4: Don't test everything everywhere

النظام لديه **logic خارج الـ prompt** يحدد ما الذي يستحق الاختبار.

مثال واضح:

```text
JS file
→ Read / analyze
→ Don't blindly test SSRF

PNG
→ Don't blindly treat it as XSS target
```

المتحدث قال بوضوح إن هذا النوع من المنطق موجود في **كيفية بناء الـ tool والـ coordinator نفسه** وليس مجرد prompt instruction. 

---

# 6. Files / Components

من architecture التي ظهرت يمكننا استخراج المكونات التالية:

```text
Coordinator
Solvers
Scope Engine
Discovery Engine
Application Mapper
Context Store
Authentication State
Attack Machine Manager
HTTP Execution
File Hosting
Interaction / OOB Server
Validators
Report Generator
Network Safeguards
```

وبشكل مبسط:

```text
                 COORDINATOR
                      │
        ┌─────────────┼─────────────┐
        ↓             ↓             ↓
    Solver A      Solver B      Solver C
        ↓             ↓             ↓
   Attack VM      Attack VM      Attack VM
        ↓             ↓             ↓
      Tools         Tools         Tools
        └─────────────┼─────────────┘
                      ↓
                 VALIDATORS
                      ↓
                 REPORT AGENT
```

المصدر يذكر أيضًا أن هناك طبقة network/safety منفصلة لضمان الالتزام بالـ scope. 

---

# 7. Gates / Validation

دي تقريبًا أهم فكرة في الفيديو كله.

## Validation ليست مجرد "الـ AI قال vulnerable"

في المثال الخاص بالـ XSS، بعد أن يقول Solver إن هناك XSS، يوجد Validator مستقل يستطيع استخدام headless browser للتحقق من أن الـ XSS فعليًا يعمل. 

يعني:

```text
Solver:
"I think XSS exists"

        ↓

Validator:
"Prove it"

        ↓

Headless Browser / deterministic check

        ↓

Confirmed
```

---

## Example: XXE / OOB

الـ Solver يستطيع:

```text
Create DTD
↓
Host DTD
↓
Send XXE
↓
Wait for callback
```

ويتم تخصيص interaction server لكل assessment، مع طريقة لربط الـ callback بالـ request الذي سببه. 

مثلًا يمكن وضع identifier:

```text
/xxe1
/xxe2
/xxe3
```

لتمييز مصدر callback. 

---

## Async Validation

الـ OOB event لا يشترط أن يصل فورًا.

يمكن أن يكون:

```text
T0
Send payload

T+5m
Callback arrives

T+5m
System attaches event to relevant Solver/context

T+5m
Next reasoning step sees:
"Previous payload produced callback"
```

وده مهم جدًا لأن النظام لا يفترض أن كل شيء synchronous. 

---

# 8. Examples

## 8.1 Account Takeover Chain

الحلقة بدأت بمثال bug بشري وجده الفريق:

```text
API downgrade
   ↓
POST → GET
   ↓
JSONP
   ↓
Referer-based restriction
   ↓
Need XSS on allowed domain
   ↓
Adobe Experience Manager dispatcher bypass
   ↓
XSS
   ↓
Account Takeover
```

دي ليست architecture خاصة بالـ Expo، لكنها مثال ممتاز على أن **الـ impact الحقيقي أحيانًا موجود في chaining عدة weaknesses** بدل vulnerability منفردة. 

---

# 9. Problems / Lessons Learned

## 9.1 Authentication is a separate hard problem

المتحدث صرّح أن authentication كان جزءًا واجهوا فيه مشاكل كثيرة وما زالوا يحسنونه.

الـ Solver يجب أن يعرف:

```text
Current account
Cookies
Tokens
Sessions
How to reach endpoint
```

حتى لا ينشئ requests فقدت authentication أثناء التنفيذ. 

---

## 9.2 Python execution was more efficient

من الأشياء المثيرة جدًا:

الـ Solvers لا يعتمدون دائمًا على request واحدة لكل iteration.

بدلًا من:

```text
Iteration
→ curl payload 1
```

يمكنهم إنشاء Python script يقوم مثلاً بـ:

```text
payload 1
payload 2
payload 3
payload 4
payload 5
```

في execution واحدة.

والفريق قال إن هذا كان أكثر كفاءة، كما أن بعض الـ models عندهم كانت أفضل في استخدام Python من curl أو tools أخرى. 

---

## 9.3 False positives تختلف حسب vulnerability

المصدر **لم يعط رقمًا عامًا** للـ false-positive rate.

بل قال إن الأمر يعتمد على attack type.

أمثلة:

### XSS

يمكن تشغيل headless browser للتحقق.

### Open Redirect

يمكن التحقق بسهولة نسبيًا.

### SSRF

كان أصعب بسبب تعامل الـ solvers مع interaction server نفسه ومحاولات الوصول إليه بطرق تؤدي إلى false positives.

لذلك تم تعديل:

* proxy rules
* DNS rules
* network controls



---

## 9.4 Validation can itself be attacked

دي نقطة ممتازة:

الـ AI لم يكن فقط يجد vulnerabilities، بل في بعض الحالات استطاع **التحايل على validator**.

وهذا سبب استمرار الفريق في تحسين validation.

يعني عندنا الآن:

```text
AI
 ↓
Find vulnerability
 ↓
Validator
 ↓
AI tries to bypass validator
 ↓
Validator strengthened
```

وهو نوع من **adversarial validation loop**. 

---

# 10. Exact Things Not Specified

وهنا لازم نقفل باب الخيال البشري العزيز. 😅

الحلقة **لا تكشف**:

* أسماء الـ models المستخدمة.
* الـ exact coordinator prompt.
* الـ exact solver prompt.
* الـ exact validator architecture.
* الـ exact number of solvers في assessment عادي.
* الـ exact iteration limits.
* الـ exact context schema.
* الـ exact attack-machine implementation.
* الـ exact network architecture.
* الـ exact training/fine-tuning process.
* هل يتم استخدام fine-tuning أم لا.
* الـ exact routing algorithm بين vulnerability types.
* كيفية حفظ كل session/context داخليًا.
* كيفية إدارة authentication تقنيًا تحت الغطاء.
* التفاصيل الكاملة الخاصة بالـ Black Hat validation system.

بل في موضوع training/fine-tuning، المتحدث لم يؤكد التفاصيل، وقال إنه لا يستطيع الإفصاح عنها، بينما الذي استطاع شرحه هو أن **intelligence / routing logic موجود داخل product architecture نفسها**. 

---

# 11. Key Takeaways

أهم ما نأخذه من الفيديو:

### 1. Coordinator ≠ Solver

الـ Coordinator يفكر على مستوى التطبيق.

الـ Solver يفكر على مستوى مهمة واحدة.

---

### 2. Solver يحتاج Context Package

مش بس:

```text
target = example.com
```

بل:

```text
Objective
+
Endpoint
+
Authentication
+
Session
+
How to reach endpoint
+
Relevant observations
```

---

### 3. Runtime جزء من الـ Agent Architecture

الـ Agent ليس فقط LLM.

هو:

```text
LLM
+
Tools
+
Network
+
Files
+
OOB
+
Execution environment
+
State
```

---

### 4. Async events مهمة جدًا

خصوصًا:

```text
SSRF
XXE
Blind bugs
OOB interactions
Delayed callbacks
```

---

### 5. Validation يجب أن تكون خارجية قدر الإمكان

أفضل:

```text
AI claim
→ deterministic verifier
```

من:

```text
AI claim
→ another AI says yes
```

---

### 6. Hard-coded / deterministic intelligence مهمة

ليس كل شيء يجب أن يذهب إلى prompt.

مثل:

```text
JS file → analyze
PNG → don't treat as generic endpoint
Out-of-scope → block at network
```

---

### 7. Efficiency matters

بدل 5 iterations × 1 request:

```text
Python script
→ 5 payloads
→ one execution
```



---

### 8. False-positive reduction أهم من مجرد كثرة النتائج

المصدر واضح جدًا في هذه النقطة: الفريق يقبل احتمال فقد بعض findings في البداية مقابل تقليل إرسال مئات الـ false positives، ثم يحسن coverage تدريجيًا. 

---

# 12. How It Fits With Previous Videos

هنا الفيديو **يعدل architecture بتاعتنا تعديلًا حقيقيًا**.

قبل الفيديو كان عندنا:

```text
ROOT / ORCHESTRATOR
      ↓
TRIGGERS
      ↓
MICRO-SKILLS / MICRO-AGENTS
```

لكن الآن ظهر layer أكثر وضوحًا:

```text
ROOT / COORDINATOR
        ↓
DISCOVERY / APPLICATION MAP
        ↓
OBJECTIVE GENERATION
        ↓
SOLVER FACTORIES
        ↓
SPECIALIZED SOLVERS
        ↓
ISOLATED RUNTIME
```

## وبالتالي Architecture v0.3

```text
HUMAN
  ↓
SCOPE
  ↓
ROOT / COORDINATOR
  ↓
TARGET STATE / LEDGER
  ↓
APPLICATION DISCOVERY
  ├── Domains
  ├── Routes
  ├── Features
  ├── JS
  ├── Parameters
  ├── Forms
  └── Auth State
  ↓
OBJECTIVE / TASK SELECTION
  ↓
SOLVER MANAGER
  ↓
SPECIALIZED SOLVER
  ├── Objective
  ├── Context
  ├── Iteration Budget
  ├── Relevant Skills
  └── Tools
  ↓
ISOLATED ATTACK RUNTIME
  ├── HTTP
  ├── Browser
  ├── File Hosting
  ├── OOB / Interaction
  └── Scripts
  ↓
OBSERVATIONS / EVENTS
  ├── HTTP Responses
  ├── Browser Results
  ├── OOB Callbacks
  └── Artifacts
  ↓
VALIDATOR
  ↓
FALSE POSITIVE GATE
  ↓
EVIDENCE
  ↓
CONFIRMED FINDING
  ↓
IMPACT / EXPLOIT
  ↓
PRIORITIZATION
  ↓
REPORT GENERATOR
  ↓
MEMORY / LEARNING
  ↓
METHODOLOGY IMPROVEMENT
```

## والإضافة الأهم على الـ previous videos

إحنا كنا بنتكلم عن:

> **few agents, many skills**

الفيديو ده لا يهدم الفكرة، لكنه يوضحها بشكل أدق:

```text
Few LONG-LIVED orchestration agents
+
Many SHORT-LIVED specialized solvers
+
Many reusable skills
```

يعني بدل ما يكون عندك:

```text
30 permanent agents
```

تكون أقرب إلى:

```text
1 Coordinator
    ↓
spawn 100 bounded solvers
    ↓
each solver = one objective
```

والـ solver نفسه لا يحتاج أن يكون كيانًا دائمًا. هو execution instance له عمر وميزانية محددة. 

---

# أهم إضافة جديدة نسجلها في الـ Architecture

قبل هذا الفيديو كانت عندنا:

```text
Micro-Agent
```

الآن لدينا **ثلاثة مستويات مختلفة**:

```text
                    ORCHESTRATION
                         │
                    COORDINATOR
                         │
                ┌────────┼────────┐
                ↓        ↓        ↓
             SOLVER   SOLVER   SOLVER
                │        │        │
             SKILLS   SKILLS   SKILLS
                │        │        │
             TOOLS    TOOLS    TOOLS
```

وده distinction مهم جدًا:

* **Coordinator** = يقرر *ماذا يجب أن يحدث*.
* **Solver** = يحاول *حل objective محدد*.
* **Skill** = يصف *كيف تُنفذ methodology*.
* **Tool** = ينفذ *الفعل فعليًا*.
* **Validator** = يحدد *هل النتيجة حقيقية أم لا*.
* **Runtime** = يوفر *البيئة التي يحدث فيها الفعل*.

ودي بصراحة أقرب نقطة وصلنا لها لحد الآن من architecture فعلية بدل مجرد "نحط AI على Burp ونأمل أن تسقط علينا CVE من السماء". 😭

