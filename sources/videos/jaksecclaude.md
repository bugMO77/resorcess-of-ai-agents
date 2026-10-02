دي حلقة **مهمة جدًا** بالنسبة للـ architecture، لأنها بتضيف لنا حاجة كنا لسه بنشوفها بشكل عام: **إزاي ننظم Claude نفسه من جوه**، والفرق الفعلي بين `CLAUDE.md` و`Skills` و`Subagents` و`Agent Teams`، وإزاي ده يركب فوق الـ Coordinator/Solver architecture اللي جمعناها قبل كده.

# Video X — Claude Code Architecture: CLAUDE.md, Skills, Subagents & Agent Teams

## 1. Main Idea

الحلقة مبنية حول فكرة:

> **ما تستخدمش Claude الافتراضي، ابنِ Claude مخصص يعرف طريقتك ومعلوماتك وأدواتك ويطور نفسه مع الوقت.**

الترتيب الذي يقدمه الفيديو تقريبًا:

```text
CLAUDE.md
   ↓
Skills
   ↓
Subagents
   ↓
Agent Teams
   ↓
Persistent / scalable hacking system
```

والهدف ليس مجرد "custom prompt"، بل بناء بيئة يستطيع فيها الـ AI أن:

- يحتفظ بسياق دائم.
    
- يستدعي methodology عند الحاجة.
    
- يفوض مهام متخصصة إلى Agents.
    
- يجعل عدة Agents تعمل معًا.
    
- يحتفظ بنتائج hunting السابقة.
    
- يعيد استخدامها في جلسات لاحقة.
    

---

# 2. Concepts / Architecture

## 2.1 `CLAUDE.md`

أهم طبقة أساسية.

الفيديو يعاملها تقريبًا كأنها:

```text
Persistent System Context
```

وتوضع في project folder أو بشكل أوسع ليتم تحميلها في sessions.

المحتوى المقترح:

```text
Who am I?
What am I doing?
What do I hunt?
How should Claude behave?
How should impact be judged?
Where is project data?
Where are scripts?
How are findings stored?
What should be read at session start?
```

المتكلم ينصح **ألا تكون ضخمة** لأن محتواها يعاد إدخاله مع الـ context، بما في ذلك عند compaction بحسب وصفه.

---

# 3. What Goes Into `CLAUDE.md`

الحلقة تقسمها عمليًا إلى عدة أجزاء.

### User Context

مثلاً:

```text
Bug bounty hunter
Favorite vulnerability classes
Applications/features of interest
Working environment
```

### Behavior

مثلاً:

```text
Authorized security research
Preferred workflow
How to handle tasks
```

### Impact Guidance

المتكلم يضع قواعد خاصة بتقييم impact، مثل أمثلة يذكر فيها أن بعض أنواع XSS أو blind SSRF لا ينبغي تلقائيًا رفعها إلى critical/high بدون impact إضافي.

### Reporting / State

يحدد:

```text
where findings go
where gadgets go
how target directories are created
what files should be read when resuming
```

### System Paths

مثل:

```text
scripts/
JS/
POC locations/
other standard directories
```

والفكرة الأساسية:

> **Claude يعرف بيئة الشغل بدل ما تسشرح له نفس الحاجات كل session.**

---

# 4. Persistent Target State

واحدة من أهم أفكار الحلقة هي بناء structure ثابت لكل target.

مثال المصدر:

```text
project/
└── target/
    ├── vulnerabilities.md
    └── gadgets.md
```

بحيث:

```text
vulnerabilities.md
→ confirmed/high findings

gadgets.md
→ useful lower-severity observations / gadgets
```

ثم Claude يحصل على rule مثل:

```text
Whenever I work on a target,
read these files first.
```

فتصبح بداية session:

```text
New Session
   ↓
Read Target State
   ↓
Understand previous work
   ↓
Continue
```

بدل:

```text
New Session
   ↓
"What the hell were we doing yesterday?"
```

---

# 5. Skills

الـ Skill مختلفة عن `CLAUDE.md`.

## `CLAUDE.md`

دائم / global context نسبيًا.

## Skill

يتم تحميلها **عندما تكون relevant**.

```text
Claude
  ↓
sees task
  ↓
matches skill description
  ↓
loads skill
  ↓
executes methodology
```

وهذا يقلل context pollution.

المصدر يؤكد أن وصف الـ Skill مهم جدًا، لأن Claude لن يستفيد منها إذا لم يستدعها.

---

# 6. Skill Structure

الفيديو يوضح وجود metadata في أعلى الملف، مثل:

```text
name:
description:
```

والـ description هي أهم جزء تقريبًا لأنها تساعد Claude على معرفة:

```text
"When should I load this?"
```

لكن يجب أن تكون:

- محددة بما يكفي لتشتغل في الوقت المناسب.
    
- عامة بما يكفي كي لا تفشل إذا تغيرت صياغة المهمة.
    

وده متوافق جدًا مع الفيديوهات السابقة التي كانت تعتبر Skill = **methodology + trigger**.

---

# 7. Building Skills From Research

الـ workflow الذي استخدمه المتحدث:

```text
Research / Blog / Writeup
        ↓
Give it to Claude
        ↓
"Create a skill"
        ↓
Claude creates first draft
        ↓
Human edits
        ↓
Final Skill
```

مثاله كان research عن webhooks ثم تحويله إلى Skill لـ:

```text
Webhook Discovery
```

بحيث يتم تفعيله عندما يريد الباحث enumeration/discovery للـ webhooks.

وده مهم لأنه يثبت مرة أخرى الفكرة:

```text
Research
   ↓
Methodology
   ↓
Skill
   ↓
Reusable hunting capability
```

---

# 8. Human Editing Still Matters

الفيديو لا يقول:

> Claude كتب Skill = انتهينا.

بالعكس، المتحدث يعدل Skill يدويًا ويحذف الأشياء التي يراها غير ضرورية.

مثل:

- reporting sections غير المهمة.
    
- duplicate instructions.
    
- impact rules التي مكانها الطبيعي `CLAUDE.md`.
    
- الأشياء التي لا يريدها في methodology.
    

يعني:

```text
AI-generated draft
       ↓
Human compression / refinement
       ↓
Production skill
```

ودي نقطة مهمة جدًا عند بناء skills عندنا.

---

# 9. Skill Loading

الـ Skill يمكن أن يتم استدعاؤها تلقائيًا عندما يرى Claude trigger مناسب.

ويمكن أيضًا forcing/explicit invocation بالاسم.

إذًا:

```text
Automatic
```

أو:

```text
Manual
```

وده مفيد جدًا في hunting لأن بعض المهام:

- واضحة جدًا للـ router.
    
- وبعضها أنت عايز تفرض methodology معينة عليها.
    

---

# 10. Skills vs Subagents

دي من أهم أجزاء الحلقة.

الـ Skill:

```text
Instructions / Methodology
```

أما الـ Subagent:

```text
Separate Claude execution context
```

يعني:

```text
Main Claude
   ↓
spawn Subagent
   ↓
Subagent executes task
   ↓
returns summary
   ↓
Main Claude continues
```

---

# 11. Why Subagents Exist

المشكلة:

لو أضفت كل methodology وكل tools وكل knowledge إلى الـ main context:

```text
Main Context
├── Recon
├── Reverse engineering
├── JS analysis
├── Webhooks
├── Cloud
├── Binary analysis
├── ...
```

هيكبر بشكل غبي.

الحل:

```text
Main Claude
     ↓
"Reverse engineer this"
     ↓
Reverse Engineering Agent
     ↓
Agent-specific context
     ↓
Result
     ↓
Main Claude
```

الـ Main Claude لا يحتاج أن يعيش طوال session وهو حامل كل تفاصيل reverse engineering.

المصدر يصف الـ subagent بأنه مناسب للمهام الأكثر تعقيدًا أو المتخصصة.

---

# 12. Subagent Can Have Its Own World

الـ Subagent يستطيع أن يكون له:

```text
Own prompt
Own CLAUDE.md/context
Own skills
Own tools
Own model
```

ويمكن تحديد tools التي يُسمح له باستخدامها.

مثلاً:

```text
Research Agent
→ read + grep only

Exploit Agent
→ HTTP + browser + scripts

Code Agent
→ write + execute
```

ودي إضافة قوية جدًا إلى architecture بتاعتنا.

---

# 13. Model Routing Inside Agents

الفيديو يوضح إمكانية اختيار model للـ Agent.

مثلاً:

```text
Opus
Sonnet
Haiku
```

الفكرة:

```text
Main Agent
    ↓
Hard reasoning → strong model
Simple analysis → cheap/fast model
Large file reading → smaller model
```

ويذكر المتحدث أن النماذج الأصغر تكون أسرع وأقل استهلاكًا للـ tokens، ومناسبة لمهام مثل القراءة والتلخيص، رغم أن قدرتها أقل.

---

# 14. Meta-Agent / Model Escalation

الحلقة تذكر أيضًا فكرة أكثر تقدمًا:

Agent صغير يمكن أن يطلب من model أقوى مساعدته عندما لا يعرف ماذا يفعل.

تقريبًا:

```text
Cheap Agent
   ↓
stuck / uncertain
   ↓
Strong Model
   ↓
Guidance
   ↓
Cheap Agent continues
```

المصدر يصفها كميزة/تجربة، وليس كـ architecture نهائية.

---

# 15. Agents Can Spawn Other Agents

وهنا hierarchy تبدأ تكبر:

```text
Main Claude
    ↓
Subagent
    ↓
Subagent
    ↓
...
```

إذن الـ Agent ليس فقط worker.

يمكن أن يصبح:

```text
Orchestrator
```

بنفسه داخل نطاق معين.

---

# 16. Agent Teams

دي طبقة أعلى من Subagents.

في الـ Subagents:

```text
Main
 ├── Agent A
 ├── Agent B
 └── Agent C

Each returns result
to Main
```

أما Agent Teams:

```text
                 TEAM LEAD
                     │
              Shared Task List
             ┌───────┼────────┐
             ↓       ↓        ↓
          Agent A Agent B Agent C
             ↕       ↕        ↕
          Communication
```

فالـ Agents يستطيعون:

- تنفيذ tasks مختلفة.
    
- استخدام task list مشتركة.
    
- التواصل مع بعض.
    
- التعاون بدل مجرد إرجاع result للـ parent.
    

---

# 17. Subagents vs Agent Teams

||Subagents|Agent Teams|
|---|---|---|
|Structure|Parent → workers|Lead → teammates|
|Communication|Mostly return result|Teammates communicate|
|Shared tasks|Limited|Central task list|
|Best for|Isolated work|Collaborative work|
|Context|Separate|Separate + coordination|
|Cost|Lower|Higher|
|Complexity|Lower|Higher|

المصدر يحذر بوضوح أن Agent Teams تستهلك **عددًا كبيرًا من الـ tokens**.

---

# 18. Example for Hunting

مثلاً:

```text
Main Coordinator
        ↓
Task: Analyze target
        ↓
Agent Team
 ├── JS Analysis
 ├── Webhook Discovery
 ├── API Mapping
 └── Access Control
```

وكل agent عنده methodology مختلفة.

وده أقرب تطبيق مباشر للفيديو على bug bounty.

---

# 19. Hooks

الحلقة تذكر Hooks كميزة:

```text
Event
 ↓
Hook
 ↓
Action
```

لكن المتحدث يرى أن استخداماتها التي يعرفها حاليًا أقرب للـ development، مثل formatting، وليس feature رئيسية للـ bug bounty.

إذن:

**سجلناها، لكن لا نضيفها حاليًا كطبقة أساسية للـ hunting architecture.**

---

# 20. Plugins

Plugins تعطي طريقة لتوزيع:

```text
Skills
Agents
Configuration
Tools
```

من مصدر خارجي.

لكن المصدر لا يتوسع كثيرًا فيها، لذلك لا نخليها layer مستقلة عندنا في الوقت الحالي.

---

# 21. MCP

دي نقطة مثيرة جدًا لأن عندنا بالفعل MCP مع Burp.

المتكلم يرى أن:

- MCP له استخدامات.
    
- لكنه ليس دائمًا أفضل choice للـ hacking.
    
- في بعض الحالات CLI + Skill أقل تكلفة من حيث الـ context/tokens.
    
- لكنه يرى مثال Playwright حيث MCP قد يكون أفضل لأن session المستمر مهم.
    

دي **رؤية المتحدث في الفيديو** وليست قاعدة عامة.

وبالتالي بالنسبة لنا:

```text
MCP ≠ deprecated
```

بل:

```text
Use MCP when stateful interaction matters.
Use CLI + Skill when deterministic command execution is enough.
```

---

# 22. Workspace Architecture

الفيديو يعطينا shape مفيد جدًا:

```text
project/
├── CLAUDE.md
├── targets/
│   └── target-a/
│       ├── vulnerabilities.md
│       └── gadgets.md
├── skills/
└── ...
```

والـ global skills موجودة في مكان عام ثم يتم تحميلها عند الحاجة.

وده ينسجم جدًا مع هدفنا السابق:

```text
Global methodology
+
Target-specific state
```

---

# 23. Gates / Validation

الفيديو نفسه لا يقدم False Positive Gate جديدة مثل فيديو Architecture السابق.

لكن يوجد عنصر مهم:

### Output Summary

الـ Subagent يعود بنتيجة إلى الـ parent.

يعني:

```text
Subagent
   ↓
Raw work
   ↓
Summary
   ↓
Parent
```

وهذا يقلل تسريب كل التفاصيل إلى الـ main context.

**ده ليس validation.** هو **context compression / result handoff**.

ودي نقطة لازم ما نخلطهاش مع الـ Validator الموجود في معماريتنا.

---

# 24. Problems / Lessons Learned

## Problem 1: Skill doesn't trigger

```text
Perfect Skill
+
Never invoked
=
useless
```

لذلك description مهمة جدًا.

---

## Problem 2: Skills can become huge

في هذه الحالة يمكن تحويلها إلى Subagent متخصص.

```text
Large Skill
    ↓
Specialized Agent
```

---

## Problem 3: Main context pollution

لو كل شيء موجود في main context، كل session تصبح أثقل.

Subagents تعزل ذلك.

---

## Problem 4: Agent Teams are expensive

زيادة teammates لا تعني تلقائيًا نتيجة أفضل، لأن الـ token consumption يرتفع بسرعة.

---

## Problem 5: AI-generated config isn't perfect

المتكلم نفسه يقول إنه يجعل Claude يكتب الـ first draft ثم يدخل يدويًا ويحذف ويعدل.

---

# 25. Exact Things Not Specified

المصدر لا يحدد:

- الـ exact schema النهائي لـ `CLAUDE.md`.
    
- عدد Skills الأمثل.
    
- كيفية قياس جودة Skill.
    
- كيفية اختيار Skill تلقائيًا داخليًا بالتفصيل.
    
- كيفية decide متى تستخدم Subagent بدل Skill.
    
- كيفية decide متى تستخدم Team بدل Subagents.
    
- exact context-transfer protocol بين Agents.
    
- exact persistence layer للـ state.
    
- exact mechanism لدمج نتائج Agents في target ledger.
    
- exact algorithm لتجنب duplicate work داخل Team.
    
- exact cost/latency model.
    
- تفاصيل الـ experimental escalation من model صغير إلى model أقوى.
    

ودي حاجات تظل **TODO/Research** عندنا، مش نخترعها.

---

# 26. Key Takeaways

### 1.

`CLAUDE.md` = **persistent identity + project behavior + state rules**

### 2.

`Skill` = **on-demand methodology**

### 3.

`Subagent` = **isolated specialized worker**

### 4.

`Agent Team` = **multiple cooperating workers + shared task list**

### 5.

Agent يستطيع تحميل Skills.

### 6.

Agent يمكنه spawn Agents أخرى.

### 7.

Model selection يمكن أن يكون per-agent.

### 8.

نتيجة الـ Subagent ترجع كـ summary بدل تلويث الـ Main Context.

### 9.

كلما زاد التعقيد، زادت تكلفة الـ token/context.

### 10.

أفضل system هو الذي يعزل المعرفة حسب المهمة بدل وضع كل شيء في system prompt واحد.

---

# 27. How It Fits With Previous Videos

هنا بقى الفيديو **يغير شكل الـ Orchestration layer عندنا**.

قبل كده كان عندنا:

```text
COORDINATOR
   ↓
SOLVERS
```

دلوقتي نقدر نفكها:

```text
                 ROOT / COORDINATOR
                         │
              ┌──────────┴──────────┐
              │                     │
         SUBAGENTS              SKILLS
              │                     │
              │              methodology
              ↓
      specialized execution
```

ثم لو المهمة كبيرة:

```text
ROOT / COORDINATOR
        ↓
    TEAM LEAD
        ↓
 ┌──────┼──────┐
 ↓      ↓      ↓
A       B      C
```

---

# Architecture v0.6

بعد الفيديو الحالي، الـ architecture بتاعتنا تصبح:

```text
                         HUMAN
                           │
                           ▼
                         SCOPE
                           │
                           ▼
                ┌──────────────────────┐
                │    ROOT COORDINATOR  │
                └──────────┬───────────┘
                           │
                           ▼
                ┌──────────────────────┐
                │    TARGET STATE      │
                │ Ledger / Coverage    │
                │ Sessions / Findings  │
                └──────────┬───────────┘
                           │
                           ▼
                ┌──────────────────────┐
                │    CONTEXT ENGINE    │
                │ HTTP / JS / Docs     │
                │ Reports / Notes      │
                │ IDs / Business Logic │
                └──────────┬───────────┘
                           │
                           ▼
                 OBJECTIVE / TASK
                           │
                           ▼
              ┌────────────────────────────┐
              │       EXECUTION LAYER      │
              │                            │
              │   Skill                    │
              │     OR                     │
              │   Subagent                 │
              │     OR                     │
              │   Agent Team               │
              └────────────┬───────────────┘
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
           Agent A      Agent B      Agent C
              │            │            │
           Skills       Skills       Skills
              │            │            │
              └────────────┼────────────┘
                           │
                           ▼
                        RUNTIME
               Burp / Caido / Browser
               Scripts / HTTP / OOB
                           │
                           ▼
                     OBSERVATIONS
                           │
                           ▼
                       VALIDATOR
                           │
                           ▼
                    FALSE POSITIVE
                         GATE
                           │
                           ▼
                    EVIDENCE / IMPACT
                           │
                           ▼
                       FINDING
                           │
                           ▼
                        REPORT
                           │
                           ▼
                   MEMORY / LEARNING
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
          CLAUDE.md      Skills      Target State
              │            │            │
              └────────────┼────────────┘
                           ▼
                  BETTER FUTURE RUNS
```

## والتمييز المهم جدًا الآن

```text
CLAUDE.md
= "مين إحنا وازاي النظام يتصرف؟"

SKILL
= "إزاي ننفذ methodology معينة؟"

SUBAGENT
= "مين ينفذ المهمة المتخصصة؟"

AGENT TEAM
= "مين ينفذ مجموعة مهام متعاونة؟"

COORDINATOR
= "إيه المهمة اللي تتعمل ومين يعملها؟"

TARGET STATE
= "إيه اللي حصل قبل كده؟"

CONTEXT ENGINE
= "إيه المعلومات اللي نحتاجها دلوقتي؟"

RUNTIME
= "إزاي ننفذ الفعل؟"

VALIDATOR
= "هل اللي لقيناه حقيقي؟"
```

وده **أهم refinement** أخدناه من الفيديو. لأنه أخيرًا فصل لنا الحاجات اللي كنا أحيانًا بنسميها كلها "Agent" رغم إنها في الحقيقة خمس وظائف مختلفة. البشر طبعًا اخترعوا المصطلح "Agentic" وبعدها احتاجوا 17 صندوقًا لشرح الفرق بينهم. 😭
