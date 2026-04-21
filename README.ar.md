<p align="center">
  <a href="https://hitmman55.github.io/claude-code-chat-viewer/">
    <img src="https://img.shields.io/badge/▶-جرّبه%20أونلاين-2ea043?style=for-the-badge" alt="جرّبه أونلاين" />
  </a>
</p>
<p align="center"><i>فقط اضغط — بدون تثبيت أو بناء أو تسجيل.</i></p>

<p align="center">
  <a href="README.md">English</a> ·
  <a href="README.ru.md">Русский</a> ·
  <a href="README.es.md">Español</a> ·
  <a href="README.fr.md">Français</a> ·
  <a href="README.zh-CN.md">中文</a> ·
  <b>العربية</b>
</p>

<div dir="rtl" lang="ar">

# Claude Code Chat Viewer

<p align="center">
  <img src="https://img.shields.io/badge/license-Unlicense-blue" alt="الرخصة: Unlicense" />
  <img src="https://img.shields.io/badge/runtime%20deps-0-brightgreen" alt="صفر اعتماديات وقت التشغيل" />
  <img src="https://img.shields.io/badge/offline-first-success" alt="يعمل دون اتصال" />
  <img src="https://img.shields.io/badge/i18n-6%20languages-informational" alt="6 لغات للواجهة" />
</p>

<p align="center">
  <img src="screenshot.png" alt="لقطة شاشة" width="900" />
</p>

عارض HTML لسجلات جلسات [Claude Code](https://claude.com/claude-code) بصيغة JSONL. يعمل مباشرة في المتصفح — بدون خادم، بدون خطوة بناء، اعتمادية واحدة مُحزَّمة محليًا. يعمل دون اتصال فور الاستخدام.

## لماذا

يكتب Claude Code كل جلسة في `~/.claude/projects/<project>/<session-uuid>.jsonl` — سطر واحد لكل سجل (رسالة المستخدم، رد النموذج، thinking، tool_use، tool_result، attachment، وما شابه). الملف الخام غير قابل للقراءة؛ الأوامر المدمجة مثل `/resume` تعرض المحادثة لكنها لا تسمح بتصديرها أو فحصها لاحقًا.

يحوّل هذا العارض ملف `.jsonl` إلى تدفق قابل للقراءة مع ألوان حسب الدور، وكتل خدمية قابلة للطي (thinking / tools / results)، ومرشّحات.

## ماذا يعرض

- **user** (أزرق) — رسائل المستخدم الحقيقية
- **assistant** (أخضر) — ردود Claude النصية
- **thinking** (بنفسجي) — التفكير الموسّع، مطويّ افتراضيًا
- **tool_use** (كهرماني) — استدعاءات الأدوات مع معاينة للمعاملات
- **tool_result** (سماوي / أحمر للأخطاء) — ردود الأدوات
- **meta / task-note** (أصفر) — حقن النظام و `<task-notification>` من الوكلاء الفرعيين
- **system / attachment / ui-state** — سجلات خدمية (مخفية افتراضيًا)

كل كتلة تظهر كصف منفصل مع شريط ملون على اليسار. لا توجد فقاعات محادثة: هذا سجلّ لا دردشة.

## كيفية الفتح

1. انسخ المستودع أو حمّل ZIP. تحتاج إلى `index.html` ومجلد `lib/`.
2. انقر نقرًا مزدوجًا على `index.html` — يُفتح في أي متصفح حديث.
3. استخدم منتقي الملفات واختر سجلّ `.jsonl`.

تُحفظ سجلات Claude Code في:

```
~/.claude/projects/<project-slug>/<session-uuid>.jsonl
```

حيث `<project-slug>` هو مجلد العمل لديك مع استبدال `/` بـ `-`. مثال: `/home/user/myproj` ← `-home-user-myproj`.

## الميزات

- **سمة فاتحة / داكنة** — زرّ في الرأس، يُحفظ التفضيل في `localStorage`.
- **ست لغات للواجهة** — English، Русский، Español، Français، 中文، العربية. تنتقل العربية تلقائيًا إلى RTL. يوجد منتقي في الرأس، ويُحفظ التفضيل.
- **تحليل متدفّق** — يُقرأ `.jsonl` عبر `file.stream()` + `TextDecoderStream`، ولا يُحمَّل الملف كسلسلة واحدة.
- **افتراضية أصلية** — `content-visibility: auto` على كل سجل: يتخطى المتصفح تخطيط ورسم السجلات خارج الشاشة. يتحمّل آلاف السجلات.
- **عرض بأجزاء** — 500 سجل في كل جزء، وزر «عرض المزيد» لبقية السجلات.
- **مرشّحات** — خمسة مربعات اختيار (thinking / tools / results / system / ui-state)، تُبدّل الفئات عبر صنف CSS واحد على الحاوية (بدون إعادة تخطيط DOM).
- **عرض آمن من XSS** — تُهرَّب كل كتلة نصية _قبل_ تمريرها إلى محلّل markdown. لا يصل HTML خام من السجل إلى DOM، لذا لا حاجة إلى مُنقٍّ وقت التشغيل.
- **حدود للحجم** — تُقصَّ كتل النص إلى 20 كيلوبايت، والكتل الخدمية إلى 5 كيلوبايت (عادة تحمل 10-50 كيلوبايت من محتوى لا يقرأه أحد). التسلسل محدود أيضًا — لا تُجسَّد مُدخلات الأدوات الضخمة بالكامل قبل القصّ.
- **احتياطي لـ `.json`** — إذا لم يكن الملف JSONL بل مصفوفة/كائن JSON عادية، يُحلَّل كقائمة سجلات.

## متطلبات المتصفح

- Chrome / Edge 85+
- Safari 18+
- Firefox 125+

كل هذا لازم لدعم `content-visibility: auto`. على المتصفحات الأقدم يعمل العارض، لكن التمرير على الملفات الكبيرة سيصبح أبطأ بشكل ملحوظ.

## الاعتماديات

واحدة فقط، مُحزَّمة محليًا في `lib/`:

- [marked](https://github.com/markedjs/marked) — markdown → HTML (~35 كيلوبايت)

بدون CDN، بدون شبكة، بدون Subresource Integrity. انسخ وشغِّل.

## الخصوصية

كل شيء يعمل محليًا في متصفحك. العارض نفسه **لا يُرسل أي طلبات شبكية تلقائية** — لا CDN، ولا تحليلات، ولا خطوط بعيدة. يتم تحييد صور Markdown المضمّنة في السجلات: تُعرض كنص خامل مع ظهور الرابط، لكن لا يتم جلبها. الروابط الخارجية (فقط `http(s)`) تُفتح في علامة تبويب جديدة فقط عند النقر عليها، مع `rel="noopener noreferrer nofollow"`. تبقى سجلاتك على جهازك.

## قيود معروفة

- الملفات التي تتجاوز حوالي 100 ميجابايت تحتاج إلى تحميل مفهرس حسب إزاحات الأسطر مع عرض نوافذ (غير مُنفَّذ).
- لا يوجد تصدير إلى Markdown/HTML (الهدف هو العرض لا التحويل).
- لا يوجد تلوين للصياغة في كتل الشيفرة (قرار مقصود للحفاظ على الحد الأدنى من الاعتماديات).

## التطوير

كل الشيفرة في ملف HTML واحد. حرِّره مباشرة — الأنماط في `<style>`، والمنطق في `<script>`، والترجمات في كائن `I18N` في بداية السكربت.

فحص صياغة JS بدون متصفح:

```bash
sed -n '/^<script>$/,/^<\/script>$/p' index.html | sed '1d;$d' | node --check /dev/stdin
```

## الرخصة

[Unlicense](LICENSE) — ملكية عامة. استخدمه كما تشاء دون الحاجة إلى إسناد.

</div>
