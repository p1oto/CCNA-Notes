# 09 - Network Devices & Headers Deep Dive

### 1. نظرة أعمق على الهيدرز (Headers Deep Dive)
تحدثنا سابقاً عن التغليف، وهنا تفصيل لأهم الخانات داخل كل هيدر:

<div align="right"><strong>أ. الهيدر الأزرق (Transport Layer / TCP & UDP)</strong></div>

- <span dir="ltr"><code>Source Port</code></span>: رقم المنفذ العشوائي لجهازك عند فتح جلسة جديدة (مثال: الجلسة الأولى تأخذ رقم <code>1024</code>).
- <span dir="ltr"><code>Destination Port</code></span>: رقم الخدمة التي تقصدها (مثال: <code>80</code> للتصفح، <code>20/21</code> للتحميل، <code>25</code> للإيميل، <code>23</code> للـ Telnet).
- <span dir="ltr"><code>Length</code></span>: خانة تحدد حجم البيانات (Data part) والذي يكون بحد أقصى <code>1460 Bytes</code>.
- <span dir="ltr"><code>CRC</code></span>: معادلة لاكتشاف الأخطاء تُحسب بناءً على البيانات والبورتات وحجم الـ Length. 

<br>

<div align="right"><strong>ب. الهيدر الأحمر (Network Layer / IP)</strong></div>

- <span dir="ltr"><code>Source IP</code></span>: عنوان الـ IP الخاص بالجهاز المُرسل (نقطة البداية).
- <span dir="ltr"><code>Destination IP</code></span>: عنوان الـ IP الخاص بالجهاز المُستقبل (الوجهة النهائية).
- <span dir="ltr"><code>ToS (Type of Service)</code></span>: خانة تُستخدم لتحديد أولوية وأهمية البيانات (QoS). الرقم الأعلى يعني أهمية قصوى (مثل تمرير الصوت أو الفيديو قبل تحميل الملفات).
- <span dir="ltr"><code>TTL (Time To Live)</code></span>: عداد عُمره الافتراضي 255. كلما مرت البيانات على راوتر، ينقص العداد بمقدار <code>1</code>. إذا وصل إلى صفر، يرمي الراوتر البيانات لمنعها من الدوران في الشبكة للأبد (Loop prevention).

<br>

<div align="right"><strong>ج. الهيدر الأخضر (Data Link Layer / MAC)</strong></div>

- <span dir="ltr"><code>Destination MAC</code></span>: الماك أدرس الخاص بالمحطة/القفزة التالية (يُكتب أولاً في الفريم).
- <span dir="ltr"><code>Source MAC</code></span>: الماك أدرس الخاص بالجهاز المُرسل للقفزة الحالية.
- <span dir="ltr"><code>Type</code></span>: خانة تحدد نوع إصدار الـ IP المُستخدم (IPv4 أو IPv6).
- **التسخين (Preamble):** <code>8 Bytes</code> غير محسوبة من حجم الـ Frame، عبارة عن نبضات <span dir="ltr"><code>10101010</code></span> لتنبيه الكارت المستقبل للاستعداد ومزامنة السرعة.
- **الذيل (FCS / Trailer):** حجمه <code>4 Bytes</code> ويُوضع في نهاية الفريم، ويحتوي على معادلة الـ <code>CRC</code> النهائية للتأكد من سلامة الفريم بالكامل من الأخطاء.
- **وقت الراحة (Idle Time / Inter-frame Gap):** بعد الانتهاء من إرسال الفريم كاملاً بالـ Trailer الخاص به، يأخذ كارت الشبكة "نَفَس" يعادل وقت إرسال <code>8 Bytes</code> كفاصل زمني قبل إرسال الفريم الذي يليه لتفريغ الكابل من الشحنات.
- **الحد الأدنى للـ Frame:** هو <code>64 Bytes</code>. الإرسال بأقل من ذلك يضر بكفاءة الشبكة (لذلك تجنب إرسال رسائل متناهية الصغر كحرف واحد).

---

### 2. مقارنة بين أجهزة الشبكة (Network Devices Comparison)
كل جهاز في الشبكة يمتلك "مستوى ذكاء" محدد بناءً على الهيدرز التي يستطيع قراءتها ومعالجتها.

<div align="right"><strong>أ. الـ Hub (مشترك غشيم - Layer 1)</strong></div>

- لا يفهم أرقاماً (IPs) ولا حروفاً (MACs).
- وظيفته الوحيدة تقوية الإشارة الكهربائية (Regenerate) ثم عمل <span dir="ltr"><code>Flood</code></span> (فيضان)؛ أي يرسل البيانات الخارجة من جهاز إلى جميع الأجهزة الأخرى المتصلة به دون تمييز. (انقرض استخدامه عملياً).

<br>

<div align="right"><strong>ب. الـ Switch العادي (جهاز التوجيه المحلي - Layer 2)</strong></div>

- يفهم الماكات فقط. يبني جدولاً في الرامات يُسمى <span dir="ltr"><code>MAC Table</code></span>.
- **كيف يتعلم؟** عندما يتحدث جهاز، يسجل السويتش الـ Source MAC الخاص به.
- **كيف يوجه؟** ينظر للـ Destination MAC ليوصل البيانات لصاحبها.
- **متى يعمل Flood؟** إذا كان لا يعرف مكان المُستلم، أو إذا كانت الرسالة موجهة للجميع (Broadcast) أو لمجموعة (Multicast).
- **التنظيف الذاتي:** إذا صمت جهاز ولم يرسل بيانات لمدة 5 دقائق، يمسح السويتش الماك الخاص به من الجدول لتوفير المساحة.

<br>

<div align="right"><strong>ج. الـ Router (بوابة الخروج - Layer 3)</strong></div>

- يفهم الـ MAC والـ IP. يبني جدولاً يُسمى <span dir="ltr"><code>Routing Table</code></span>.
- يُستخدم للربط بين الشبكات المختلفة (LAN to WAN).
- لا يقوم بعمل Flood للـ Broadcast إطلاقاً، بل يوقفه عنده كحاجز.
- **كيف يتعلم؟** لا يتعلم بمفرده كالسويتش، بل يجب تعليمه يدوياً (Static) أو بتشغيل بروتوكول توجيه (مثل OSPF).

<br>

<div align="right"><strong>د. الـ Firewall (الجدار الناري - Layer 4 to 7)</strong></div>

- يبني جدولاً يُسمى <span dir="ltr"><code>Inspection Table</code></span>، ويقرأ حتى أرقام البورتات (Layer 4).
- **قاعدته الافتراضية:** يسمح للبيانات بالخروج من الشبكة الداخلية للإنترنت، ويمنع دخول أي اتصال قادم من الخارج (إلا إذا كان رداً على طلب داخلي).
- **الجيل الحديث (Next-Gen Firewall):** يصل ذكاؤه إلى (Layer 7)، أي يفهم محتوى البيانات نفسه (Payload) ويمكنه منع كلمات معينة، أفعال معينة (مثل Like أو Comment)، أو تطبيقات محددة.