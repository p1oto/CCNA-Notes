# 04 - Application & Transport Layers Deep Dive

### 1. طبقة التطبيقات (Application Layer)
هذه الطبقة في نموذج الـ `TCP/IP` تدمج 3 طبقات من الـ `OSI` وهي (Application, Presentation, Session).
- **الوظيفة الأساسية:** تقوم بتوحيد المقامات؛ أي تحويل لغة البشر (نصوص، صور، صوت) إلى وحايد وأصفار `0s & 1s` ليفهمها الكمبيوتر.
- **مبدأ العميل والخادم (Client-Server):** تعتمد أغلب تطبيقات الشبكة على هذا المبدأ، حيث يكون الـ `Server` جهازاً ذو إمكانيات عالية ومفتوحاً طوال الوقت لخدمة الـ `Clients` (المستخدمين).
- **أبرز البروتوكولات المستخدمة:**
  * بروتوكول **`HTTP`**: مخصص للتصفح الآني (`Browsing`) وجلب صفحات الويب. بياناته غير مشفرة (`Clear-text`).
  * بروتوكول **`HTTPS`**: هو النسخة الآمنة والمشفرة من التصفح (`Secure Browsing`).
  * بروتوكول **`FTP`**: مخصص لنقل الملفات الضخمة (`Huge Files`) للرفع والتنزيل (`Upload & Download`).
  * بروتوكول **`SMTP`**: مخصص لإرسال الإيميلات (`Send/Push`) من جهاز المستخدم إلى سيرفر البريد.
  * بروتوكول **`POP3`**: مخصص لسحب واستخلاص الإيميلات (`Retrieve/Pull`) من السيرفر إلى جهاز المستخدم.
  * بروتوكول **`Telnet`**: مخصص للتحكم عن بُعد في إعدادات الرواتر والسويتشات (`Remote Configuration`). 

---

### 2. طبقة النقل (Transport Layer) - المهام المشتركة
تحتوي هذه الطبقة على أهم برنامجين في الشبكة: بروتوكول **`TCP`** (للدقة) وبروتوكول **`UDP`** (للإنجاز والسرعة).
هناك 3 مهام مشتركة يقوم بها كلا البروتوكولين:

<div align="right"><strong>أ. عملية التقطيع (Segmentation)</strong></div>

- يتم تقطيع الداتا الكبيرة إلى أجزاء لتسريع النقل وتسهيل معالجة الأخطاء.
- يبلغ حجم الجزء الصافي المسموح به من البيانات (`Data Part`) بحد أقصى **`1460 Bytes`**.

<br>

<div align="right"><strong>ب. عملية اكتشاف الأخطاء (Error Detection - CRC)</strong></div>

- يتم أخذ صورة مصغرة مضغوطة من البيانات لتكتشف الأجهزة أي خطأ أو تلاعب حدث أثناء النقل.
- يُخصص لهذه الصورة **`2 Bytes`** داخل الهيدر.

<br>

<div align="right"><strong>ج. عملية الترقيم والعنونة (Addressing - Port Numbers)</strong></div>

- يتم استخدام أرقام المنافذ (`Port Numbers`) لتمييز التطبيقات عن بعضها (مثلاً ليفصل الكمبيوتر بين متصفح جوجل وتطبيق الواتساب).
- يبلغ حجم منفذ البورت `16-Bit`، مما يعطي أرقاماً تبدأ من `0` إلى `65535`.
- **منفذ المستخدم (Source Port):** يفتح جهازك منفذاً عشوائياً يبدأ من رقم **`1024`** فأعلى لكل نافذة اتصال.
- **منفذ السيرفر (Destination Port):** تستخدم السيرفرات أرقاماً محجوزة وثابتة (`Well-known Ports`) من `0` إلى `1023`.
- **مفهوم السوكيت (Socket):** هو دمج عنوان الـ `IP` مع رقم الـ `Port Number` (مثال: `10.0.0.1:1024`)، ليفصل السيرفر بين جلسات المستخدمين المختلفين.

---

### 3. المهام الخاصة ببروتوكول الـ TCP (أسباب الدقة)
يتميز بروتوكول الـ `UDP` بأن الهيدر الخاص به خفيف جداً (**`8 Bytes`** فقط) لأنه يركز على السرعة. 
بينما يمتلك بروتوكول الـ `TCP` هيدر أكبر (**`20 Bytes`**) لأنه يقوم بـ 3 وظائف إضافية تضمن الدقة:

<div align="right"><strong>أ. خطوة إنشاء الجلسة (Session Establishment)</strong></div>

- لا يرسل الـ `TCP` الداتا فجأة، بل يتأكد أولاً من جاهزية الطرف الآخر عبر تبادل 3 رسائل تُعرف بـ **`3-Way Handshake`**:
  1. رسالة `SYN` (لطلب التزامن).
  2. رسالة `SYN-ACK` (للتأكيد من السيرفر).
  3. رسالة `ACK` (للتأكيد النهائي من المستخدم).
- يتم الاتفاق في هذه الخطوة على حجم النافذة **`Window Size`** (وهو عدد القطع التي يمكن إرسالها معاً قبل انتظار تأكيد الاستلام).

<br>

<div align="right"><strong>ب. خطوة الإدارة والتحكم (Management & Control)</strong></div>

- يرسل المستلم رسالة تأكيد استلام (`ACK`) بعد كل مجموعة بيانات.
- يقوم السيرفر بطلب القطعة المفقودة تحديداً بالرقم التسلسلي إذا سقطت في الطريق.
- يصبر البروتوكول ويحاول إعادة إرسال القطعة المفقودة حتى **16 مرة** قبل أن يقطع الاتصال.

<br>

<div align="right"><strong>ج. خطوة إنهاء الجلسة (Session Termination)</strong></div>

- يتم إنهاء الاتصال بسلام وودية عبر 4 رسائل (`4-Way Handshake`).
- يهدف هذا الإنهاء إلى تحرير رقم المنفذ (`Port Number`) ليُصبح متاحاً (`Free`) للاستخدام في تطبيق آخر.

---

### 4. ملخص أحجام التغليف (Encapsulation Sizes)
لفهم كيف يزداد حجم البيانات مع إضافة كل هيدر:
- **حجم الداتا (Data Part):** أقصى حجم هو **`1460 Bytes`**.
- **حجم السيجمنت (Segment):** يُضاف هيدر الـ Transport (بحد أقصى `20 Bytes`) ليصبح **`1480 Bytes`**.
- **حجم الباكيت (Packet):** يُضاف هيدر الـ IP (بحجم `20 Bytes`) ليصبح **`1500 Bytes`** *(وهو الـ MTU)*.
- **حجم الفريم (Frame):** يُضاف هيدر الـ MAC وذيل الـ CRC (بحجم `18 Bytes`) ليصبح **`1518 Bytes`**.

---

### 5. جداول التلخيص (Summary Tables)

#### الجدول الأول: أشهر بروتوكولات طبقة التطبيقات والمنافذ الخاصة بها
<div dir="rtl">

<table border="1" cellpadding="8" cellspacing="0" width="100%">
  <thead>
    <tr bgcolor="#161b22">
      <th align="center">اسم البروتوكول</th>
      <th align="center">رقم المنفذ (Port)</th>
      <th align="center">بروتوكول النقل</th>
      <th align="center">الوظيفة الأساسية</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="center"><strong>HTTP</strong></td>
      <td align="center"><code>80</code></td>
      <td align="center">TCP</td>
      <td>التصفح العادي وجلب صفحات الويب (غير مشفر).</td>
    </tr>
    <tr>
      <td align="center"><strong>HTTPS</strong></td>
      <td align="center"><code>443</code></td>
      <td align="center">TCP</td>
      <td>التصفح الآمن والمشفر لصفحات الويب.</td>
    </tr>
    <tr>
      <td align="center"><strong>FTP</strong></td>
      <td align="center"><code>20, 21</code></td>
      <td align="center">TCP</td>
      <td>رفع وتنزيل الملفات الضخمة بكفاءة عالية.</td>
    </tr>
    <tr>
      <td align="center"><strong>SMTP</strong></td>
      <td align="center"><code>25</code></td>
      <td align="center">TCP</td>
      <td>إرسال رسائل البريد الإلكتروني إلى السيرفر.</td>
    </tr>
    <tr>
      <td align="center"><strong>POP3</strong></td>
      <td align="center"><code>110</code></td>
      <td align="center">TCP</td>
      <td>استقبال واستخلاص الإيميلات من السيرفر.</td>
    </tr>
    <tr>
      <td align="center"><strong>Telnet</strong></td>
      <td align="center"><code>23</code></td>
      <td align="center">TCP</td>
      <td>التحكم عن بُعد في الرواتر والسويتشات لعمل الإعدادات.</td>
    </tr>
  </tbody>
</table>

</div>

<br>

#### الجدول الثاني: مقارنة بين بروتوكولي النقل (TCP vs UDP)
<div dir="rtl">

<table border="1" cellpadding="8" cellspacing="0" width="100%">
  <thead>
    <tr bgcolor="#161b22">
      <th align="center">وجه المقارنة</th>
      <th align="center">بروتوكول الـ TCP</th>
      <th align="center">بروتوكول الـ UDP</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="center"><strong>الميزة الأساسية</strong></td>
      <td align="center">الدقة والاعتمادية العالية.</td>
      <td align="center">السرعة والإنجاز الفوري.</td>
    </tr>
    <tr>
      <td align="center"><strong>حجم الهيدر</strong></td>
      <td align="center">كبير نسبياً (<strong><code>20 Bytes</code></strong>).</td>
      <td align="center">خفيف جداً (<strong><code>8 Bytes</code></strong> فقط).</td>
    </tr>
    <tr>
      <td align="center"><strong>إنشاء الجلسة</strong></td>
      <td align="center">يُنشئ الجلسة بـ <code>3-Way Handshake</code>.</td>
      <td align="center">يُرسل البيانات مباشرة دون مقدمات.</td>
    </tr>
    <tr>
      <td align="center"><strong>التأكيد (ACK)</strong></td>
      <td align="center">ينتظر تأكيد وصول البيانات ويعيد إرسال المفقود.</td>
      <td align="center">لا يطلب تأكيد الوصول ولا يبالي بالمفقود.</td>
    </tr>
    <tr>
      <td align="center"><strong>التطبيق المناسب</strong></td>
      <td align="center">الملفات، النصوص، رسائل البريد (بيانات لا تحتمل الخطأ).</td>
      <td align="center">الألعاب، الفيديو، مكالمات الصوت (بث مباشر).</td>
    </tr>
  </tbody>
</table>

</div>