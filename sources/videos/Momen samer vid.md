تمام. المرة دي هكتبهولك **كـ Note مرجعية للفيديو نفسه**، مش مجرد تلخيص سريع. هحافظ على ترتيب وأفكار صاحب الفيديو، وأفصل بين **ما قاله صراحة** وبين الاستنتاجات اللي ممكن نفهمها منه.

# AI Hunting Agent Architecture — Video 1

## 1. الفكرة الأساسية للفيديو

الفيديو **نظري**، والغرض منه شرح مشروع AI Agent للـhunting قبل البدء في بناء الـfeatures فعليًا في الفيديوهات التالية.

صاحب الفيديو بيشرح:

- إيه الـfeatures اللي موجودة في الـAgent.
    
- إيه أهم الملفات.
    
- كل ملف دوره إيه.
    
- الـAgent بيشتغل إزاي.
    
- إزاي الـAgent يتعامل مع الـskills.
    
- إزاي يقلل الـfalse positives.
    
- إزاي يحدد الـimpact والـpriority.
    
- إزاي يستخدم أكثر من Agent.
    
- إزاي يحفظ الـworkspace والـstate.
    
- إزاي يتعلم من الـwriteups ويحدّث `agent-briefing.md`.
    
- إزاي يربط OpenCode بالـMCP/browser.
    

والفيديو **مش هدفه تنفيذ كل ده مرة واحدة**، بل تجهيز الفهم قبل بناء كل feature لوحدها. صاحب الفيديو يقول بوضوح إن المشروع اتطور تدريجيًا، وإنه لا يُفترض البدء بـ70 Skill أو كل شيء دفعة واحدة.

---

# 2. شرط أساسي قبل استخدام AI في الـHunting

الـAI Agent **مش بديل عن فهم الـWeb Security**.

لازم الـHunter يكون عنده أساسيات مثل:

- OWASP Top 10.
    
- XSS.
    
- CSRF.
    
- HTTP basics.
    
- الفرق بين GET / POST / PUT / DELETE وغيرها.
    
- Requests / Responses.
    
- Server-side / Client-side concepts.
    
- Basic vulnerability concepts.
    

السبب:

الـAgent ممكن يتجاهل Finding أنت شايفها مهمة، أو يعتبر حاجة False Positive، وإذا أنت أصلًا مش فاهم vulnerability مش هتعرف توجهه.

يعني:

```text
AI finds something
        ↓
Human must understand it
        ↓
Human decides whether it deserves attention
```

فالمبدأ:

> **AI يساعد الباحث، لكنه لا يلغي فهم الباحث.**

---

# 3. `AGENTS.md` — الـMaster File

أهم ملف في المشروع هو:

```text
AGENTS.md
```

صاحب الفيديو يسميه:

> **Master / Main mindset of the project**

وهو الملف الذي يحتوي على التعليمات الرئيسية للـAgent:

```text
Who are you?
What is your mission?
What is your scope?
What should you do?
What should you avoid?
Where should you save things?
What should happen before/after testing?
```

يعني بدل ما الـAgent يتصرف من دماغه، عنده "constitution" للمشروع.

---

# 4. Scope Lock

أول Feature مهمة داخل `AGENTS.md`:

## Scope Lock

الغرض منها منع الـAgent من الخروج عن الـscope.

مثال المشكلة:

```text
Target = example.com
```

الـAgent يكتشف:

```text
third-party provider
subdomain
API domain
CDN
```

ثم يقرر من نفسه إنه يختبرهم.

ده خطر لأن وجود الـdomain في traffic أو التطبيق **لا يعني تلقائيًا إنه in-scope**.

إذًا:

```text
Scope
 ↓
Agent must respect it
 ↓
Before testing external/third-party assets
 ↓
Determine whether action is allowed
```

الفكرة الأساسية:

> **لا تجعل الـAgent يحدد الـscope بنفسه.**

---

# 5. Skills

أي Agent للحـunting لازم يكون عنده Skills متخصصة.

أمثلة ذكرها الفيديو:

```text
XSS
IDOR / Access Control
Privilege Escalation
...
```

لكن المشكلة لو عندك:

```text
70+ skills
```

والـAgent يحملهم كلهم في البداية.

النتيجة:

```text
Huge context
→ higher token usage
→ confusion
→ hallucination
→ worse performance
```

لذلك:

## Skill loading on demand

الـAgent أولًا يحدد:

```text
What am I testing?
```

ثم يحمل الـskills المرتبطة بالمهمة.

مثال:

```text
Testing XSS
    ↓
XSS skill
+
Encoding skill
+
Relevant bypass skill
```

وليس:

```text
Load all 70 skills
```

---

# 6. False Positive Gate

دي من أهم أجزاء الفيديو.

صاحب الفيديو عنده مرحلة اسمها:

```text
False Positive Gate
```

هدفها منع الـAgent من تحويل كل observation إلى vulnerability.

الـworkflow:

```text
Observation
   ↓
Candidate Finding
   ↓
False Positive Gate
   ↓
Evidence Standard
   ↓
Confirmed Finding
```

---

# 7. الـ10 Questions للـFalse Positive Gate

الفيديو يذكر وجود:

> **10 questions**

والقاعدة المذكورة بوضوح:

```text
If ANY question = NO
→ Do NOT report
```

لكن transcript الحالي **لا يحتوي النص الحرفي للأسئلة العشرة كلها**، لذلك ماينفعش ننسب قائمة من 10 أسئلة محددة للفيديو.

المحاور اللي يذكرها الفيديو صراحة تشمل:

### Scope

هل الـfinding داخل النطاق؟

### Real Impact

هل فيه أثر حقيقي؟

### Attacker Reachability

هل المهاجم قادر فعليًا يوصل للأثر؟

### PoC

هل عندك proof-of-concept واضح؟

### Exploitability

هل يمكن استغلال المشكلة فعلًا؟

### Evidence

هل يوجد دليل ملموس؟

### False-positive similarity

هل finding مشابهة لحاجة موجودة سابقًا في الـFalse Positive Database؟

### No guessing

هل الاستنتاج مبني على تجربة/دليل وليس:

```text
maybe
probably
could
```

### Impact

ما الأثر الحقيقي؟

### Confirmation

هل تم إثبات الحالة بدل ربط observations ببعضها فقط؟

إذن المبدأ:

```text
Candidate ≠ Finding
```

---

# 8. False Positive Database

صاحب الفيديو لديه:

```text
False Positive Database
```

الهدف:

```text
Old confirmed false positive
          ↓
Database
          ↓
New similar observation
          ↓
Recognize similarity
          ↓
Ignore / avoid repeating mistake
```

يعني الـAgent لا يبدأ من الصفر في كل مرة.

دي تعتبر **ذاكرة للأخطاء السابقة**.

---

# 9. Evidence Standard

بعد اجتياز False Positive Gate، يوجد:

```text
Evidence Standard
```

وصاحب الفيديو يذكر أن عنده **10 requirements** لازم تتوفر قبل اعتبار الـfinding حقيقية.

من الأشياء التي ذكرها صراحة:

- In scope.
    
- Real impact.
    
- Attacker can reach the impact.
    
- اجتياز الـFalse Positive questions.
    
- وجود evidence ملموس.
    
- عدم الاعتماد على التخمين.
    

المبدأ الأساسي:

> **No guessing.**

الـAgent لا يقول:

```text
"ممكن دي vulnerability"
```

ثم يبدأ report.

بل:

```text
Test
→ Evidence
→ Validation
→ Confirm
```

---

# 10. ليه الـAgent محتاج Filters؟

المشكلة الطبيعية:

```text
Agent finds A
Agent finds B
Agent sees relation
Agent says:
"Great, vulnerability!"
```

لكن A + B **مش بالضرورة vulnerability**.

لذلك:

```text
Finding candidate
 ↓
FP filtering
 ↓
Evidence
 ↓
Validation
 ↓
Real finding
```

وده أحد أهم أسباب عدم ترك الـAgent يعمل reporting مباشرة.

---

# 11. Multi-Agent Architecture

لو Agent واحد يعمل:

```text
Recon
Dorking
JS Analysis
Fuzzing
Exploitation
Reporting
```

كلهم في نفس الـcontext:

```text
Huge context
→ context exhaustion
→ confusion
→ hallucination
```

الحل الذي يطرحه الفيديو:

```text
Root / Main Agent
        │
 ┌──────┼──────┬──────┐
 ▼      ▼      ▼      ▼
Recon  Dork   JS   Other
Agent  Agent Agent Agent
```

كل Sub-Agent:

- له context منفصل.
    
- له tools مناسبة لمهمته.
    
- له task محددة.
    
- لا يخرج عن مهمته.
    

مثال الـRecon Agent:

```text
Recon Agent
├── detect domain
├── enumerate
├── fingerprint
├── discover related subdomains
└── return recon results
```

وليس مسؤولًا عن XSS أو exploitation.

وفي النهاية النتائج ترجع للـMain Agent.

---

# 12. Exploit Gate

لو الـAgent وجد Finding **حقيقية**، يبدأ:

```text
Exploit Gate
```

الهدف:

> معرفة أعلى Impact يمكن تحقيقه من الـFinding.

يعني لا يكتفي:

```text
"I found vulnerability."
```

بل يبحث:

```text
What can this actually lead to?
```

مثال conceptual:

```text
Finding
 ↓
Can it be chained?
 ↓
Can privilege increase?
 ↓
Can data be accessed?
 ↓
Can account impact increase?
 ↓
What is the real maximum impact?
```

والـExploit Gate تأتي **بعد confirmation**، وليس قبلها.

---

# 13. Bypass Logic

الفيديو لا يريد الـAgent أن يعتبر أول فشل نهاية الاختبار.

مثال:

```text
Payload failed
```

لا يعني:

```text
No vulnerability
```

بل قبل الانتقال للـnext test، يمكن تجربة تقنيات مثل:

- Request method changes.
    
- Response manipulation.
    
- Encoding.
    
- Alternative techniques.
    

الفكرة:

```text
Technique A failed
 ↓
Try relevant bypasses
 ↓
Then decide
```

---

# 14. Chaining

Feature أخرى:

```text
Chaining
```

الفكرة:

```text
Finding A
+
Finding B
=
Higher impact
```

مثال ذكره:

```text
Self-XSS
+
CSRF
```

بدل الحكم مباشرة أن الأولى useless، الـAgent يحاول معرفة هل يوجد chain يرفع الـimpact.

لكن مرة ثانية:

> لازم يكون فيه دليل فعلي، مش مجرد جمع أسماء vulnerabilities.

---

# 15. Strength / Previous State

إذا الـAgent عمل Hunting سابقًا على Target:

```text
Old state
Old endpoints
Old JS
Old tests
Old findings
```

في Hunt جديد لا يجب أن يبدأ من الصفر.

أول شيء:

```text
Retrieve old data
```

ثم:

```text
What changed?
What is new?
```

مثلًا:

```text
Old JS
+
New JS
↓
Analyze only new/changed areas
```

لأن target ممكن يكون اتغير:

- Features جديدة.
    
- Endpoints جديدة.
    
- APIs اتشالت.
    
- APIs اتضافت.
    
- Domain تغير.
    
- Behavior تغير.
    

فالـAgent لا يريد إعادة اختبار كل شيء بلا داعٍ.

---

# 16. Workspace Rules

كل Hunting Session لازم تحفظ نتائجها.

الفيديو يذكر حفظ أشياء مثل:

```text
Reports
Requests
Responses
JS
Evidence
Scripts
Subdomains
Notes
```

يعني عندك:

```text
Workspace
   ↓
Persistent hunting state
```

مش مجرد conversation history.

---

# 17. `prioritization.md`

الفيديو يضيف ملف:

```text
prioritization.md
```

الغرض:

```text
P1
P2
P3
P4
```

لكن أهم قاعدة:

> **لا تحدد severity من vulnerability type فقط.**

يعني:

```text
"XSS = P1"
```

مش منطق.

اللي يتقيم هو:

```text
Actual impact
+
Attacker capability
+
Exploitability
+
Reachability
```

الفيديو ينتقد مشكلة إن الـAI يرفع findings بسيطة جدًا لمستوى Critical، وده من أسباب report flooding.

---

# 18. Golden Line في Prioritization

الفكرة اللي يريد صاحب الفيديو ترسيخها:

```text
Do NOT classify by endpoint/vulnerability name alone.
```

بل:

```text
What can the attacker actually do?
```

يعني:

```text
Vulnerability Type
       ↓
Actual capability
       ↓
Real impact
       ↓
Priority
```

---

# 19. `agent-briefing.md`

فيه ملف:

```text
agent-briefing.md
```

فكرته:

```text
External knowledge
   ↓
Writeups / Articles / Resources
   ↓
Extract useful lessons
   ↓
Patterns
   ↓
Checklists
   ↓
agent-briefing.md
```

الـAgent لا يحتاج يقرأ 19 أو 50 writeup كامل كل مرة.

بل:

```text
Raw resources
→ distilled knowledge
→ reusable hunting notes
```

---

# 20. Update Workflow

فيه concept لأمر:

```text
/update
```

العملية بشكل عام:

```text
Resources
 ↓
Fetch
 ↓
Process
 ↓
Extract lessons
 ↓
Patterns / checklist
 ↓
Update agent-briefing.md
```

فـ`agent-briefing.md` بيتجدد مع المعرفة الجديدة.

---

# 21. Availability Testing Problem

صاحب الفيديو ذكر مشكلة:

الـAgent أحيانًا يحاول يتعامل مع حاجات مثل:

```text
Burp Collaborator
External callbacks
External server
```

بطرق غير مناسبة.

وكان يقوم أحيانًا بـ:

```text
curl
Browser
```

على شيء لا يمكن اختباره بهذه الطريقة.

الحل عنده كان تعديل الـinstructions بحيث يعرف:

```text
What can be tested
How it can be tested
What should not be tested that way
```

يعني **Methodology تشمل قيود الـtool نفسه**، وليس فقط vulnerability steps.

---

# 22. 405 Method Not Allowed

دي نقطة صغيرة لكنها مهمة جدًا.

لو endpoint رجع:

```text
405 Method Not Allowed
```

الـAgent لا يقول:

```text
Dead endpoint
```

بل يجرب:

```text
Other HTTP methods
OPTIONS
Relevant alternatives
```

الفكرة:

```text
405
≠
Stop
```

بل:

```text
405
→ investigate supported methods
```

دي قاعدة صغيرة داخل الـsystem methodology.

---

# 23. Browser / MCP

الفيديو يتكلم عن:

```text
OpenCode
+
MCP
+
Browser
```

والهدف إعطاء الـAgent القدرة على:

```text
Open browser
Navigate
Click
Screenshot
Inspect page
Interact
```

وده يوسّع testing من:

```text
HTTP only
```

إلى:

```text
Application behavior
+
UI interactions
```

---

# 24. Project Structure

الهيكل الأساسي الذي يقدمه الفيديو:

```text
huting/
├── AGENTS.md
├── prioritization.md
├── agent-briefing.md
├── opencode.json
├── .opencode.json
├── package.json
├── skills/
├── .agents/
├── runtime/
├── scripts/
├── learning_engine/
├── memory/
├── writeups/
├── .opencode/
├── testing/
├── wordlist/
├── logs/
└── .github/
```

المغزى مش أسماء الـfolders فقط.

المغزى هو فصل:

```text
Instructions
Skills
Runtime
Memory
Learning
Target Data
Automation
Logs
```

بدل ما كل شيء يعيش في نفس المكان.

---

# 25. أهم Architecture من الفيديو

```text
                    HUMAN
                      │
                      ▼
                 MAIN AGENT
                      │
           ┌──────────┴──────────┐
           │                     │
        Scope                Existing State
           │                     │
           └──────────┬──────────┘
                      ▼
               Understand Target
                      │
                      ▼
              Relevant Skills
                      │
                      ▼
               Specialized Agents
                      │
        ┌─────────────┼─────────────┐
        ▼             ▼             ▼
      Recon          Dork           JS
        │             │             │
        └─────────────┼─────────────┘
                      ▼
                   Testing
                      │
                      ▼
                 Candidate
                      │
                      ▼
             False Positive Gate
                      │
                      ▼
              Evidence Standard
                      │
                      ▼
                 Validation
                      │
                      ▼
                  CONFIRMED
                      │
                      ▼
                Exploit Gate
                      │
                      ▼
                Max Impact
                      │
                      ▼
                   Report
                      │
                      ▼
                  Workspace
                      │
                      ▼
             Memory / Briefing
```

---

# 26. أهم قواعد الفيديو

### Rule 1

**Scope before testing.**

### Rule 2

**Do not load every Skill. Load relevant Skills only.**

### Rule 3

**Candidate finding ≠ confirmed finding.**

### Rule 4

**Use a False Positive Gate.**

### Rule 5

**Use Evidence Standards before reporting.**

### Rule 6

**No guessing.**

### Rule 7

**Specialize Agents instead of making one giant Agent.**

### Rule 8

**Try relevant bypasses before concluding a test failed.**

### Rule 9

**Use actual impact for prioritization.**

### Rule 10

**Persist every important test result in the workspace.**

### Rule 11

**Use previous state; don't repeat completed work blindly.**

### Rule 12

**Successful exploitation should be followed by impact analysis, not immediate reporting.**

### Rule 13

**Learn from old false positives.**

### Rule 14

**Distill external research into reusable knowledge.**

### Rule 15

**Human remains part of the loop.**

---

# 27. أهم حاجة تحفظها من الفيديو

الفكرة كلها ممكن تختصر في:

```text
DON'T BUILD:
"AI that hacks websites"

BUILD:
"A system that applies a security-testing methodology"
```

يعني:

```text
Scope
→ State
→ Surface
→ Function
→ Trigger
→ Skill
→ Test
→ Evidence
→ Validation
→ Impact
→ Report
→ Memory
```

وده هو السبب إن **`AGENTS.md` + Skills + Workspace + Memory + Gates** أهم من مجرد اختيار Model قوي.

---

## ملاحظة مهمة للـNotes

**الفيديو ذكر وجود 10 False Positive Questions و10 Evidence Requirements، لكنه لم يعرض نص العشرين نقطة بالكامل في الـtranscript الذي عندنا.** لذلك في الـNotes الأفضل تكتب:

```text
False Positive Gate:
- 10 questions
- Any NO → no report

Evidence Standard:
- 10 requirements
- Must have concrete evidence
- No guessing

TODO:
Extract exact 10+10 requirements from the implementation video where AGENTS.md is actually created.
```

وده أدق من إننا نملأهم من دماغنا ونكتشف لاحقًا إننا بنبني المشروع على اختراع من عندنا.
