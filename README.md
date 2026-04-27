# أ. أمل المطيري — Portfolio

موقع شخصي بتصميم عربي RTL لمعلمة علوم الحاسب الآلي وتعليم الموهوبات، يتضمن قسم تعريفي، إحصاءات تعليمية، ومركز ملفات متكامل مرتبط بـ Google Drive.

---

## المميزات

- واجهة عربية RTL بالكامل
- **Hero section** — تأثير الكتابة التدريجي (typewriter) على الاسم، حلقات دوارة، وروابط التواصل الاجتماعي
- **Statistics** — أرقام متحركة (count-up) تُفعَّل عند الظهور في نطاق العرض
- **مركز الملفات التعليمية** — استعراض هيكل مجلدات جوجل درايف
- بحث وفلترة للمجلدات (الكل / مرتبط / غير مرتبط)
- عرض شبكي وعرض شجري للمجلدات
- **Google Drive API v3** — مزامنة تلقائية للمجلدات الفرعية دون الحاجة لإدخالها يدويًا
- **DriveBrowserModal** — تصفح محتويات المجلدات داخل الصفحة مباشرةً
- رابط "فتح في درايف" كخيار احتياطي في نافذة جديدة
- **MobileFolderPreviewModal** — نافذة معاينة ملء الشاشة على الجوال عند اختيار مجلد
- **لوحة معاينة جانبية** على سطح المكتب (sticky side panel)
- الوضع الداكن والفاتح مع حفظ التفضيل في `localStorage` (المفتاح: `theme`)
- خلفية جسيمات متحركة عبر particles.js
- تصميم متجاوب للجوال والتابلت وسطح المكتب
- منظومة أيقونات SVG مدمجة بالكامل دون أي مكتبة أيقونات خارجية

---

## طريقة التشغيل

لا يتطلب المشروع أي تثبيت أو بناء.

**للعرض المباشر:** افتح `index.html` في أي متصفح.

**للتطوير والاختبار (موصى به):** شغّل خادم محلي لتفادي قيود CORS عند تحميل الخطوط وطلبات Drive API:

```bash
# macOS / Linux
python3 -m http.server 8000

# Windows
py -m http.server 8000
```

ثم افتح المتصفح على: `http://localhost:8000`

---

## تخصيص المحتوى

جميع البيانات القابلة للتعديل موجودة في أعلى كتلة `<script>` داخل `index.html`:

| الثابت | الغرض |
|---|---|
| `teacherInfo` | الاسم، التخصص، الدرجة العلمية، سنوات الخبرة، رقم الهاتف، البريد الإلكتروني، وروابط التواصل |
| `statsData` | بطاقات الإحصاء الأربع — النص، الرقم، والأيقونة |
| `foldersData` | المجلدات الجذرية — أضف `driveId` لربط المجلد بـ Google Drive |

### ربط مجلد Google Drive

1. افتح المجلد في Google Drive
2. انسخ المعرّف من الرابط: `drive.google.com/drive/folders/`**`FOLDER_ID`**
3. ضعه في المدخل المناسب داخل `foldersData`:

```js
{ id: 1, name: "ابداع", driveId: "FOLDER_ID" }
```

اتركه `driveId: null` للمجلدات غير المرتبطة بعد.

> **ملاحظة:** المجلدات المرتبطة بـ Drive تُجلَب مجلداتها الفرعية تلقائيًا من Google Drive API — لا حاجة لكتابة `children` يدويًا. المجلدات غير المرتبطة يمكنها استخدام `children` يدويًا كبديل.

---

## Google Drive API — الإعداد

### 1. تفعيل Google Drive API

1. افتح [Google Cloud Console](https://console.cloud.google.com/)
2. أنشئ مشروعًا جديدًا أو اختر مشروعًا موجودًا
3. من القائمة الجانبية: **APIs & Services → Library**
4. ابحث عن **Google Drive API** وفعّله

### 2. إنشاء API Key

1. من القائمة: **APIs & Services → Credentials**
2. اضغط **Create Credentials → API key**
3. انسخ المفتاح وضعه في الثابت `GOOGLE_DRIVE_API_KEY` داخل `index.html`

### 3. تقييد المفتاح (ضروري)

في إعدادات المفتاح:

- **Application restrictions:** HTTP referrers (Websites)
- **Website restrictions:** أضف المراجع المسموح بها، مثل:
  ```
  http://localhost:8000/*
  https://your-domain.com/*
  https://username.github.io/*
  ```
- **API restrictions:** اختر **Restrict key** ثم حدد **Google Drive API** فقط

### 4. متطلبات الوصول

| العنصر | التفاصيل |
|---|---|
| الثابت | `GOOGLE_DRIVE_API_KEY` في أعلى الـ script |
| النطاق | Drive API v3 فقط |
| التخزين المؤقت | نتائج المجلدات الفرعية تُخزَّن في الذاكرة 5 دقائق (`driveFolderCache`) |
| الدوال المستخدمة | `listDriveFolder()` · `listDriveSubfolders()` · `getDisplayFolder()` وغيرها |

يجب أن يكون كل مجلد مشاركًا بصلاحية **"Anyone with the link"** أو عامًا حتى يتمكن API Key غير المصادق (unauthenticated) من قراءته.

---

## ملاحظات أمنية

- **مفاتيح API في الواجهة الأمامية مرئية** في كود المتصفح — هذا متوقع وطبيعي للمشاريع العامة.
- الحماية الفعلية تعتمد بالكامل على **تقييد المفتاح** بالمراجع والـ API المسموح بها (راجع قسم الإعداد أعلاه).
- لا تنشر مجلدات Drive خاصة أو حساسة؛ إذ يتمكن أي شخص لديه رابط المجلد من الوصول إليه.
- إذا سرب المفتاح علنًا (مثلًا في commit عام)، **أوقفه فورًا** من Google Cloud Console وأنشئ مفتاحًا جديدًا.
- زر "فتح في درايف" يفتح المجلد في نافذة جديدة ويخضع لصلاحيات Google العادية — لا يتجاوز أي قيود.

---

## استكشاف الأخطاء

| المشكلة | السبب المحتمل | الحل |
|---|---|---|
| **Drive API 403** | المفتاح غير مقيّد بالمرجع الصحيح، أو Drive API غير مفعّل | تحقق من قيود المفتاح في Google Cloud Console، وتأكد أن الطلب يأتي من مرجع مضاف في القائمة |
| **المجلد لا يظهر أو يبقى "جاري المزامنة"** | المجلد غير مشارك علنًا | تأكد أن المجلد مشارك بصلاحية "Anyone with the link" |
| **الخطوط لا تظهر** | مجلد `thmanyah typeface/` مفقود أو في مكان خاطئ | تأكد أن المجلد موجود بجانب `index.html` بنفس المسار |
| **مكتبات CDN لا تُحمَّل** | انقطاع في الإنترنت أو حجب CDN | تحقق من الاتصال، أو استضف المكتبات محليًا مؤقتًا للتطوير |
| **الصفحة لا تعمل من `file://`** | قيود CORS تمنع تحميل الخطوط وطلبات API | شغّل خادمًا محليًا (راجع قسم طريقة التشغيل) |
| **مشاكل التخطيط على الجوال أو Safari** | سلوك `100dvh` أو safe-area قد يختلف | اختبر عبر DevTools device emulation أو جهاز حقيقي، وتأكد من وجود `<meta name="viewport">` |

---

## هيكل الكود (component tree)

```
App
├── ParticlesBackground
├── Nav
└── main
    ├── Hero          — (Typewriter · orbit rings · socials)
    ├── Stats         — (CountUp · IntersectionObserver)
    ├── Portfolio     — (Drive Command Center)
    │   ├── Toolbar   — (search · filters · view toggle)
    │   ├── FolderCard[]          — grid mode
    │   ├── TreeRow[]             — tree mode (lazy Drive loading)
    │   ├── FolderPreviewPanel    — desktop sticky side panel
    │   ├── MobileFolderPreviewModal  — fullscreen overlay on mobile
    │   └── DriveBrowserModal     — in-page Drive API browser
    ├── Contact
    └── Footer
```

---

## ملاحظات للمطور

- **حافظ على الملف الواحد** — المشروع مصمم كـ single-file app. لا تقسّمه إلى ملفات متعددة أو تحوّله إلى Vite/Next.js ما لم يكن ذلك قرارًا مقصودًا.
- **لا تُعيد إدخال Google Drive iframe** — الـ iframe أُزيل لأن Google تمنع التنقل الداخلي داخل الإطار المضمّن. `DriveBrowserModal` هو المتصفح الحالي داخل الصفحة عبر Drive API.
- **`DriveBrowserModal`** — يُفتح عند الضغط على "معاينة داخل الصفحة"، ويتصفح محتويات المجلد عبر Drive API v3.
- **`MobileFolderPreviewModal`** — يُفتح تلقائيًا على شاشات الجوال (أقل من 768px) عند اختيار مجلد، بدلًا من عرض لوحة المعاينة أسفل البطاقات.
- **بعد أي تعديل، تحقق من:** العرض الشبكي والشجري، النموذجين (DriveBrowserModal وMobileFolderPreviewModal)، البحث والفلاتر، وزر تبديل الوضع الداكن/الفاتح.

---

## الخطوط

يستخدم الموقع ثلاثة خطوط Thmanyah محلية محملة عبر `@font-face`:

| الخط | الاستخدام |
|---|---|
| `ThmanyahSans` | النصوص العامة |
| `ThmanyahSerifDisplay` | العناوين الكبيرة والـ Hero |
| `ThmanyahSerifText` | فقرات النص الطويل |

احتفظ بمجلد `thmanyah typeface/` بجانب `index.html`، وإلا سيتراجع الموقع لخطوط النظام.

يُحمَّل خط **JetBrains Mono** من Google Fonts لعرض النصوص اللاتينية والأكواد.

---

## الملفات

| الملف / المجلد | الغرض |
|---|---|
| `index.html` | التطبيق الكامل — ملف واحد يحتوي على HTML وCSS وJSX |
| `thmanyah typeface/` | ملفات الخطوط العربية المحلية (woff2) |
| `CLAUDE.md` | تعليمات المشروع لـ Claude Code |
| `digram1.md` | مرجع مكوّن React لواجهة شجرة المجلدات |
| `portfolio-code.md` | متطلبات التصميم الأصلية |
