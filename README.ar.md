# حزمة Adaptive Multi-Agent Skills

مجموعة Skills لتنظيم العمل بين عدة وكلاء برمجيين مثل Claude وAGY وOpenCode، مع إمكانية تغيير الأدوار والموديلات، والتعامل مع نفاد الحصة (Quota)، وتوزيع المهام حسب موارد الجهاز.

**اسم الحزمة:** `adaptive-multi-agent-orchestrator`

**اللغات:** [English README](README.md) | العربية

## التثبيت باستخدام npx skills

تستخدم الحزمة أداة Skills CLI لتثبيت الـ Skills مباشرة من GitHub. يلزم Node.js بالإصدار `22.20.0` أو أحدث وفق متطلبات CLI الحالية.

### تثبيت كل Skills على الوكلاء المدعومين

نفّذ الأمر من مجلد المشروع الذي تريد تثبيت الـ Skills فيه:

```bash
npx skills add mahmoudredash/adaptive-agent-skills- --all
```

هذا يثبت جميع الـ Skills الموجودة في المستودع على الوكلاء الذين يدعمهم CLI ويكتشفهم. التثبيت الافتراضي يكون داخل المشروع الحالي.

### اختيار Skills أو Agents محددين

لعرض أسماء الـ Skills المتاحة أولاً:

```bash
npx skills add mahmoudredash/adaptive-agent-skills- --list
```

لتثبيت Skill محدد على Agent محدد، استخدم الاسم الموجود في قائمة النتائج:

```bash
npx skills add mahmoudredash/adaptive-agent-skills- --skill adaptive-multi-agent-orchestrator --agent claude-code
```

يمكن تحديد أكثر من Skill أو Agent بتكرار الخيارات:

```bash
npx skills add mahmoudredash/adaptive-agent-skills- \
  --skill adaptive-multi-agent-orchestrator \
  --skill agent-role-model-routing \
  --agent claude-code \
  --agent opencode
```

### تثبيت عام لجميع المشاريع

إذا أردت إتاحة الـ Skills على مستوى المستخدم بدلاً من المشروع الحالي:

```bash
npx skills add mahmoudredash/adaptive-agent-skills- --all --global
```

> راجع قائمة الوكلاء المدعومين في Skills CLI؛ استخدم أسماء الـ Agents التي يتعرف عليها CLI كما هي. لا تفترض أن اسم AGY يساوي اسم Agent آخر.

### تحديث Skills المثبتة

```bash
npx skills check
npx skills update
```

المراجع الرسمية للأداة: [Skills CLI على npm](https://www.npmjs.com/package/skills) و[دليل الأوامر](https://github.com/vercel-labs/skills/blob/main/docs/commands.md).

## إعداد ملف سياسة المشروع

تثبيت الـ Skills لا ينسخ ملف إعدادات سياسة الموديلات تلقائياً؛ هذا الملف إعداد مستقل خاص بكل مشروع.

المسار المقترح داخل مشروعك:

`<project-root>/.config/agent-model-policy.yaml`

هذا المسار مخفي ومحلي للمشروع، لتجنب التعارض مع مجلد `config/` الذي قد يكون مستخدماً بالفعل من التطبيق أو إطار العمل.

انسخ ملف الإعداد من هذا المستودع إلى المشروع:

```bash
mkdir -p .config
curl -fsSL https://raw.githubusercontent.com/mahmoudredash/adaptive-agent-skills-/main/.config/agent-model-policy.yaml \
  -o .config/agent-model-policy.yaml
```

بعد النسخ، افتح الملف وعدّل القيم التجريبية لتطابق الموديلات المتاحة فعلياً في بيئتك. لا تترك القيم النصية مثل `ENTER_MODEL_ID` كما هي.

## تخصيص الموديلات والأدوار

أنت المتحكم في قائمة الموديلات وفي طريقة استخدامها. أضف لكل موديل:

- **المعرّف الفعلي (Model ID):** كما يظهر في الـ runtime الذي تستخدمه.
- **Runtime / Provider:** البيئة ومزود الخدمة.
- **متى يُستخدم (use_when):** المهام التي تريد اختيار هذا الموديل لها.
- نقاط القوة والقيود والحالات التي لا تفضّل استخدامه فيها.

مثال توضيحي؛ استبدل القيم بأسماء ومعرّفات حقيقية من بيئتك:

```yaml
model_catalog:
  - id: "YOUR_CLAUDE_MODEL_ID"
    runtime: "Claude Code"
    provider: "Anthropic"
    use_when: |
      استخدمه للتخطيط المعماري، والتحليل المعقد،
      وقرارات الدمج والتغييرات متعددة الصفحات.
    strengths:
      - "التحليل والتخطيط"
    limitations:
      - "قد تكون الحصة محدودة"
    avoid_when:
      - "التعديلات الروتينية الصغيرة"

  - id: "YOUR_AGY_MODEL_ID"
    runtime: "AGY"
    provider: "YOUR_PROVIDER"
    use_when: |
      استخدمه لتنفيذ المهام المعتمدة وتعديلات الكود المحددة.
    strengths:
      - "تنفيذ المهام البرمجية"
    limitations:
      - "يرفع القرارات المعمارية المعقدة للمسؤول التقني"
    avoid_when:
      - "مراجعة مستقلة لتغييرات نفذها بنفسه"
```

### تحديد الموديل الأساسي والبدائل

داخل قسم `agents`، حدد لكل Agent الموديل الأساسي وترتيب موديلات الـ Fallback:

```yaml
agents:
  claude:
    enabled: true
    default_role: technical_lead
    primary_model: "YOUR_CLAUDE_MODEL_ID"
    fallback_models:
      - "YOUR_CLAUDE_FALLBACK_MODEL_ID"

  agy:
    enabled: true
    default_role: implementer
    primary_model: "YOUR_AGY_MODEL_ID"
    fallback_models:
      - "YOUR_AGY_FALLBACK_MODEL_ID"

  opencode:
    enabled: true
    default_role: independent_qa
    primary_model: "YOUR_OPENCODE_MODEL_ID"
    fallback_models:
      - "YOUR_OPENCODE_FALLBACK_MODEL_ID"
```

يتم تجربة البدائل بالترتيب المكتوب. لا تضف موديلًا إلا إذا كان متاحاً فعلياً للـ Agent المعني.

### تغيير الأدوار عند نفاد Quota

مثال: إذا انتهت حصة AGY، اسمح لـ OpenCode بتولي التنفيذ بعد الموافقة المطلوبة:

```yaml
quota_fallback:
  agy:
    on_quota_exhausted:
      eligible_agents:
        - opencode
        - claude
      require_user_approval_for_role_change: true

manual_overrides:
  allow_opencode_only_mode: true
  opencode_only_mode_roles:
    - implementer
    - investigator
    - tester
  allow_single_agent_to_self_review_as_independent: false
```

يمكنك تغيير ترتيب الوكلاء البدلاء والأدوار المسموح بها حسب احتياجك.

**تنبيه مهم:** إذا نفذ OpenCode التعديلات، فلا يجوز اعتباره مراجعاً مستقلاً لنفس التعديلات. يجب إسناد المراجعة إلى Agent آخر أو توضيح أن المراجعة المستقلة غير متاحة والتوقف عند بوابة القبول إذا كانت مطلوبة.

## الأدوار الافتراضية

هذه أدوار مقترحة وليست ثابتة؛ يمكنك تعديلها في ملف الإعداد:

| Agent | الدور الافتراضي |
|---|---|
| Claude | القيادة التقنية والتخطيط والتنسيق |
| AGY | التنفيذ الأساسي |
| OpenCode | الاختبار والمراجعة المستقلة |

## آلية التعامل مع المشكلات

### نفاد حصة الموديل أو مزود الخدمة

1. حفظ نقطة استكمال تتضمن حالة المهمة والقرارات والملفات التي تغيرت والاختبارات ونتائجها.
2. تجربة الموديل البديل التالي المحدد لنفس Agent.
3. إذا لم يتوفر بديل، الانتقال إلى Agent بديل مسموح به في الإعدادات.
4. نقل ملكية الملفات بوضوح، ومنع أكثر من Agent من الكتابة في الملف نفسه في الوقت ذاته.
5. توضيح أي تغيير في Agent أو Model.
6. التوقف بأمان إذا لم يتوفر بديل صالح.

### ضغط موارد الجهاز

ضغط الذاكرة أو المعالج أو مساحة القرص ليس هو نفسه نفاد الحصة. عند ضغط موارد الجهاز، قلل التوازي أو ضع المهام في قائمة انتظار؛ لا تفترض أن تغيير الموديل سيحل المشكلة.

## الـ Skills الموجودة في المستودع

- `adaptive-multi-agent-orchestrator`: تنسيق المهام والتوازي وتوزيع العمل.
- `agent-role-model-routing`: اختيار الموديلات وتوجيه الأدوار والـ Fallback.
- `agy-implementation-workflow`: سير عمل التنفيذ.
- `opencode-independent-qa`: المراجعة والاختبار المستقلان.
- `project-safety-acceptance`: حماية المشروع وضوابط السلامة والقبول.

## قواعد السلامة

- الحفاظ على تعديلات المستخدم الحالية وعدم إتلافها.
- عدم تنفيذ أوامر Git التدميرية.
- عدم عمل Stage أو Commit أو بناء APK إلا بطلب صريح.
- عدم حذف الاختبارات أو إضعافها لتجاوز الفشل.
- عدم تعديل Backend أو APIs أو نطاقات غير معتمدة.
- استخدام مساحة العمل المشتركة افتراضياً؛ لا تستخدم Worktrees أو Clones إلا بطلب صريح.
- Agent واحد فقط يملك صلاحية الكتابة في الملف في الوقت نفسه.
- عدم بدء المهمة التالية قبل قبول المهمة الحالية عندما يتطلب سير العمل ذلك.
