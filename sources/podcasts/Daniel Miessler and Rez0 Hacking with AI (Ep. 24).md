الفيديو ده مهم جدًا، لأنه بيربط **أغلب الخيوط اللي كنا بنجمعها في الفيديوهات السابقة**: الـ agents، الـ context، الـ embeddings، الـ recon pipelines، الـ proxy، والـ AI security نفسه.

والإضافة الأخطر هنا إننا لأول مرة عندنا بوضوح **مسارين منفصلين**:

```text
AI → يساعدنا نعمل Hacking
AI ← نحن نهجم على أنظمته
```

وده لازم يدخل في الـ architecture بتاعتنا كمسارين، مش نخلطهم في بعض.

# Video X — AI for Hacking & Hacking AI: Agents, Context, Embeddings and Tool Security

> **ملاحظة:** الـ transcript لا يذكر عنوان الحلقة بشكل واضح، لذلك ده working title وصفي، وليس عنوانًا منقولًا حرفيًا من المصدر. 

---

# 1. Main Idea

الحلقة تدور حول 4 أفكار كبيرة:

```text
1. Mechanize the methodology
2. Give AI better context
3. Turn AI into tool-using agents
4. Attack AI systems as a new attack surface
```

والفكرة الأساسية في الجزء الهجومي:

> لا تستخدم الـ LLM فقط كـ chatbot، بل كطبقة ذكاء فوق أدواتك الحالية.

وفي المقابل، عندما يكون الهدف هو **اختبار AI systems**، لا تنظر فقط إلى prompt injection. انظر إلى:

```text
Input
↓
LLM
↓
Tools / Plugins
↓
Internal Systems
↓
Sensitive Actions
```

لأن الخطورة الحقيقية تظهر عندما يكون الـ model متصلًا بأدوات ذات صلاحيات عالية. 

---

# 2. Concepts / Architecture

## 2.1 Mechanizing the Methodology

من أقدم أفكار المتحدث:

بدل أن تعتمد على tools ضخمة منفصلة، اعمل **أوامر صغيرة ذات واجهة ثابتة**.

مثال الفكرة:

```text
get_tlds
   ↓
get_subdomains
   ↓
find_open_ports
   ↓
...
```

كل command يعمل شيئًا واحدًا، ويمكن pipe الناتج إلى command آخر.

والـ backend tool يمكن تبديله بدون تغيير الـ workflow:

```text
find_open_ports
   ├── nmap
   ├── naabu
   └── masscan
```

يعني أنت تفصل:

```text
WHAT I WANT
```

عن:

```text
HOW IT IS IMPLEMENTED
```



---

# 3. Why This Matters For Our Architecture

ده متوافق جدًا مع اللي جمعناه سابقًا:

```text
Skill
↓
Stable Interface
↓
Deterministic Tool
```

يعني بدل ما الـ Agent يعرف تفاصيل كل tool:

```text
"شغّل naabu بهذه الـ flags"
```

يمكن أن يعرف:

```text
"اكتشف open ports"
```

والـ implementation يتغير تحتها.

ده يخلي الـ methodology نفسها أكثر ثباتًا.

---

# 4. Clean Data Before Giving It To AI

نقطة مهمة جدًا في الحلقة:

**AI لا يحب noise.**

لو أعطيت الـ model:

```text
whole webpage
+
HTML
+
CSS
+
JS
+
ads
+
tracking
+
metadata
```

فأنت بتدفع له كمية هائلة من الـ context الذي لا يحتاجه.

الحل:

```text
Raw Input
   ↓
Cleaner / Extractor
   ↓
Relevant Data
   ↓
LLM
```

تم ذكر مثال لأداة command-line تقوم بالبحث داخل codebase ثم تعيد **الدالة المطلوبة بدل المشروع كله**.



وده مهم جدًا لنا لأن:

> **Pre-processing هو جزء من AI system، وليس مجرد خطوة قبل الـ AI.**

---

# 5. Codebase Understanding

الحلقة تناقش مشكلة كبيرة:

```text
Large Codebase
↓
Function A
↓
Function B
↓
Function C
↓
...
```

لو سألت الـ AI عن function صغيرة، أحيانًا يحتاج معرفة:

* من يستدعيها؟
* ماذا تستدعي؟
* أين يدخل الـ data؟
* أين يتم sanitization؟
* أين الـ sink؟

ومن هنا ظهرت فكرة:

```text
Codebase
↓
Parse incrementally
↓
Map relationships
↓
Build compressed representation
↓
Ask LLM questions
```

والهدف هو إعطاء الـ LLM **خريطة علاقات** بدل تمرير المشروع كله في كل مرة. 

---

# 6. GPT Engineer / Incremental Understanding

تمت مناقشة مشاريع مثل GPT Engineer كأمثلة على نمط:

```text
Understand a little
↓
Save what matters
↓
Understand next piece
↓
Connect pieces
↓
Continue
```

يعني بدل:

```text
Feed 100% of project
→ hope model understands
```

نعمل:

```text
File A
↓
Summary / relation

File B
↓
Summary / relation

File C
↓
Summary / relation

Global map
```

وده قريب جدًا من مفهوم:

> **Incremental context construction**

---

# 7. Source → Sink Mapping

من أقوى الأفكار المطروحة:

بدل أن تسأل:

> "هل يوجد XSS؟"

اعمل:

```text
Sources
   ↓
Data Flow
   ↓
Transformations
   ↓
Sanitization
   ↓
Sinks
```

ثم اسأل:

```text
هل يوجد path
من source
إلى sink
بدون protection مناسبة؟
```

وهذا يمكن تطبيقه على:

* XSS
* SQL injection
* command injection
* SSRF
* path traversal
* template injection

لكن **المصدر يطرحها كفكرة/اتجاه**، وليس implementation مكتملًا. 

---

# 8. Documentation as Attack Surface Intelligence

فكرة قوية جدًا في الحلقة:

بدل أن تقرأ documentation يدويًا بالكامل:

```text
Documentation
↓
AI
↓
"ما الذي يقول النظام إنني لا أستطيع فعله؟"
↓
Test those restrictions
```

مثل:

```text
"You cannot X"
"You need permission Y"
"Only admins can Z"
"This endpoint is internal"
```

ثم يصبح:

```text
Documentation
↓
Declared constraints
↓
Test constraints
↓
Look for violations
```

وده قريب جدًا من منهجية:

**"Read the manual, then attack the assumptions in the manual."**

---

# 9. Smart Brute Force / Intelligent Enumeration

بدل:

```text
/admin
/api
/test
/dev
...
```

استخدم السياق الموجود:

```text
Existing endpoints
+
Documentation
+
JS
+
Naming patterns
+
Observed routes
+
Application vocabulary
```

ثم:

```text
Predict likely routes
↓
Generate candidates
↓
Test
↓
Learn
↓
Generate more
```

يعني fuzzing يتحول من:

```text
dictionary attack
```

إلى:

```text
context-driven enumeration
```

---

# 10. Embeddings + Burp/Caido Data

دي من أهم أجزاء الفيديو بالنسبة لنا.

الفكرة:

```text
Burp / Caido History
        ↓
        Loader
        ↓
   Normalize / Chunk
        ↓
     Embeddings
        ↓
     Retrieval
        ↓
        LLM
```

والاقتراح كان إنشاء **Document Loader خاص بملفات Burp أو Caido**.

مثلاً تسأل:

```text
"هات request الـ authentication"

"هات request بيجيب user object"

"هات request بيجيب organization"

"هات requests الخاصة بالـ admin"
```

والـ system يحاول العثور عليها بناءً على السياق، وليس فقط exact matching. 

---

# 11. The "Burp History as Knowledge Base" Idea

وده مهم جدًا بالنسبة لمشروعنا.

بدل أن يكون:

```text
Burp History
= passive traffic
```

يصبح:

```text
Burp History
= target knowledge
```

ثم يمكن ربطه مع:

```text
Live traffic
+
Documentation
+
JS
+
Swagger
+
Previous findings
+
Human notes
```

فتصبح لدينا:

```text
TARGET KNOWLEDGE GRAPH / CONTEXT STORE
```

حتى لو لم نستخدم Graph database فعلية.

---

# 12. Authentication Context

من الأمثلة المهمة:

إذا كان الـ Agent يبني request جديدة، لا نريده يخترع:

```text
Cookie
Authorization
User ID
```

بل يستخدم الموجود في الـ history.

مثلًا:

```text
User A
→ user_id = ...

User B
→ user_id = ...

Admin
→ user_id = ...
```

ثم:

```text
Build request
+
reuse correct identity
```

وده يربط مباشرة بفكرة **ID/identity ledger** التي ظهرت عندنا سابقًا.

---

# 13. Human Notes as Live Context

واحدة من أحلى الأفكار في الحلقة:

أثناء الـ hunting، تكتب ملاحظة مثل:

```text
user IDs starting with 1000 are privileged
```

فتصبح المعلومة جزءًا من الـ context.

ثم الـ system يستطيع إعادة تفسير البيانات القديمة بناءً على المعلومة الجديدة.

يعني:

```text
Old observations
       +
New human note
       ↓
Re-evaluate context
       ↓
New hypotheses
```



وده **feedback loop حقيقي**، مش مجرد memory file.

---

# 14. Meta-Prompter

الحلقة تتحدث عن طبقة اسمها تقريبًا:

```text
USER PROMPT
   ↓
META-PROMPTER
   ↓
ENHANCED PROMPT
   ↓
LLM
   ↓
FINAL ANSWER
```

وظيفتها:

* توضيح السؤال
* إضافة السياق المطلوب
* إضافة domain terminology
* تحديد الخبرة المطلوبة
* تحديد output format
* تحسين specificity

فبدل:

```text
write python parser
```

يمكن أن يتحول إلى طلب أكثر تحديدًا حول:

* الجودة
* style
* efficiency
* requirements
* expected output



---

# 15. Step-by-Step Reasoning + Examples

المشاركون يناقشون أن:

* إعطاء أمثلة لما تريده مهم.
* وصف الـ desired behavior مهم.
* بعض تقنيات prompting التي نوقشت وقت تسجيل الحلقة تضمنت step-by-step reasoning وTree of Thought.

### المهم لنا

**Tree of Thought ليس مجرد prompt.**

المصدر نفسه يوضح أنه يحتاج wrapper/system يقوم بشيء مثل:

```text
Generate Path A
Generate Path B
Generate Path C
        ↓
Evaluate
        ↓
Discard bad path
        ↓
Continue promising path
```

وده مختلف عن:

```text
"think harder"
```

لأنه architecture خارجية، وليس مجرد instruction. 

---

# 16. Model Routing

ظهر هنا concept مهم جدًا:

```text
User request
      ↓
Agent / Router
      ↓
Strong Cloud Model
      ↓
if unsuitable / unavailable
      ↓
Local Model
```

أحد المشاركين ذكر architecture فيها Agent أمامي يستخدم Model أقوى كخيار أول ثم يمرر بعض المهام إلى local model، خصوصًا عندما تكون هناك قيود على المهام الأمنية في النماذج السحابية.

---

# 17. "Reverse" API

دي فكرة رائعة جدًا.

تم وصف API اسمها `reverse` تقريبًا تقوم بأخذ input غامض وتحاول إرجاعه إلى representation مفهومة.

أمثلة ناقشوها:

```text
JWT
↓
decode / inspect
```

أو:

```text
encoded data
↓
detect representation
↓
transform
```

وتمت الإشارة إلى أنه يمكن أن يعمل مع:

* encodings
* known hashes/representations
* JWT-like structures
* transformations

الفكرة المهمة ليست "كسر hash"، بل:

> **AI-powered data transformation / decoding layer.**

---

# 18. CyberChef / Burp Decoder as an Agent

الحلقة تعطي مثالًا واضحًا جدًا:

بدل workflow يدوي:

```text
URL decode
↓
Base64 decode
↓
modify
↓
Base64 encode
↓
URL encode
```

يصبح:

```text
Input
↓
AI transformation engine
↓
desired output format
```

وده في architecture عندنا يمكن أن يكون:

```text
TRANSFORMATION SKILL
```

وليس Agent كامل.

---

# 19. Hacking AI: Prompt Injection

النصف الآخر من الحلقة ينتقل إلى:

> كيف نهاجم AI systems؟

أول vector واضح:

```text
Prompt Injection
```

الـ participants يقترحون البحث عن:

* prompt manipulation
* special tokens / formatting
* jailbreak behaviors
* system prompt leakage
* hidden functionality
* tool/plugin access

والهدف ليس فقط:

> "أخليه يقول شيئًا ممنوعًا."

بل:

```text
Prompt Injection
↓
Understand System
↓
Discover capabilities
↓
Discover tools
↓
Discover internal data
↓
Reach impactful action
```

---

# 20. System Prompt as Intelligence Source

إذا تمكن الباحث من التأثير على الـ model، يمكن أن يسأل عن:

```text
What are you not supposed to do?
What tools do you have?
What plugins are available?
What internal systems can you access?
```

الفكرة هنا:

**System prompt ليس مجرد prompt.**

يمكن أن يكون:

```text
Attack Surface Documentation
```

---

# 21. Indirect Prompt Injection

ودي أهم من مجرد direct injection.

المصدر يعطي مثالًا منطقيًا:

```text
User
 ↓
Internal AI
 ↓
Reads error / webpage / external content
 ↓
Attacker-controlled content
 ↓
"Ignore previous instructions..."
```

فالـ input للـ LLM لا يأتي فقط من user textbox.

يمكن أن يأتي من:

* webpages
* internet browsing
* uploaded files
* logs
* errors
* documents
* indexed content

وبالتالي:

```text
ANY DATA ENTERING AN LLM
= POTENTIALLY UNTRUSTED
```

والمتحدث يطرح كقاعدة هندسية قوية عدم إعطاء agent الذي يتعامل مع browsing/end-user data صلاحيات على internal/admin systems. 

---

# 22. AI Agent as an Attack Surface

الـ architecture الخطرة هي:

```text
Attacker-Controlled Input
        ↓
       LLM
        ↓
      Tools
        ↓
Internal Systems
```

مثل:

```text
LLM
 ├── Email
 ├── Calendar
 ├── Browser
 ├── Database
 ├── Files
 ├── Admin API
 └── Cloud
```

هنا الـ model ليس مجرد text generator.

هو **privileged action router**.

وده أحد أهم أفكار الحلقة كلها.

---

# 23. Tool / Plugin Security

آخر وأقوى فكرة في الفيديو تقريبًا:

الـ Agent قد يعتمد على plugins/tools خارجية.

مثال:

```text
LLM
 ↓
Plugin URL
 ↓
Tool instructions
 ↓
Action
```

المشكلة:

ماذا يحدث إذا:

* domain انتهى؟
* subdomain takeover؟
* developer أصبح malicious؟
* server تم اختراقه؟
* YAML/config تغير؟

قد يصبح:

```text
Trusted AI
 ↓
Untrusted / Modified Tool
```

والـ Agent ينفذ التعليمات الجديدة.



---

# 24. Tool Integrity Proposal

الاقتراح في الحلقة:

```text
Plugin Definition
     ↓
Hash
     ↓
Store trusted hash
     ↓
At runtime:
download plugin
     ↓
hash it
     ↓
compare
     ↓
match? → allow
different? → BLOCK / human review
```

يعني:

```text
Tool Integrity
=
Hash + Verification + Human Re-approval
```

ودي فكرة source-derived واضحة جدًا في نهاية النقاش. 

---

# 25. Problems / Lessons Learned

## Problem 1: Context Window

المشكلة المتكررة:

```text
Large JS
Large Burp log
Large PDF
Large codebase
```

لا يمكن دائمًا إدخال كل شيء دفعة واحدة.

الحل الذي يناقشه المصدر:

```text
Chunking
+
Embeddings
+
Summaries
+
Document loaders
+
Incremental understanding
```

---

## Problem 2: Embeddings لا تعني أن AI "فهم كل شيء"

إذا قسمت data إلى chunks، فالـ retrieval سيجلب chunks ذات صلة، لكن الـ model لا يرى بالضرورة كل العلاقات خارج chunk.

لذلك ظهرت فكرة:

```text
Embeddings
+
Relationships / summaries
+
Global context
```

وليس embeddings وحدها.

---

## Problem 3: AI قد يدور في loop

المثال المذكور:

```text
admin1
admin2
admin3
admin4
...
```

الـ Agent قد يدخل في fuzzing loop غبي.

الحل:

```text
iteration limits
+
loop detection
+
state
+
stop conditions
```

---

## Problem 4: AI ممكن يخليك أبطأ

مهم جدًا:

```text
Manual = 20 min
AI debugging = 2 hours
```

لأنك تحاول إجبار AI على إكمال task كان بإمكانك تنفيذه بنفسك أسرع.

الحل ليس "لا تستخدم AI".

الحل:

> **اعرف متى تسحب المهمة منك وتعملها بنفسك.**



---

# 26. Exact Things Not Specified

المصدر لا يعطي implementation كامل لـ:

* نظام الـ embeddings.
* schema محدد لـ Burp/Caido loader.
* architecture كاملة للـ code graph.
* exact meta-prompt.
* exact Tree of Thought implementation.
* exact local model routing.
* exact `reverse` API code.
* exact plugin hashing mechanism.
* exact hash algorithm.
* exact tool verification protocol.
* exact exploit methodology لـ prompt injection.
* exact AI Canary implementation.
* exact model stack المستخدم فعليًا لكل component.

كذلك بعض أرقام benchmark/model/context التي قيلت في الحوار هي **claims مرتبطة بزمن تسجيل الحلقة** وليست مواصفات حالية لنماذج اليوم، فلا ينبغي لنا نقلها كأنها حالة 2026.

---

# 27. Key Takeaways

### 1.

**Mechanize your methodology before adding AI.**

```text
stable commands
+
stable interfaces
+
replaceable backends
```

### 2.

**Clean data before sending it to the model.**

### 3.

**Build context incrementally.**

### 4.

**Use live traffic + docs + JS + Swagger + notes together.**

### 5.

**Treat human notes as active intelligence, not passive notes.**

### 6.

**Use AI to decode/reverse protocols مثل Protobuf.**

### 7.

**Coverage should become explicit state.**

### 8.

**AI security starts where LLM meets tools.**

### 9.

**Every external input into a privileged agent should be treated as untrusted.**

### 10.

**Third-party tools/plugins need integrity verification.**



---

# 28. How It Fits With Previous Videos

ودي أهم فقرة عندنا.

بعد الفيديوهات السابقة كان عندنا:

```text
HUMAN
 ↓
SCOPE
 ↓
COORDINATOR
 ↓
SOLVERS
 ↓
SKILLS
 ↓
RUNTIME
 ↓
VALIDATION
 ↓
FINDING
 ↓
REPORT
 ↓
LEARNING
```

الفيديو ده يضيف **Context Infrastructure** في المنتصف:

```text
                 COORDINATOR
                      ↓
              TARGET CONTEXT
       ┌──────────────┼──────────────┐
       ↓              ↓              ↓
    HTTP Logs        JS           Docs
       ↓              ↓              ↓
    Burp/Caido      Code Map      API Docs
       └──────────────┼──────────────┘
                      ↓
               CONTEXT ENGINE
             ┌────────┼────────┐
             ↓        ↓        ↓
         Retrieval  Notes   Embeddings
             ↓        ↓        ↓
             └────────┼────────┘
                      ↓
                   SOLVER
                      ↓
                  RUNTIME
                      ↓
                VALIDATION
```

والـ **Learning Loop** أصبح أوضح:

```text
Finding / Mistake
       ↓
Human Note
       ↓
Context Update
       ↓
Re-evaluate old/new data
       ↓
Better future decisions
```

---

# Architecture v0.5

أنا هثبت عندنا المعمارية بالشكل ده حاليًا:

```text
HUMAN
  ↓
SCOPE
  ↓
COORDINATOR
  ↓
TARGET STATE / LEDGER
  ↓
CONTEXT ENGINE
  ├── HTTP History
  ├── JS
  ├── Documentation
  ├── Swagger / API Specs
  ├── Previous Reports
  ├── Human Notes
  ├── Identities / IDs
  └── Application Metadata
  ↓
COVERAGE MAP
  ↓
OBJECTIVE SELECTION
  ↓
SOLVER
  ├── Skills
  ├── Context
  ├── Tools
  └── Iteration Budget
  ↓
ISOLATED RUNTIME
  ├── Burp / Caido
  ├── Browser
  ├── Scripts
  ├── OOB
  └── Transformation Tools
  ↓
OBSERVATIONS
  ↓
VALIDATION
  ↓
FALSE POSITIVE GATE
  ↓
EVIDENCE
  ↓
FINDING
  ↓
IMPACT / CHAINING
  ↓
REPORT
  ↓
LEARNING
  ├── Human Notes
  ├── False Positive Lessons
  ├── Skills
  ├── CLAUDE.md / AGENTS.md
  └── Target Knowledge
  ↓
IMPROVED METHODOLOGY
```

## ومسار ثاني موازٍ:

```text
AI TARGET
   ↓
INPUT
   ↓
LLM
   ↓
TOOLS / PLUGINS
   ↓
INTERNAL DATA / ACTIONS
```

وده **مش جزء من hunting pipeline نفسها**، لكنه **AI Security attack surface** لازم يبقى branch مستقل عندنا.

---

## أهم 3 إضافات من الفيديو عندنا

### **A. Context Engine**

الـ AI لا ينبغي أن يعمل على request منفردة إذا كان عندنا:

> history + JS + docs + identities + notes + previous findings.

### **B. Coverage Engine**

لا نريد Agent يقول:

> "خلصت."

نريد system يعرف:

```text
what exists
what was tested
what wasn't tested
under which identity
against which vulnerability class
```

### **C. AI Attack Surface**

أي Agent عنده:

```text
user input
+
internet access
+
tools
+
internal permissions
```

هو هدف أمني مستقل بحد ذاته.

وده بيكمل آخر فيديوين بشكل ممتاز، لأننا انتقلنا من **"كيف نبني الـ hunter"** إلى **"كيف نبني الـ context الذي يعيش عليه hunter"** ثم بدأنا نرى **"كيف نهاجم الـ AI agents أنفسهم"**. 
