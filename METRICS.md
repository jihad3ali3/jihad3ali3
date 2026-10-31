# 🛠️ دليل داشبورد الـ Metrics

البروفايل بيستعمل [lowlighter/metrics](https://github.com/lowlighter/metrics) عشان يولّد صورة SVG فيها إحصائيات GitHub،
وبعدها `README.md` بيعرضها. الصورة **مش مرفوعة يدوي** — الـ workflow بيبنيها ويحدّثها لوحده.

## إيه اللي كان مكسور قبل كذا؟

- `README.md` كان بيشير إلى `metrics.svg` لكن الملف **مش موجود** أصلًا في الريبو.
- مفيش أي workflow في `.github/workflows/` بيشغّل metrics، فالصورة كانت بتظهر مكسورة (broken image).

## إيه اللي اتعمل؟

| الملف | الوظيفة |
| :--- | :--- |
| `.github/workflows/metrics.yml` | يبني الداشبورد ويحوّله للريبو تلقائيًا |
| `metrics.svg` | داشبورد واحد عريض (`config_display: large`) |
| `README.md` | بيعرض الداشبورد بمسار نسبي |

الداشبورد فيه:

| القسم | البلوجن / الإعداد | محتاج إيه |
| :--- | :--- | :--- |
| الهيدر (الاسم، تاريخ الانضمام، الموقع، المتابعين) | `base: header` | — |
| عدّادات النشاط (commits / PRs / comments) | `base: activity` | — |
| الريبوهات (رخص، releases، packages، disk، stars/forks/watchers) | `base: repositories` | `METRICS_TOKEN` |
| تقويم المساهمات ثلاثي الأبعاد + الـ streaks | `plugin_isocalendar` (`full-year`) | — |
| توزيع اللغات + الـ legend (نسب/أسطر/حجم) | `plugin_languages` + `plugin_languages_indepth` | `METRICS_TOKEN` |
| **Mastered technologies** (شبكة أيقونات التكنولوجيات) | `plugin_topics` (`mode: icons`) | **topics على الريبوهات** |
| عادات الكود + رسوم (time of day / day / language activity) | `plugin_habits` (facts + charts) | `METRICS_TOKEN` |
| أسطر الكود المضافة/المحذوفة | `plugin_lines` | — |
| شارات الإنجازات | `plugin_achievements` (compact) | — |
| آخر نشاط على الـ issues / PRs | `plugin_followup` (indepth) | — |
| الـ Reactions اللي استلمتها | `plugin_reactions` | — |

الألوان متظبطة على ثيم البروفايل (أزرق `#3B82F6` + إنديغو `#6366F1` + سماوي `#22D3EE`) عن طريق `THEME_CSS` و `THEME_JS`،
والتوقيت مضبوط على `Asia/Aden`.

## التشغيل

1. ادمج الـ Pull Request في `main` — أول تشغيل بيبدأ تلقائيًا لأن الـ workflow جديد.
2. أو من تبويب **Actions ← Profile metrics ← Run workflow**.
3. بعد ما يخلص، هتلاقي `metrics.svg` في جذر الريبو، وصورة الـ README هتشتغل.
4. التشغيل المجدول: كل يوم **02:17 UTC** (05:17 صباحًا بتوقيت صنعاء).

## التوكن (اختياري لكن مستحسن)

الـ workflow بيشتغل بـ `GITHUB_TOKEN` المدمج، وكفاية للريبوهات العامة فقط.
عشان المساهمات الخاصة (private) تظهر في التقويم والـ streaks:

1. اعمل Personal Access Token (classic) من Settings ← Developer settings، بصلاحيات:
   - `repo` (للمساهمات الخاصة)
   - `read:user`
   - `read:org` (لو عندك أورجانيزيشنز)
2. من الريبو: **Settings ← Secrets and variables ← Actions ← New repository secret** بالاسم `METRICS_TOKEN`.
3. الـ workflow بيستخدمه تلقائيًا: `${{ secrets.METRICS_TOKEN || github.token }}` — لو السر مش موجود بيرجع للتوكن الافتراضي بدون فشل.

> 💡 ملاحظة مهمة (1): أغلب نشاطك الحالي مساهمات في ريبوهات خاصة (حوالي 265 مساهمة مقيدة)، وبدون `METRICS_TOKEN` التقويم هيبان أفتح بكتير من الحقيقة.

> ⚠️ ملاحظة مهمة (2): `GITHUB_TOKEN` بيشوف **ريبو واحد بس** (ريبو البروفايل نفسه). عشان كده من غيره الكارت بيطلع ناقص:
> `1 Repository` و `0 Languages` و `Recent coding habits → Unexpected error`. أول ما تضيف `METRICS_TOKEN` الأقسام دي هتتعبّى ببياناتك الحقيقية (17 ريبو + اللغات + الرسوم).

## التخصيص

- **إضافة بلوجن** (مثال: نجوم الريبوهات):
  ```yaml
  plugin_stars: "yes"
  plugin_stars_limit: 4
  ```
- **ترتيب الأقسام**: `config_order`.
- **استبعاد ريبوهات من حساب اللغات**: `plugin_languages_skipped` (مستبعد حاليًا ريبوهات التجارب/الكاميرا
  زي `camera2` و`TESTC` و`ONE` و`replit` عشان الكارت يبان بلغاتك الحقيقية).
- **تغيير الألوان**: عدّل `THEME_CSS` و `THEME_JS` في أعلى الـ workflow (مش محتاج تلمس باقي الملف). العناوين = `#6366F1`، الأيقونات/اللينكات = `#22D3EE`، ومربعات التقويم/الرسوم = `#C7D2FE → #60A5FA → #3B82F6 → #6366F1`.
- **عرض عادي بدل العريض**: شيل `config_display: large` أو خليه `columns`.
- **وقت التشغيل**: عدّل الـ `cron` (بتوقيت UTC).

## ملاحظات

- تشغيل الـ workflow بياخد تقريبًا 2–5 دقايق.
- لو الداشبورد ما اشتغلش، بص على سجل الـ Actions — و`plugins_errors_fatal: "no"` بتخلي أي بلوجن يفشل من غير ما يوقف الصورة.
- ما تعدّلش `metrics.svg` يدوي — أول تشغيل جديد هيكتب فوقه.

## الأجزاء اللي في كارت lowlighter ومش بتتكرر عندنا

| البلوك في الصورة | السبب |
| :--- | :--- |
| **PageSpeed Insights** (4 دوائر) | محتاج مفتاح Google PageSpeed API (مجاني) + لينك موقع شغّال. لو عندك موقع: ضيف سر `PAGESPEED_TOKEN` وشيل التعليق عن البلوك في الـ workflow. |
| **Suggested tracks** (Apple Music / Spotify) | محتاج ربط حساب Apple Music / Spotify بـ token خاص بيك — مش بيانات GitHub. |
| **1 Project / Roadmap and todos** | بلوجن `plugin_projects` بيعتمد على **Projects (classic)** اللي GitHub ألغاه خلاص (API اتمسح) — مش متاح لحد. |
| **اللوجو الكبير + Developer Program Member** | دي مش بلوجن أصلًا؛ دي صورة تجميعية (composite) من كذا كارت من موقع metrics نفسه، مش صورة واحدة. |

> ملاحظة: صورة lowlighter اللي بعتها هي **6 كروت منفصلة** جنب بعض (header + habits + languages + topics + pagespeed + projects + music)،
> إحنا بنولّد **كارت واحد** فيه أكبر قدر منها. عشان نحصل على نفس الكثافة تقريبًا، الأقسام اللي ممكن تتضاف لازم تتحقق فيها الشروط اللي في الجدول فوق.
