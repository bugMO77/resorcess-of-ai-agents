## تلخيص الساعة الأولى

---

### من هو المتكلم وما السياق؟

أحمد الحني، bug bounty hunter، بيشارك تجربة **3 شهور** من العمل الفعلي على AI agent للهانت. مش نظرية، تجربة حقيقية بمشاكل حقيقية.

---

### الـSetup اللي وصل له

```text
Strix (fork معدّل)
    ↓
Cloud Model (مش Ollama)
    ↓
Browser (مهم جداً)
    ↓
Shell + Tools
    ↓
Target
```

بدأ بـ Kai → فشل (مفيش browser، مفيش chat أثناء scan)  
انتقل لـ Strix → لقى فيه كل اللي يحتاجه

---

### الأخطاء الحقيقية اللي وقع فيها

**الخطأ الأول: System prompt فاض**

```text
System prompt وصل 10,000 سطر
↓
بدأ يضيف skills بدون تفكير
↓
النتيجة: context ضخم + أداء وحش
```

**الخطأ الثاني: الـAgent بيmiss حاجات**

```text
عنده 10 JS files + 20 UI section
↓
يمسك 2 files + 3 sections
↓
يكمل ويسيب الباقي
السبب: context window امتلأ
```

**الخطأ الثالث: فكرة الـSub-agents الغلط**

```text
فكر: لقيت 3 JS files → افتح 3 agents موازيين
↓
المشكلة: لما بتبعت بيانات كتير لموديل مرة واحدة
هو بيmiss ويختار هو إيه اللي يعمله
↓
الـRoot agent مش اتعمل orchestrator بشكل صح
```

---

### الحل الصح اللي وصل له: الـLedger

```text
بدل ما Agent يحاول يفتكر كل حاجة من الـcontext
↓
يكتب في files خارجية:
- JS files المكتشفة
- Endpoints المستخرجة
- UI sections
- Cookies + session data
↓
لما يحتاج حاجة → يقرأ من الـfile مش من الـcontext
↓
النتيجة: context ظل خفيف + مفيش missing
```

المنطق:

```text
Ledger file مهما كبر
مستحيل يوصل لـ1M token
لكن الـcontext ممكن يمتلئ بسهولة
```

---

### الـSkill Files: الفكرة الجوهرية

الـAgent بدون skill = موديل بيقولك payloads عامة

```text
بدون skill:
"اعمل XSS bypass"
→ URL encoding, double encoding, etc.
→ عام جداً، ناقص كود، ناقص context
```

```text
مع skill file:
نفس السؤال
→ methodology كاملة
→ أمثلة حقيقية
→ references
→ edge cases
```

**المبدأ:** الـSkill مش payload dump، هو methodology مكتوبة بطريقة الـAI يفهمها.

---

### Testing Methodology اللي اتكلم عنها

```text
1. Load target in browser (مش curl)
2. Map ALL UI sections (مش بس JS files)
3. Extract endpoints من كل JS file
4. سجّل كل ده في ledger
5. Test endpoint by endpoint من الـledger
6. مش من الـcontext
```

نقطة مهمة جداً قالها:

> "اللي بتعمله بدماغك وبإيدك مش هينفع تقول للـAI اعمله بنفس الطريقة، لازم تترجمه بطريقة الـAI يفهمها"

---

### Key Takeaways من الساعة دي

|المشكلة|الحل|
|---|---|
|Agent بيmiss items|Ledger files خارج الـcontext|
|Context بيمتلئ|Compressor + تسجيل مستمر|
|Sub-agents فوضى|Orchestrator ينظم، مش يفتح كل حاجة مرة واحدة|
|Model بدون direction|Skill files بـmethodology واضحة|
|Browser vs curl|دايماً browser في SPAs|

---

### اللي لسه جاي في الـ2.5 ساعة الجاية

بناءً على الـtranscript لقيت إشارات لـ:

- Triggers (اتكلم عنها في الدقيقة الأخيرة)
- Email field attacks
- System prompt الفعلي بتاعه
- Live scan demo

---

**ابعت الساعة التانية.** الـtriggers والـskill files العملية هي الجزء الأهم.


## تلخيص الساعة التانية

---

### المحور الرئيسي: كيف تخلي الـAgent يعرف إمتى وإيه يتست؟

---

### 1. الـTriggers بالتفصيل

الـTrigger مش بس "لما تلاقي XSS تعمل كذا"، هو نظام كامل.

**أنواع الـTriggers:**

```text
Phase Trigger     → تسلسل خطوات ثابتة من أول الـscan
Context Trigger   → لو حصل X اعمل Y
Keyword Trigger   → بارامتر اسمه "url" → test SSRF + Redirect + LFI
Event Trigger     → لما يحصل action معين
Schedule Trigger  → بعد ساعتين من الـscan ابدأ كذا
Condition Trigger → لو الـresponse كذا افعل كذا
```

**الـPhase Trigger بالتفصيل:**

```text
Phase 0: Recon
  → Map robots.txt، sitemap، كل URL
  → سجّل في الـledger

Phase 1: JS Analysis + IDOR
  → حلّل كل JS file
  → استخرج endpoints
  → test IDOR في كل endpoint

Phase 2: JS Endpoints Deep Test
  → IDOR, SSRF, server-side issues

Phase 3: Parameter Testing
  → Bypass, pollution, encoding

Phase 4: UI Sections
  → كليك في كل حاجة
  → سجّل كل action
```

**مشاكل الـPhase Trigger:**

```text
عيب: Agent مش بيوقف لما يلاقي SQL error
يكمل على نفس الخطة
الحل: Context Trigger جوه الـPhase
```

---

### 2. الـFunction-Centric: الفكرة الجوهرية

```text
بدل ما Agent يفتح الموقع ويتست كل حاجة
↓
كل function يتست لوحدها على حدة

مثال:
Settings page جوا:
  - Change name    → function مستقلة
  - Change email   → function مستقلة
  - Change password → function مستقلة
  - Delete account → function مستقلة
```

**ليه ده أهم من الـUI Sections؟**

```text
UI Section: "Settings"
Function: "Change Email"

الـAgent لو اتعامل مع الـUI Section
هيشوف الصفحة كلها كوحدة واحدة
هيmiss تفاصيل داخل كل function

لو اتعامل مع كل function لوحدها
هيعمل test كامل لكل workflow
```

---

### 3. الـSkill Files: الفرق الحقيقي

الـمقارنة اللي وضّحها:

```text
بدون Skill:
"اعمل XSS bypass"
→ URL encoding
→ double encoding
→ generic payloads
→ ناقص كود فعلي
→ ناقص edge cases

مع Skill File:
نفس الطلب
→ methodology كاملة
→ كيف تـvalidate الـreflection
→ payloads مرتبة بالسياق
→ كيف تـconfirm مش false positive
→ references حقيقية
```

**مثال Keyword Trigger + Skill:**

```text
بارامتر اسمه "url" أو "redirect"
↓
Trigger يشتغل
↓
يلود 3 skills: SSRF + Open Redirect + LFI
↓
يبدأ يتست الـ3 بـmethodology كاملة
```

---

### 4. مشكلة الـFalse Positives والحل

**المشكلة:**

```text
Agent بيبعت request
يقرأ الـresponse
يقول "vulnerable"
أنت تبعت نفس الـrequest يدويًا
→ مختلف تمامًا
→ Agent كان بيهلّس
```

**الحل الصح:**

```text
Agent لما يعتقد إنه وجد vulnerability
↓
يكتب Python/Bash script
↓
الـscript يبعت نفس الـrequest بالضبط
↓
الـscript يـcheck الـresponse بـcondition محددة
↓
لو الـscript قال valid → valid
لو قال false → skip
```

---

### 5. مشكلة الـHTTP Methods

```text
Agent يلاقي endpoint ترجع 405 Method Not Allowed
↓
الغلط: يـskip الـendpoint
↓
الصح: يجرب methods تانية
→ OPTIONS أولاً يشوف إيه المسموح
→ يجرب الكل
→ PUT, PATCH, DELETE, HEAD
```

رول صغيرة في الـsystem prompt حلّت المشكلة دي.

---

### 6. القرار المهم: إيه اللي الـAgent يتست وإيه اللي لأ

أهم نقطة في الساعة دي:

```text
❌ Injections (SQL, XSS, etc.)
   - مكلفة جداً بالـtokens
   - الـAgent هيعبط فيها
   - في tools متخصصة أحسن منه فيها

✅ الـAgent مناسب لـ:
   - Broken Access Control
   - IDOR
   - Business Logic
   - JS Analysis + Endpoint Discovery
   - Information Disclosure
   - SSRF (في حالات معينة)
```

**السبب المنطقي:**

```text
Injections محتاجة:
→ آلاف الـrequests
→ pattern matching معقد
→ أدوات زي sqlmap أو dalfox أحسن بمراحل

Broken Access Control محتاجة:
→ فهم الـcontext
→ تجربة roles مختلفة
→ قراءة الـresponse بذكاء
← هنا الـAgent يتفوق
```

---

### 7. الـLedger Structure الكاملة

```text
workspace/
├── js_files.json
│   └── {file: "main.js", analyzed: true, endpoints: [...]}
├── endpoints.json
│   └── {endpoint: "/api/users", method: "GET", tested: false}
├── ui_sections.json
│   └── {section: "Settings", visited: true, functions: [...]}
├── session_data.json
│   └── {cookies: {...}, tokens: {...}}
└── findings.json
    └── {endpoint: "...", finding: "...", validated: true/false}
```

---

### 8. الدرس الأكبر في الساعة دي

> "اللي بتعمله بدماغك وبإيدك وإنت بتتست مش هينفع تقول للـAI اعمله بنفس الطريقة. لازم تترجمه بطريقة الـAI يفهمها."

يعني:

```text
Human Tester:
"أنا شايف endpoint /api/users
عندي experience إن ممكن يكون فيه IDOR
هجرب أغير الـID"

AI Agent يحتاج:
"لو endpoint فيه parameter اسمه id أو user_id
جرب قيم IDs تانية
قارن الـresponse
لو رجعت data مختلفة → possible IDOR
اكتبه في الـledger واعمل validation"
```

---

### Key Takeaways من الساعة التانية

|الموضوع|الخلاصة|
|---|---|
|Triggers|Phase + Context + Keyword = أقوى combination|
|Function-Centric|كل function تتتست لوحدها|
|False Positives|Script يـvalidate، مش الـAgent|
|HTTP Methods|405 مش نهاية، جرب methods تانية|
|Injections|متخليش الـAgent يضيع وقته فيها|
|Skill Files|Methodology، مش payload dump|

---

**ابعت الساعة التالتة.** على الأرجح فيها الـsystem prompt الفعلي والـlive demo.


## تلخيص الساعة التالتة

---

### المحاور الرئيسية في الساعة دي

---

### 1. مشكلة الـScope والـThird-Party Domains

```text
Target: app.com
↓
اللوجن بيحصل على: auth.thirdparty.com
↓
الـAgent بيتوه
مش عارف يحلل الـflow
```

**الحل:**

```text
خلّيه يلوجن دايماً بالـBrowser مش بـcurl
Browser بيتعامل مع الـredirects تلقائياً
```

---

### 2. مشكلة الـSwagger/API Docs

```text
Agent لقى Swagger
↓
اعتبره الـsource of truth الوحيد
↓
تجاهل JS files + UI sections
↓
فاته endpoints مش موجودة في الـSwagger
```

**الرول اللي أضافه:**

```text
Swagger = نقطة بداية مش نهاية
لازم تكمل بـJS analysis + UI browsing
```

---

### 3. الـLive Demo: إيه اللي اتعمل فعلاً

```text
Workspace structure:
├── shared_data/
├── screenshots/
├── js_files/      ← 14 file, كلهم pending
├── function_attacks/
├── state/
└── condition_scripts/
```

**الـFlow اللي شافوه:**

```text
Root Agent (Orchestrator)
↓
فتح Browser → Login ✓
↓
Download 14 JS files → Ledger
↓
فتح Sub-agent لـJS Analysis
↓
Sub-agent تاني لـUI Sections
↓
كل section تتست لوحدها
```

---

### 4. أهم سؤال واجوبته: إيه الموديل المناسب؟

**سؤال: نستخدم local model نوفر tokens؟**

```text
الإجابة: لأ

Local model → 3-4 دقايق لكل response
الـscan هياخد أيام مش ساعات

الأفضل:
DeepSeek V4 Pro → رخيص جداً + كويس
Qwen models على OpenRouter → free tier موجود
Claude → الأقوى بس الأغلى
```

**المعادلة الحقيقية:**

```text
مش Quality vs Quantity
هو: هل الـuse case يحتاج ذكاء عالي؟

Broken Access Control testing → أي موديل معقول يكفي
Complex business logic → هنا تحتاج الأقوى
```

---

### 5. سؤال مهم: Payloads في الـSkill File أم لأ؟

الإجابة بالتفصيل:

```text
❌ الغلط:
Skill file = قائمة payloads طويلة
→ Agent يجرب كل payload
→ توكنز تضيع
→ context يمتلئ

✅ الصح:
Skill file = methodology
→ "لو لقيت reflection، جرب encoding bypass"
→ "لو عندك parameter اسمه url، test SSRF"
→ الـAgent يفكر ويختار الـpayload المناسب
```

**والأذكى:**

```text
بدل ما تكتب payloads بإيدك
روح لـ4-5 models مختلفة
اسألهم يكتبوا لك skill file للـXSS مثلاً
اجمع الردود
فلترها
النتيجة أقوى من اللي تكتبه لوحدك
```

---

### 6. سؤال: نعمل Agent منفصل لكل vulnerability؟

```text
السؤال: Agent للـXSS + Agent للـSQLi + Agent للـIDOR؟

الإجابة: لأ بالمرة

ليه غلط:
→ كل Agent هتقابل فيه مشاكل مختلفة
→ هتضيع وقت تحل مشاكل كل واحد
→ مالوش لازمة

الصح:
→ Agent واحد + Skill Files منفصلة
→ Triggers تلود الـSkill المناسب في الوقت المناسب
```

---

### 7. سؤال: Injections في الـAgent؟

```
الإجابة الصريحة:
"أنا مش بحب الـAI يتست Injections"

السبب:
→ هياكل tokens كتير جداً
→ هيكون متعب
→ في tools متخصصة أحسن منه
   (sqlmap, dalfox, nuclei)

الـAgent يتفوق في:
→ Broken Access Control
→ IDOR
→ Business Logic
→ JS Analysis
```

---

### 8. False Positives: الحل النهائي

```text
المشكلة: Agent بيقول "vulnerable" وهو مش كده

الحل في 3 خطوات:
1. Agent يعتقد إن في vulnerability
2. يكتب Python script يعمل نفس الـrequest
3. الـScript يـcheck الـresponse بـcondition محددة
   → Script قال valid → valid ✓
   → Script قال false → ignore ✓
```

---

### 9. نقطة مهمة: إزاي تـdebug الـAgent

```text
مش بتبعت المشكلة للـAI وتقوله "صلّحها"

الطريقة الصح:
1. أنت تحدد المشكلة بنفسك
   → "بيmiss UI section X"
2. تفهم السبب
   → "Context امتلأ فنسي"
   → "الـBrowser ما عملش load للـsection"
   → "موديل ما قراش كل الـsections"
3. بعد ما تفهم السبب
   → تاخد الـAI معك تناقش الحل
   → مش ترميله المشكلة
```

---

### الـInsights الختامية

|الموضوع|الخلاصة|
|---|---|
|Swagger|نقطة بداية، مش كل شيء|
|Local Models|بطيء جداً، مش يستاهل|
|Skill Files|Methodology مش payloads|
|Agent منفصل لكل vuln|فكرة غلط|
|Injections|خليها لـtools متخصصة|
|False Positives|Script يـvalidate|
|Debug|افهم السبب الأول، بعدين اسأل الـAI|

---

**ابعت الـ34 دقيقة الأخيرة.** على الأرجح فيها خلاصة + Q&A مهمة.


## تلخيص الـ34 دقيقة الأخيرة

---

### المحاور الرئيسية

---

### 1. الـLive Demo: اللي اتشاف فعلاً

```text
النتائج الحقيقية للـscan:
- Total endpoints: 189
- Tested: 107
- Valid finding: IDOR في cross-tenant data
- False positives: SSRF (مبلغها من قبل)
- Swagger exposure: low impact
```

**الـIDOR اللي اتأكد:**

```text
Request بـtoken أحمد
→ /api/company/{id}
→ رجع data شركة رقم 99

Request بـtoken عبده
→ نفس الـendpoint
→ رجع نفس الـdata ✓ IDOR confirmed
```

---

### 2. درس مهم جداً من الـDemo

```text
Agent بلّغ: "SSRF confirmed"
أحمد جرب نفس الـrequest يدوياً
→ مختلف تماماً
→ Agent كان بيهلّس

الدرس:
كل finding لازم تراجعه بنفسك
مش تبلّغ على طول
```

---

### 3. سؤال الـ43,000 Subdomain

```text
السؤال: حد قال بيشغل Agent على 43,000 subdomain؟

الإجابة:
→ هيشتغل، مش هيرفض
→ لكن مش هيدي أحسن performance
→ Context هيمتلئ
→ هيتوه من اللي عمله

الصح:
Agent على target واحد محدد
أنت تفهمه أولاً
بعدين تشغله
```

---

### 4. سؤال: هل الـAI بياخد مكان الـHunter؟

الإجابة الصريحة:

```text
مستحيل ياخد مكانك لو انت شغال

السبب:
أول باج أحمد جابها → بعد 4-5 شهور
Agent مش هيصبر 4-5 شهور يفكر

اللي الـAgent مش بيعمله:
→ يقعد يومين يفكر في حاجة
→ يفهم الـbusiness context
→ يلاحظ الـundocumented behavior
→ يعرف يـreport بشكل كامل

اللي بيعمله:
→ يزود سرعتك
→ يكتشف حاجات كنت هتفوتك
→ يغطي surface أكبر
```

---

### 5. نقطة مهمة: لازم تفهم الـTarget قبل ما تشغله

```text
المشكلة اللي حصلت:
Agent جاب IDOR
Triager قال: "وريني ازاي تعملها في الـUI"
أحمد: "أنا مش اشتغلت على الـUI أصلاً"

الدرس:
مش تشغل الـAgent على target وانت ما فهمتوش
لأن:
→ هيجيب findings
→ مش هتقدر تـreport صح
→ مش هتقدر تـreproduce
→ Triager هيغلق الـreport
```

---

### 6. نصيحة الختام: الـWatching مهم

```text
مش تشغله وتنام من أول يوم

الـprocess الصح:
1. شغله على target اشتغلت عليه قبل
2. راقب هو بيعمل إيه
3. قارن بـfindings أنت جبتها
4. لما تتأكد إنه بيشتغل صح
5. وقتها تقدر تشغله وتنام
```

---

### الـRoadmap اللي اقترحه للبداية

```text
مافيش roadmap ثابت

اللي يساعدك:
→ قرا writeups وبلوجز
→ قعد مع الـAgent وراقبه
→ كل ساعة مع الـAgent = خبرة حقيقية
→ مش محتاج تكتب كود
→ محتاج تفهم methodology
```

---

## الخلاصة الكاملة للـ4 ساعات

```text
المعادلة النهائية:

Hunter يفهم الـtarget
↓
+ Ledger يحفظ كل حاجة
↓
+ Skill Files بـmethodology واضحة
↓
+ Triggers تلود الـskill الصح في الوقت الصح
↓
+ Script يـvalidate كل finding
↓
+ أنت تراجع وتـreport
=
Agentic Hunting فعّال
```

---

### الـChecklist النهائية

```text
قبل ما تشغل:
□ اشتغل على الـtarget مانوال الأول
□ افهم الـbusiness logic
□ عمل Ledger structure
□ كتبت Skill files لأهم vulnerabilities
□ حددت الـtriggers

أثناء الـscan:
□ راقب الـlogs
□ تأكد الـJS files اتحللت كلها
□ تأكد الـUI sections اتزارت كلها
□ validate كل finding قبل ما تبلّغ

بعد الـscan:
□ راجع كل finding بنفسك
□ اتأكد من الـimpact
□ ابني الـreport صح
```
