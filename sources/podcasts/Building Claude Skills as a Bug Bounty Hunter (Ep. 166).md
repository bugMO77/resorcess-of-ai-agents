تمام، ده **يتسجل كـ Note مستقلة** لأن الفيديو ده فيه حاجات مهمة جدًا، وخصوصًا إنه بيجاوب على أسئلة كنا إحنا أصلًا وصلنا لها: **إمتى أعمل Skill؟ إمتى أسيب الـAgent حر؟ إزاي أرتب الـNotes؟ إزاي أخليه يشتغل ساعات؟ وإيه الفرق بين Agent وFolder؟**

# Critical Thinking - Bug Bounty Podcast

## AI Skills, Methodology, Notes, Validation & Persistent Hunting

> **نوع الحلقة:** Nuts & Bolts / Practical AI-assisted Bug Hunting
> **الموضوع الأساسي:** استخدام Claude Code/Agent Harnesses داخل الـBug Bounty، وتصميم الـSkills والـNotes والـValidators والـTarget-specific environments.
> **المحور الرئيسي:** لا تحاول تحويل الـAI إلى نسخة صلبة منك في كل شيء. استخدم الـSkills حيث تكون المعرفة أو الطريقة المطلوبة غير متاحة للـmodel أو تريد طريقة تنفيذ محددة، واترك له مساحة للاستكشاف عندما يكون جيدًا في المهمة أصلًا. 

---

# 1. Main Idea

الحلقة تناقش استخدام الـAI في الـBug Bounty باعتباره:

```text
Human Hunter
     +
AI Agent
     +
Skills
     +
Notes / State
     +
Tools
     +
Validation
```

وليس:

```text
"اكتب له hack target وخلاص"
```

الفكرة الأساسية عند المتحدثين:

> **AI أصبح أداة Pair-Hacking، لكن لازم يكون عندك architecture يخليه يعمل بالشكل اللي يناسبك.**

وفي نفس الوقت، لا ينبغي أن تحول كل شيء إلى instructions صلبة، لأن جزءًا من قوة الـAgent هو قدرته على اكتشاف حلول لم تكن أنت قد كتبتها له.

---

# 2. أهم نقطة في الحلقة: لا تستخدم كلمة "Training" بشكل فضفاض

أحد المتحدثين يعترض على عبارة:

> "I'm training Claude"

ويفرّق بين:

```text
Training
```

وبين:

```text
Context
Prompt
Skills
Instructions
Knowledge
Tools
```

يعني عندما تضع له methodology أو notes أو skill، فأنت **توجهه وتزوده بالمعلومات والأدوات**، وليس أنك تدرب نموذجًا جديدًا.

النقطة دي مهمة لأننا في مشروعنا لازم نفرق بين:

```text
Model training
```

وبين:

```text
Agent configuration
+
Knowledge
+
Skills
+
Memory
```



---

# 3. إمتى Skill تكون مفيدة؟

الفيديو يطرح السؤال الأهم:

> متى أعمل Skill أصلًا؟

ويصنف الحالات المهمة إلى عدة فئات.

## الحالة الأولى: الـAgent لا يعرف الشيء

مثال:

```text id="vns5ur"
Custom VPS
Custom server
Custom directories
Custom credentials
Custom API
Custom environment
```

الـAgent لن يعرف:

```text
where to save
how to connect
which server
which API
which workflow
```

هنا الـSkill أو `CLAUDE.md` مفيدة لأنها تزوده بالمعلومات التي **لا يمكن للنموذج أن يخمنها من نفسه**. 

---

# 4. الحالة الثانية: Secret / Non-public Knowledge

لو عندك:

```text id="52c4ls"
Technique
Gadget
Oracle
Private research
Custom methodology
```

مش موجودة في الـpublic training data.

فبدل ما تعتمد على أن الـmodel سيخترعها، تضعها في:

```text id="xw2y7n"
Skill
Notes
Memory
References
```

مثال ذكره الحوار:

```text
Gadgets
Primitive
Program-specific knowledge
Unique bug bounty techniques
```

وده يدخل مباشرة في مفهوم **secret knowledge**.



---

# 5. الحالة الثالثة: هناك طرق كثيرة للحل، لكنك تريد طريقة معينة

دي من **أهم أفكار الحلقة**.

مثلًا عند عمل HTTP request:

الـAgent يستطيع استخدام:

```text
curl
Python
JavaScript
Kaito
Wget
Chrome DevTools
Playwright
```

لكن أنت تريد:

```text id="3d6fwm"
Use Kaito
because:
- I need proxy visibility
- I need history
- I need screenshots
- I need reproducibility
- I need replay sessions
```

هنا Skill مفيدة.

لأن المشكلة ليست:

> "هل يستطيع تنفيذ المهمة؟"

بل:

> "هل أريده ينفذها **بالطريقة التي تناسب workflow الخاص بي**؟"

وده أحد أهم تعريفات الـSkill في الحلقة. 

---

# 6. Skill لا يجب أن تمنع الـAgent من التفكير

ودي نقطة مهمة جدًا.

الـSkill يمكن أن تقول:

```text
Try Method A
```

لكن لا يجب أن تتحول إلى:

```text
If A fails:
STOP
```

الـpattern الأفضل:

```text id="jo1my7"
Method A
   ↓
fails?
   ↓
Method B
   ↓
fails?
   ↓
Method C
   ↓
Agent exploration
```

## Fallback Architecture

الفيديو يعطي مثالًا على Skill تستخدم عدة مستويات:

```text id="r1w4xq"
Tier 1
Use predefined skill/tool

        ↓ fails

Tier 2
Use underlying client/library

        ↓ fails

Tier 3
Write a script / compose actions

        ↓ fails

Tier 4
Use lower-level protocol directly
```

وده يعتبر **Fallback Ladder**.

الفكرة:

> **Skill تعطي deterministic path، لكن لا تسجن الـAgent داخله.**



---

# 7. متى الـSkill تصبح مشكلة؟

المتحدثون يحذرون من:

```text
Bad AGENTS.md
Bad CLAUDE.md
Poorly designed Skills
```

لأنها ممكن **تقلل جودة الـAgent بدل ما تحسنها**.

السبب:

```text
Over-constrained instructions
+
irrelevant methodology
+
too many rules
=
lower flexibility
```

فالمبدأ:

> **Do not add instructions just because you can.**



---

# 8. Hardcoded Methodology vs Free Exploration

دي أهم فكرة عملية في الحلقة كلها.

مثال:

أنت عندك:

```text
Front-end Analysis Methodology
```

تقدر تعمل Skill تجبر الـAgent على:

```text
1. Find JS
2. Find inline JS
3. Search source maps
4. Beautify
5. Extract endpoints
6. Extract parameters
7. ...
```

والميزة:

```text
Determinism
Coverage
Repeatability
Confidence
```

لكن المشكلة:

قد يفوت الـAgent أشياء لم تدخلها في الـworkflow.

---

# 9. الحل: شغل الاتنين

الحوار يقترح:

```text
Run A:
Hardcoded Methodology
```

وفي نفس الوقت:

```text
Run B:
Free-roaming Agent
```

ثم:

```text id="0ij4yd"
A output
+
B output
↓
Compare
↓
What did A miss?
What did B miss?
What did either try that the other didn't?
↓
Improve methodology
```

وده نظام ممتاز جدًا لتطوير الـSkills.

---

# 10. Continuous Skill Improvement Loop

الـworkflow يصبح:

```text
Methodology
   ↓
Run
   ↓
Free exploration
   ↓
Compare
   ↓
Discover new technique
   ↓
Add useful technique to skill
   ↓
Run again
```

يعني الـSkill ليست ملفًا ثابتًا.

هي:

```text
Skill v1
→ Test
→ Learn
→ Skill v2
→ Test
→ Learn
→ Skill v3
```

وده يخلي الـAgent يساعدك في **تحسين الـAgent نفسه**.



---

# 11. لا تعتمد على Output فقط عند المقارنة

في الحلقة ظهرت نقطة ممتازة جدًا:

لو Agent A وAgent B خلصوا شغل، لا تقارن فقط:

```text
Report A
vs
Report B
```

لأنك قد تفقد معلومات من الـprocess نفسه.

الأفضل أن كل Agent يحتفظ بـ:

```text
What I tried
What worked
What failed
What I skipped
What I discovered
```

ثم Agent آخر يقارن:

```text
Context A
+
Output A

vs

Context B
+
Output B
```

وده أفضل من مجرد جمع التقارير النهائية.

---

# 12. `Session Search`

أحد الأمثلة العملية التي ذكرها المتحدث:

Skill صغيرة للبحث داخل جلسات Claude السابقة.

مثل:

```text
"I talked yesterday about YAML parsers."
```

بدل ما تبحث يدويًا:

```text
session-search
↓
grep / rg across session logs
↓
find relevant conversation
```

هو ذكر أن لديه عدة GB من session logs، ويستخدم Skill صغيرة للوصول للمعلومات القديمة. 

## Lesson

ليس كل Skill لازم تكون:

```text
"Advanced XSS Hunting Agent"
```

ممكن تكون صغيرة جدًا:

```text
Search previous sessions
Find token
Locate old research
Find old command
```

وأحيانًا هذه الـmicro-skills تكون أكثر فائدة.

---

# 13. Notes Structure

دي من **أهم الحاجات اللي خرجت من الحلقة**.

الـHunter هنا لا يحط كل شيء في `notes.md`.

بل hierarchy:

```text id="q6a8tq"
Notes
  ↓
Leads
  ↓
Primitives / Gadgets
  ↓
Findings
  ↓
Reports
```

أو بشكل funnel:

```text id="wvdx4p"
LOTS OF NOTES
      ↓
    LEADS
      ↓
   PRIMITIVES
      ↓
   FINDINGS
      ↓
   REPORTS
```

كل مستوى يكون أقل وأكثر قيمة من المستوى الذي قبله.



---

# 14. معنى Notes

الـ`Notes` هي كل الملاحظات الخام المهمة:

```text
Behavior
Observation
Endpoint
Interesting parameter
Architecture detail
Odd response
```

مش شرط تكون vulnerability.

---

# 15. معنى Leads

الـLead هي:

```text
Interesting thing
that deserves investigation
```

مثلًا:

```text
Potential IDOR
Odd redirect
Interesting endpoint
Unexpected auth behavior
```

لكن لم يتم إثباتها.

إذًا:

```text
Lead ≠ Finding
```

---

# 16. Primitives / Gadgets

الـPrimitive أو Gadget هو شيء قابل لإعادة الاستخدام أو البناء عليه.

مثل:

```text
Interesting endpoint
Interesting parameter
Known bypass
Useful request
Interesting JS function
Oracle
Authentication quirk
```

الهدف أن تخرج من تجربة Target معينة **knowledge reusable**.

---

# 17. Findings

الـFinding هي التي اجتازت الـvalidation.

```text
Candidate
   ↓
Validation
   ↓
Confirmed Finding
```

وهذا يتطابق مع فكرة الـvalidators التي ظهرت في الفيديوهات السابقة.

---

# 18. Reports

الـReport هو آخر مرحلة:

```text
Finding
 ↓
Report
```

بالتالي لا يجب أن تكون كل ملاحظة Report.

وده يقلل جدًا من:

```text
Report spam
False positives
Overrated issues
```

---

# 19. Validator Skill

الفيديو يقترح بشدة:

```text
Validator
```

سواء:

```text
Skill
```

أو:

```text
Sub-agent
```

أو حتى:

```text
Tool
```

وظيفته:

```text
Candidate Finding
      ↓
Validator
      ↓
Real?
```

---

# 20. Validator يجب أن يكون Skeptical

مثال التعليمات التي يقترحونها:

```text
CORS issues are often false positives.
```

أو:

```text
Don't report BXSS
unless there is an actual trigger.
```

والأهم:

```text
Do not mark everything High/Critical.
```

لازم تعطي الـValidator **أمثلة حقيقية** على:

```text
False Positive
Low
Medium
High
Critical
```

بدل أن تقول له:

> "كن دقيقًا."

لأن كلمة "دقيق" لا تفعل شيئًا بمفردها.



---

# 21. AI-generated Reports مشكلة مستقلة

الحلقة تحذر أن الـAI-generated report قد:

### يدمج Bugs متعددة

```text
Bug A
+
Bug B
+
Bug C
↓
One strange report
```

### يسيء فهم Threat Model

مثل:

```text
Feature restriction bypass
```

ويعتبرها:

```text
"Complete security degradation"
```

بدون وجود evidence مناسب.

إذن:

```text
AI Report ≠ automatically accurate
```

ولذلك المتحدث يفضل أن يكون الـReport Writer:

```text
concise
technical
structured
consistent
```

ومعتمد على examples جيدة من reports كتبها الإنسان.



---

# 22. Report Writer Skill

Skill مناسبة:

```text
Report Writer
```

المدخلات:

```text
Finding
Evidence
POC
Impact
Context
```

والـoutput:

```text
Exact fields
Exact structure
Concise technical writing
```

والأفضل أن تعطيها:

```text
Your best historical reports
```

ثم تقول:

```text
Match this structure and quality.
```

---

# 23. Scope Skill

من الأمثلة المهمة:

```text
BB Scope / H1 Brain
```

الفكرة أن الـAgent لا يعرف دائمًا:

```text
Program policy
Scope
Previous disclosed reports
```

فSkill أو MCP تجلب له:

```text
Policy
Scope
Public reports
```

وده يقلل:

```text
Out-of-scope testing
Wrong assumptions
Repeated issues
```



---

# 24. Kaito Skill / Proxy Integration

المتحدثون يركزون جدًا على فكرة ربط الـAgent بالـHTTP proxy.

مثل:

```text
Claude
 ↓
Kaito Skill
 ↓
HTTP history / Replay
 ↓
Proxy
```

الفائدة:

```text
Visibility
History
Screenshots
Replay
POCs
Human collaboration
```

وفي الحلقة ذكروا أن من أعلى الأشياء قيمة أن تجعل Claude قادرًا على أخذ request من الـHTTP history وإعادة إنتاجه في proxy/replay environment. 

### بالنسبة لنا

إحنا عندنا:

```text
OpenCode
↓
Burp MCP
↓
Burp
```

فـ**المبدأ نفسه موجود عندنا بالفعل**، حتى لو الأداة مختلفة.

---

# 25. Skills ليست دائمًا مجرد Markdown

الفيديو يوضح أن Skill ممكن تكون:

```text
SKILL.md
```

فقط.

أو:

```text
SKILL.md
+
Scripts
+
Command line tools
+
References
```

يعني:

```text id="adg8xw"
Skill
├── instructions
├── binaries/scripts
├── helper code
└── references
```

والـAgent يستخدمها بحسب الحاجة.

---

# 26. Skill Frontmatter مهم جدًا

الـ`SKILL.md` لديه frontmatter يحتوي مثلًا:

```text
name
description
```

والـdescription يتم تحميله مبكرًا لتحديد:

> متى تستخدم هذه الـSkill؟

وده مهم جدًا.

يعني:

```text id="76u1dv"
Skill description
       ↓
Agent sees when/why to invoke
       ↓
Full skill loads when needed
```

المتحدثان يوضحان أن هذا **ليس RAG بالمعنى التقليدي للـembeddings search**.

هو mechanism متعلق بـskill loading.



---

# 27. Skills vs RAG

النقطة التي يجب تسجيلها:

```text
Skills
≠
Embedding-based RAG
```

حسب المتحدث، الـClaude Code skills يتم اكتشافها من metadata/frontmatter عند الحاجة، وليس بالضرورة باستخدام semantic vector retrieval.

---

# 28. Claude MD / Global Instructions

الـ`CLAUDE.md` أو equivalent project instructions يجب أن يحتوي على:

```text
Who am I?
What is my workflow?
What is my scope?
What tools do I use?
Where do I save things?
What should I avoid?
```

لكن لا يجب أن يكون ضخمًا بلا سبب.

الهدف:

```text
Contextual steering
```

وليس:

```text
1000-page methodology dumped into every request
```

---

# 29. Non-destructive Default

من القواعد العملية التي أكدوا عليها:

```text
Non-destructive actions by default
```

خصوصًا في Bug Bounty.

الـAgent يجب أن يعرف:

```text
Allowed testing
+
Owned accounts
+
Defined scope
```

وأن يتجنب:

```text
Destructive actions
```

إلا عندما يكون واضحًا أنها على موارد مملوكة ومخصصة للاختبار.



---

# 30. Persistent Hacking

من أكثر الأشياء التي ناقشتها الحلقة:

> كيف تخلي الـAgent يفضل شغال وأنت بعيد؟

أحد المتحدثين يقول إنه يكتب له ببساطة:

```text
I'm going to bed.
Don't ask me questions.
Don't stop hacking.
Keep going deeper.
```

وذكر أن هذا جعله يستمر **عدة ساعات** في بعض التجارب.

لكن ده **تجربة صاحب الحلقة**، وليس guarantee لسلوك ثابت عند كل نموذج أو framework. 

---

# 31. Notes مهمة للاستمرار بعد Compaction

لأن الـAgent قد يعمل:

```text
Compaction
```

لازم تكون عنده Notes عن:

```text
What I was doing
What I tried
What worked
What failed
What remains
```

بحيث يستطيع استعادة الـstate.

---

# 32. مشكلة Sub-Agent Explosion

دي نقطة مهمة جدًا.

المتحدث ذكر أن كثرة الـsub-agents قد تؤدي إلى مشاكل أثناء الـcontext compaction.

التجربة التي ذكرها:

```text
2–3 subagents
```

أفضل عند التشغيل الطويل من:

```text
4+
```

في بيئته وتجربته، لأن كثرة الـsubagents قد تؤدي إلى compaction/context problems تجعل استرجاع الجلسة أصعب. 

**دي ملاحظة تجريبية من المصدر، مش قاعدة عالمية.**

---

# 33. Local vs Remote Agent

المتحدث عنده 3 أنماط تقريبًا:

### Desktop Pair Hacking

```text
Claude Code
+
Terminal
```

### Remote VPS

```text
Claude-like Agent
+
Discord
```

### Automated Bot

```text
Discord
+
Persistent environment
```

الفكرة:

> نفس الـAgent workflow، لكن طرق الوصول إليه تختلف.

وده يوضح إن الـUI مش هو جوهر الـarchitecture.

---

# 34. Agent vs Folder

دي من **أهم أجزاء الحلقة بالنسبة لسؤالك السابق**.

هناك طريقتان:

## الطريقة الأولى: Agent

Agent متخصص لديه:

```text
Specific system prompt
Specific skills
Specific tools
```

مثال:

```text
Bug Bounty Agent
```

---

## الطريقة الثانية: Folder-scoped configuration

بدل ما تعمل Agent منفصل، تفتح Claude/OpenCode من:

```text
~/hunting/
```

ويتم تحميل config/skills المرتبطة بهذا المشروع.

والـsubfolder يمكن أن يضيف configuration خاصًا به.

يعني:

```text id="w9v6mq"
Home
└── Global config

Hunting/
└── Project-specific config

Target-A/
└── Target-specific config
```

والـAgent يورث configuration من الأعلى إلى الأسفل بحسب البيئة.



---

# 35. Target-Specific Folder

ودي النقطة التي جاوبت سؤالك السابق حرفيًا.

ممكن يكون عندك:

```text
targets/
├── target-a/
│   └── .claude/
│       └── ...
│
├── target-b/
│   └── .claude/
│       └── ...
```

وعندما تبدأ Agent من داخل:

```text
target-a/
```

يكون لديه:

```text
Global instructions
+
Hunting instructions
+
Target-A instructions
```

---

# 36. Automatic Target Bootstrap

الحلقة تقترح Workflow جميل:

```text
I want to hack Target X
        ↓
Create target folder
        ↓
Fetch program policy
        ↓
Store scope
        ↓
Write target-specific config
        ↓
Launch Agent
```

وبالتالي لا تحتاج كل مرة أن تشرح للـAgent:

```text
scope
policy
target context
```

من الصفر. 

---

# 37. أفضل Skills المقترحة في الحلقة

الأمثلة التي تم ذكرها:

```text
Kaito mode
Session search
Zero-day research
Report writer
Scope / H1 brain
Validator
Recon pipelines
```

والمعنى المهم:

**ليس كل Skill لازم تكون vulnerability-specific.**

بعض أهم الـSkills هي infrastructure skills:

```text
Search
Memory
Scope
Reporting
Proxy
Session retrieval
Automation
```

---

# 38. Skill Pipeline

مثال pipeline:

```text
Subdomains
   ↓
HTTPX
   ↓
Fuzz
   ↓
Collect results
   ↓
Store output
```

هنا Skill تكون مجرد:

```text
Contextual automation wrapper
```

بدل كتابة نفس pipeline يدويًا في كل جلسة.

---

# 39. Final Recommended Architecture From This Video

```text id="p0ra9g"
                 GLOBAL
                   │
           ┌───────┴────────┐
           │                │
      General Config      Skills
           │                │
           └───────┬────────┘
                   │
                HUNTING
                   │
          ┌────────┴────────┐
          │                 │
       Scope             Notes
          │                 │
          │       ┌─────────┼─────────┐
          │       ▼         ▼         ▼
          │     Leads   Primitives Findings
          │                             │
          │                             ▼
          │                           Reports
          │
          ▼
      TARGET FOLDER
          │
          ▼
      Target Context
          │
          ▼
       AI Agent
          │
    ┌─────┴─────┐
    ▼           ▼
Hardcoded    Free-run
Workflow     Exploration
    │           │
    └─────┬─────┘
          ▼
      Compare
          ↓
   Improve Workflow
          ↓
      Validator
          ↓
       Report
```

---

# 40. أهم 20 Rule من الحلقة

```text id="8ncy4m"
1. Skills are not model training.
2. Use skills when the model lacks knowledge.
3. Use skills when you need a specific execution method.
4. Do not over-constrain the model.
5. Give skills fallback paths.
6. Let the agent explore if the prescribed method fails.
7. Hardcode methodology when determinism matters.
8. Also run free-form exploration to discover new techniques.
9. Compare hardcoded vs free-form results.
10. Feed useful discoveries back into the methodology.
11. Maintain a structured note hierarchy.
12. Separate notes, leads, primitives, findings, and reports.
13. Use a skeptical validator.
14. Give the validator real examples.
15. Don't let AI inflate severity.
16. Integrate proxy/replay visibility into the workflow.
17. Keep scope and operational rules in persistent instructions.
18. Make long-running agents maintain strong state/notes.
19. Avoid excessive sub-agent fan-out.
20. Use target-specific folders/configuration for isolation.
```

---

# 41. أهم شيء بالنسبة لمشروعنا إحنا

الفيديو ده عدّل نقطة كنا ممكن نقع فيها بسهولة:

كنا ممكن نبني:

```text
AGENTS.md
+ 70 Skills
+ rigid methodology
+ multi-agent
```

ونقول:

> "كده الـAI بقى Hunter."

لكن الحلقة بتقول حاجة أدق:

```text
                    AGENT
                       │
          ┌────────────┴────────────┐
          │                         │
   Deterministic                   Freedom
      Workflow                  Exploration
          │                         │
          └────────────┬────────────┘
                       ▼
                    Compare
                       ↓
                 Learn / Improve
```

**وده في رأيي من أهم الحاجات اللي أضافتها الحلقة للسلسلة كلها.**

لأنك مش عايز Agent يقلدك فقط.
إنت عايزه:

> **ينفذ الـmethodology اللي أنت واثق فيها، وفي نفس الوقت يكتشف حاجات أنت ما كتبتهاش، وبعدها تستفيد من اكتشافاته لتطوير الـmethodology.**

وده بالضبط النوع من الـloop اللي يخلي النظام يتحسن مع الوقت بدل ما `AGENTS.md` يفضل حجرًا مقدسًا عمره 6 شهور. 😂

