تمام. هنعتبر ده **مصدر جديد مستقل من Critical Thinking / Bug Bounty Podcast**، ونطلعه بنفس نظام الـNotes الثابت اللي اتفقنا عليه. الفيديو ده مختلف عن فيديوهات بناء الـAI Hunter، لأنه **Deep Dive أمني في MCP نفسه**، وده مهم جدًا لنا لأننا بالفعل شغالين بـMCP بين OpenCode وBurp.

# Critical Thinking - Bug Bounty Podcast

## MCP Security Deep Dive

> **نوع الفيديو:** RFC / Protocol Security Deep Dive
> **الموضوع:** Model Context Protocol (MCP)
> **الهدف:** فهم Architecture + Communication + Authentication + Objects + Attack Surface من منظور أمني.
> **ملاحظة المصدر:** المتحدث يوضح أنه قضى حوالي 16 ساعة في البحث عن البروتوكول، ولا يقدّم نفسه كخبير MCP، وأن بعض النقاط هي security hypotheses مبنية على قراءة الـspec وتجارب محدودة وليست threat model شاملًا. 

---

# 1. Main Idea

الفكرة الأساسية:

**MCP ليس مجرد "طريقة لتوصيل Tool بالـLLM".**

هو **Protocol** بين:

```text
MCP Client
      ↕
   JSON-RPC
      ↕
MCP Server
```

ومع وجود:

```text
Authentication
Capabilities
Tools
Resources
Prompts
Sampling
Elicitation
Roots
MCP Proxies
```

فإن الـMCP نفسه أصبح **Attack Surface مستقلة**.

الفيديو يحاول تعليم الباحث أن يتعامل مع MCP بنفس الطريقة التي يتعامل بها مع أي Protocol:

```text
Understand Spec
      ↓
Understand Messages
      ↓
Understand Trust Boundaries
      ↓
Understand Objects
      ↓
Identify Security Boundaries
      ↓
Test Implementation
```



---

# 2. MCP Architecture

العناصر الأساسية التي يركز عليها:

```text
MCP Client
     │
     │ JSON-RPC
     │
MCP Server
```

وقد توجد عناصر إضافية:

```text
MCP Client
MCP Server
MCP Proxy
Authentication / Authorization Server
```

لكن الفيديو يركز أساسًا على Client وServer. 

---

# 3. Communication Protocol

التواصل يعتمد على:

```text
JSON-RPC
```

وده يجعل الـprotocol:

* readable
* inspectable
* relatively easy to implement
* interesting من ناحية الـprotocol testing

المتحدث ذكر أنه استطاع بناء MCP client/server بسيطين بـPython عن طريق قراءة وكتابة الرسائل مباشرة. 

---

# 4. Transport Mechanisms

الفيديو يذكر ثلاث طرق أساسية:

```text
STDIO
Streamable HTTP
Older HTTP Server-Sent Events
```

المتحدث لديه خبرة أكبر مع STDIO، ويقول إن خبرته بالـHTTP transports أقل.

### بالنسبة لنا

دي نقطة مهمة لأن setup بتاعنا الحالي:

```text
OpenCode
 ↓
mcp-proxy
 ↓
Burp MCP :9876
```

يعني الـMCP مش مجرد concept نظري عندنا، إحنا بالفعل عندنا Transport layer لازم نفهمها.



---

# 5. Authentication

الفيديو يعرض OAuth كأحد mechanisms المستخدمة في MCP.

النقطة التي لفتت انتباه المتحدث:

### Dynamic Client Registration

هو يشير إلى أن الـspec تشجع دعم:

```text
OAuth 2 Dynamic Client Registration
```

ويرى أن هذه الوظيفة قد تفتح attack surface مثل SSRF في بعض implementations.

**مهم:** ده طرح أمني من المتحدث، وليس معناه أن كل MCP server فيه SSRF بسبب Dynamic Client Registration.



---

# 6. Authentication Security Checks

المتحدث يركز على:

```text
HTTPS
+
Redirect URI restrictions
+
Localhost handling
```

ويشير إلى أن أي logic من نوع:

```text
Is this localhost?
```

يستحق التدقيق.

الفكرة ليست أن `localhost` vulnerability بحد ذاته، وإنما:

```text
Validation logic
      ↓
Parsing / encoding / normalization
      ↓
Potential discrepancy
```

يعني سؤال الباحث هنا:

> "كيف يحدد التطبيق أن هذا URI فعلًا localhost؟"

وليس:

> "هل localhost خطير؟"



---

# 7. Initialization Flow

دي من أهم أجزاء الفيديو.

عند بدء الاتصال:

```text
Client
  ↓
initialize RPC
  ↓
Server
  ↓
initialize response
```

الـClient يرسل معلومات مثل:

```text
Protocol Version
Capabilities
Client Info
```

والـServer يعيد:

```text
Protocol Version
Capabilities
Server Info
Instructions
```



---

# 8. Capabilities

الـCapabilities تخبر الطرف الآخر:

> "أنا أقدر أعمل إيه؟"

المتحدث يذكر Client capabilities مثل:

```text
Roots
Sampling
Elicitation
```

مع خصائص مثل:

```text
subscribe
listChanged
```

ومن ناحية Server capabilities توجد أشياء مثل:

```text
Logging
Prompts
Resources
Tools
Utilities
```

والـCapabilities مهمة لأنها تحدد **سطح التفاعل** بين الطرفين.



---

# 9. `instructions` في Initialization Response

دي من أهم النقاط في الفيديو.

الـMCP server يرسل:

```text
instructions
```

والمتحدث يلفت النظر إلى أن الـclient قد يدمج هذه التعليمات مع الـsystem prompt أو context بحسب implementation.

من منظور أمني:

```text
Malicious MCP Server
        ↓
instructions
        ↓
Client Context
        ↓
Potential Prompt / Tool Hijacking
```

إذن أثناء تقييم MCP implementation:

> **لا تركز فقط على Tools. افحص أيضًا ما يتم إدخاله من server إلى model context أثناء initialization.**



---

# 10. MCP Client Objects

الفيديو يقسم أهم client-side objects إلى:

```text
Roots
Sampling
Elicitation
```

وهذه requests يمكن للـServer استخدامها للتفاعل مع الـClient.

---

# 11. Roots

الفكرة:

> الـClient يمكنه expose filesystem roots للـServer.

بالتالي:

```text
MCP Client
    ↓
Roots
    ↓
Filesystem locations
```

الـServer يستطيع طلب:

```text
roots/list
```

للحصول على الـroots التي يعرّفها الـClient.

## Security significance

لو كنت تختبر **Client** باستخدام malicious MCP server، فالسؤال:

```text
What filesystem roots does this client expose?
```

وقد ينتج عن ذلك:

* Path disclosure
* Environment disclosure
* Sensitive directory disclosure

بحسب implementation.

المتحدث يقترح تحديدًا اختبار ما إذا كان Client يعيد roots حتى في الحالات التي لا يظهر فيها الدعم بوضوح.



---

# 12. Sampling

Sampling يسمح للـMCP server بطلب:

```text
LLM interaction
```

بمعنى أن Server قد يطلب من الـClient أن يجعل الـLLM يولّد message / response.

وده بيخلق Trust Boundary قوية:

```text
Malicious MCP Server
        ↓
Sampling Request
        ↓
LLM
        ↓
User / Tools / Other Actions
```

المخاطر التي يطرحها المتحدث تشمل:

* Prompt injection.
* Tool invocation.
* Resource exhaustion.
* Chained actions.
* Malicious server influencing model behavior.

والـspec نفسها تشدد على ضرورة وجود **human in the loop** في sampling.



---

# 13. Elicitation

Elicitation يسمح للـMCP Server بأن يطلب معلومات من المستخدم.

يعني:

```text
MCP Server
     ↓
Elicitation
     ↓
User
     ↓
Information
```

الـspec كما ينقل الفيديو تقول إن الـserver لا يجب أن يستخدم elicitation لطلب معلومات حساسة.

لكن من ناحية security testing، المتحدث يهتم جدًا بفكرة:

```text
Auto-accepted elicitation
```

أي:

```text
Server asks
      ↓
Client already has information
      ↓
Client automatically returns it
```

بدون تدخل المستخدم.

وده ممكن يحول الـelicitation من:

```text
User interaction
```

إلى:

```text
Information extraction primitive
```



---

# 14. Invisible Prompt Smuggling

المتحدث يذكر إمكانية استخدام:

```text
Invisible Unicode
```

في المحتوى الذي يراه المستخدم أو الـUI.

الفكرة:

```text
Visible text
+
Invisible instructions
```

وقد يؤدي ذلك إلى:

```text
UI deception
Prompt injection
Tool hijacking
```

النقطة الأهم هنا:

> لا تختبر الـbackend فقط، اختبر أيضًا **كيف يعرض الـClient المحتوى القادم من MCP**.



---

# 15. MCP Server Objects

المتحدث يركز على أهم عنصرين:

```text
Tools
Resources
```

بينما يترك:

```text
Prompts
Utilities
```

كجزء للبحث الإضافي.

---

# 16. Tools

MCP server يمكنه تعريف Tools.

الـClient أولًا يعمل:

```text
tools/list
```

ثم الـServer يعيد definitions.

بعدها Client يمكنه إرسال:

```text
tools/call
```

لاستدعاء Tool.

الـTool schema يحتوي على عناصر مثل:

```text
name
title
description
inputSchema
outputSchema
annotations
```



---

# 17. Tool Naming Confusion

دي من **أهم نقاط الفيديو**.

الـTool يمكن أن يكون لديه أسماء/تسميات في أكثر من مكان:

```text
name
title
annotation title
```

ثم توجد:

```text
Namespaces
```

لمنع collisions بين عدة MCP servers.

المشكلة الأمنية:

```text
What user sees
        ≠
What backend invokes
```

مثال conceptual:

```text
Displayed:
"Safe Search Tool"

Actual:
"dangerous_internal_tool"
```

لو الـUI والـbackend يستخدمان identifiers مختلفة، ممكن يظهر:

```text
Tool Hijacking
Tool Confusion
Name Collision
```



---

# 18. Tool Descriptions

الـTool description نفسه مهم.

لو الـClient يضع:

```text
Tool Description
```

داخل:

```text
System Prompt / Model Context
```

فقد يصبح:

```text
MCP Server
   ↓
Malicious Description
   ↓
Model Context
   ↓
Tool Selection Manipulation
```

وده أحد أشكال:

```text
Tool Poisoning / Tool Hijacking
```

إذن أثناء الاختبار:

> **لا تفحص tool behavior فقط، افحص metadata التي تصف الـtool.**



---

# 19. Tool Responses

الـTool قد يرجع أنواع مختلفة من المحتوى، منها:

```text
Text
Image
Audio
Resource Links
Structured Data
```

وده يفتح طبقة ثانية من الـattack surface:

```text
MCP Server
   ↓
Tool Response
   ↓
MCP Client Rendering / Processing
```

فالسؤال ليس فقط:

> "هل الـTool آمن؟"

بل:

> "ماذا يحدث عندما يعرض الـClient الـoutput؟"



---

# 20. Namespace Collisions

الـnamespace هدفه منع:

```text
Server A:
search

Server B:
search
```

من أن يصبحا ambiguous.

لكن المتحدث يشير إلى احتمال مشاكل لو كان بناء الـfully-qualified name غير واضح.

مثال conceptual:

```text
server__tool
```

ولو اسم الـtool نفسه يحتوي على delimiter.

النتيجة المحتملة:

```text
Ambiguous parsing
Name collision
Wrong tool invocation
```

دي نقطة protocol/parser testing أكثر من كونها مجرد vulnerability category.



---

# 21. User Confirmation Problem

لو الـClient يعرض للمستخدم:

```text
"Do you approve this tool call?"
```

فالـLLM أو الـUI قد يشارك في اتخاذ القرار.

المتحدث يرى إن جعل الأمن معتمدًا على:

```text
LLM/User confirmation
```

بدون safeguards قوية يمثل **security architecture concern**.

يعني:

```text
Don't assume confirmation UI = security boundary.
```



---

# 22. MCP Proxies

الـMCP Proxy:

```text
Multiple MCP Servers
        ↓
      Proxy
        ↓
Single MCP Server interface
```

يعني الـProxy يلعب دور:

```text
Client + Server
```

في نفس الوقت.

وده يخلق:

```text
More abstraction layers
+
More parsers
+
More schemas
+
More trust boundaries
```

المتحدث ذكر حالات محتملة لـDoS نتيجة اختلافات في التعامل مع schemas/types بين الأطراف.



---

# 23. Resources

ودي من **أهم أجزاء الفيديو بالكامل**.

MCP Servers يستطيعون expose:

```text
Resources
```

التي قد تكون:

```text
Files
HTTP URLs
Git repositories
Application data
Other context
```

ثم الـClient يمكنه طلب:

```text
resources/list
```

ثم:

```text
resources/read
```

بحسب الـURI.



---

# 24. لماذا Resources خطيرة؟

لأن الـResource URI قد يمثل:

```text
file://...
https://...
git://...
```

فمن منظور Security Testing:

```text
Resource URI
      ↓
URI parsing
      ↓
Path resolution
      ↓
File / Network / Git operation
```

كل طبقة من دول attack surface.

---

# 25. Resource Templates

دي من أكثر الأفكار التي ركز عليها المتحدث.

هناك:

```text
resources/templates/list
```

والـtemplate يحتوي variables يتم إدخالها داخل URI.

فإذا كان implementation سيئًا:

```text
Template
   ↓
User-controlled variable
   ↓
URI construction
   ↓
Path handling
```

يصبح من الضروري اختبار:

```text
Path traversal
Arbitrary file access
URI confusion
Normalization issues
```

المتحدث يعتبر Resource Templates من الأماكن ذات القيمة العالية في الاختبار. 

---

# 26. Git Resources

النقطة الغريبة التي لفتت انتباهه:

```text
git
```

موجود ضمن schemes المتعلقة بالـResources.

وهو يرى أن Git له attack surface واسع، مثل:

* repository behavior
* submodules
* hooks
* filesystem interaction

لكن النقطة العامة الأهم:

> **مجرد إضافة scheme جديد تعني إضافة parser + handler + trust boundary جديدة.**



---

# 27. HTTP Resources

الـHTTPS resources لها caveat:

> المفروض يكون الـClient قادرًا على fetch الـresource بنفسه من الويب، بدل أن يكون MCP Server هو الذي يجلبه نيابة عنه.

وده يخفف بعض SSRF semantics، لكنه لا يلغي احتمالات:

```text
Client-side attacks
URL handling flaws
Scheme confusion
Rendering issues
```



---

# 28. High-Level Attack Surface Map

الفيديو كله ممكن تحوله للخريطة دي:

```text id="g4tdx4"
                    MCP
                     │
       ┌─────────────┴─────────────┐
       │                           │
     CLIENT                      SERVER
       │                           │
 ┌─────┼─────┐              ┌──────┴──────┐
 ▼     ▼     ▼              ▼             ▼
Roots Sampling            Tools         Resources
      Elicitation             │             │
                             │             │
                       Metadata         URI handling
                       Names            Templates
                       Titles           File access
                       Description      HTTP
                       Namespace        Git
                       Schema           Rendering
```

وفوق الجميع:

```text
Authentication
Initialization
Capabilities
Transport
JSON-RPC
Proxy layers
```

---

# 29. Security Testing Workflow المستخلص من الفيديو

دي أهم Note عملية:

```text id="q9r8pb"
1. Identify MCP implementation
        ↓
2. Determine Client / Server / Proxy
        ↓
3. Identify transport
        ↓
4. Capture initialization
        ↓
5. Compare capabilities
        ↓
6. Inspect instructions
        ↓
7. Enumerate tools
        ↓
8. Inspect tool metadata
        ↓
9. Inspect tool namespaces
        ↓
10. Test tool invocation boundaries
        ↓
11. Enumerate resources
        ↓
12. Read resources
        ↓
13. Inspect resource URIs
        ↓
14. Inspect resource templates
        ↓
15. Check client-side rendering
        ↓
16. Assess roots / sampling / elicitation
        ↓
17. Test proxy/schema boundaries
        ↓
18. Validate impact
```

ده **تنظيم لأفكار المتحدث**، مش checklist حرفية قالها بالترتيب.

---

# 30. أهم Attack Classes في الفيديو

### A. Initialization / Context Injection

```text
instructions
→ model context
→ tool hijacking
```

### B. Tool Poisoning

```text
tool description
→ system prompt
→ model influence
```

### C. Tool Name / Namespace Confusion

```text
name
+
title
+
annotation
+
namespace
→ ambiguity
```

### D. Resource Abuse

```text
resources/read
resources/templates
→ path / URI issues
```

### E. Client Data Disclosure

```text
roots/list
→ filesystem paths
```

### F. Sampling Abuse

```text
server
→ LLM sampling
→ downstream actions
```

### G. Elicitation Abuse

```text
server
→ request user data
→ auto acceptance
```

### H. Rendering / Content Injection

```text
tool response
→ client UI
→ XSS / injection / deceptive UI possibilities
```

### I. Protocol / Schema DoS

```text
Client
↔ Proxy
↔ Server
→ schema mismatch
→ parser issues
→ DoS
```



---

# 31. أهم Lessons من الفيديو

## Rule 1

**Read the protocol spec.**

MCP ليس black box.

## Rule 2

**Understand the message flow before testing bugs.**

Initialization مهم جدًا.

## Rule 3

**Capabilities define attack surface.**

## Rule 4

**Metadata is part of the security boundary.**

خصوصًا:

```text
name
title
description
annotation
```

## Rule 5

**Do not trust UI labels.**

ما يظهر للمستخدم ليس بالضرورة ما يتم استدعاؤه فعليًا.

## Rule 6

**Resources تستحق اهتمامًا كبيرًا.**

خصوصًا:

```text
resources/read
resources/templates
```

## Rule 7

**Client-side behavior matters.**

MCP security ليست server-only.

## Rule 8

**Human approval is not automatically a robust security boundary.**

## Rule 9

**Every abstraction layer adds attack surface.**

خصوصًا:

```text
MCP Proxy
Client
Server
Auth Server
```

## Rule 10

**Modern AI protocols تحتاج testing تقني تقليدي أيضًا.**

لا تنشغل فقط بـprompt injection وتنسى:

```text
Parser
Schema
URI
Auth
Path
Namespace
State
```

---

# 32. What the Speaker Explicitly Says Is Uncertain

وده مهم جدًا للنوتس.

المتحدث صراحة يقول إن:

* خبرته حوالي 16 ساعة بحث.
* لم يختبر كل transports بعمق.
* authentication تحديدًا ليست أقوى أجزاء خبرته.
* بعض الـattack ideas هي **hypotheses**.
* لا يعتبر الفيديو threat model شاملًا.
* ويتوقع corrections من أشخاص أكثر خبرة في MCP. 

إذن ما نكتبش في النوتس:

```text
"هذه vulnerabilities مؤكدة في MCP"
```

الأدق:

```text
"هذه attack surfaces / hypotheses اقترحها المتحدث بناءً على قراءة الـspec وتجارب محدودة."
```

---

# 33. Relation to Our AI-Hunting Project

دي بقى أهم نقطة **بالنسبة لنا إحنا**.

إحنا عندنا حاليًا:

```text
OpenCode
   ↓
MCP
   ↓
Burp
```

فالفيديو ده بيدينا طريقة تفكير ممتازة في اختبار **الـMCP layer نفسها**.

لكن الأهم أنه يعزز مبادئ كنا جمعناها من الفيديوهات السابقة:

```text
Protocol
 ↓
Understand State
 ↓
Map capabilities
 ↓
Map tools/resources
 ↓
Identify trust boundaries
 ↓
Form hypotheses
 ↓
Validate
```

وده شبيه جدًا بـ:

```text
Target
 ↓
Surface
 ↓
Function
 ↓
Trigger
 ↓
Skill
 ↓
Evidence
 ↓
Validation
```

يعني MCP نفسه ممكن يبقى **Target / Attack Surface جديد** داخل الـAI Security methodology، مش مجرد plumbing نسيبه في الخلفية.

---

# 34. Key Takeaways

لو عايز الجزء الذي يتحفظ في أول النوتس:

```text
MCP SECURITY — CORE NOTES

MCP = JSON-RPC based protocol between clients and servers.

Core layers:
- Transport
- Initialization
- Authentication
- Capabilities
- Tools
- Resources
- Client objects
- Proxy layers

Client objects:
- Roots
- Sampling
- Elicitation

Server objects:
- Tools
- Resources
- Prompts / Logging / Utilities

High-value security areas:
- Initialization instructions
- Tool metadata
- Tool name/title mismatch
- Namespace collisions
- Tool descriptions / prompt injection
- User confirmation boundaries
- Roots disclosure
- Sampling abuse
- Elicitation abuse
- Resource URI handling
- Resource templates
- Path traversal / file access
- Git resource handling
- Client-side rendering
- Proxy/schema mismatches
- Authentication / dynamic client registration

Core methodology:
Understand protocol
→ inspect initialization
→ enumerate capabilities
→ inspect tools/resources
→ inspect trust boundaries
→ test implementation
→ validate actual impact

Important:
The podcast is not a complete MCP threat model.
Several attack ideas are hypotheses from the speaker's research.
```

ده أنسب شكل تحفظه في الـNotes لأنه **بيفصل الـMCP fundamentals عن الـsecurity hypotheses**، ودي نقطة مهمة جدًا عشان ما تتحولش كل ملاحظة من بودكاست إلى "ثغرة مؤكدة في الإنتاج". 😄 
