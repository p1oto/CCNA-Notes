# 05 - Network Layer & IPv4 Basics

### 1. طبقة الإنترنت (Internet / Network Layer)
هذه الطبقة (Layer 3 في الـ OSI) تُسمى في الـ TCP/IP بطبقة الـ `Internet Layer`، والبروتوكول الأساسي الذي يعمل بها هو بروتوكول الـ **`IP` (Internet Protocol)**.

- **وظيفة بروتوكول الـ IP الأساسية:** مسؤول عن توصيل البيانات من المصدر إلى الوجهة النهائية (`End-to-End Data Delivery`).
- **العناوين المنطقية (Logical Address):** الـ IP هو عنوان متغير يتغير بتغير مكان الجهاز والشبكة التي يتصل بها (على عكس الـ `MAC Address` الثابت من المصنع).
- **وحدة القياس:** بمجرد أن يضيف الـ `IP` الهيدر الخاص به (بحجم `20 Bytes`) على الـ `Segment`، يُصبح اسم البيانات **`Packet`**.

---

### 2. محتويات هيدر الـ IP (IP Header Fields)
الهيدر الأحمر (IP Header) يحتوي على معلومات مصيرية لإيصال البيانات، أهمها 4 خانات:

<div align="right"><strong>أ. عنوان المصدر والوجهة (Source & Destination IP)</strong></div>

- يحدد عنوان الـ IP للجهاز الذي أرسل البيانات، وعنوان الـ IP للوجهة النهائية المُراد الوصول إليها.

<br>

<div align="right"><strong>ب. خانة الـ TOS (Type of Service / QoS)</strong></div>

- هي خانة بحجم `1 Byte` (مُستخدم منها `3 Bits` فقط) لتحديد **أهمية البيانات** وإعطائها أولوية في المرور عبر الرواتر (`Priority / Quality of Service`).
- **نظام الأولوية (من 0 إلى 7):**
  * **رقم 7 و 6:** أعلى أولوية، وتُعطى لبروتوكولات الرواتر (مثل `OSPF`) لتبني جداولها أولاً وتمنع سقوط الشبكة.
  * **رقم 5 و 4:** أولوية عالية لبيانات الـ `Voice` (الصوت) والـ `Video` (لأنها لا تحتمل التأخير).
  * **رقم 3 إلى 0:** أولوية عادية لبيانات الـ `Data` العادية، فمثلاً التصفح يأخذ (3)، والتحميل `FTP` يأخذ (0).

<br>

<div align="right"><strong>ج. خانة الـ TTL (Time To Live)</strong></div>

- هي خانة عداد تنازلي لمنع حدوث مشكلة دوران البيانات بلا توقف (`Layer 3 Loops`) في حال وجود خطأ في توجيه الرواتر.
- **آلية العمل:** تخرج الباكيت من الكمبيوتر بقيمة افتراضية (ستاندرد) = **`255`** *(وفي أنظمة Windows تخرج بقيمة `128`)*. 
- كلما عبرت الباكيت على راوتر، يقوم الراوتر بطرح `1` من القيمة. إذا وصلت القيمة إلى `0`، يقوم الراوتر بإعدام الباكيت (Drop) لإنهاء اللوب.

<br>

<div align="right"><strong>د. خانة الـ CRC (Cyclic Redundancy Check)</strong></div>

- صورة مصغرة إضافية من الهيدر لضمان عدم حدوث تلاعب أو خطأ في عناوين الـ IP أثناء النقل.

---

### 3. أساسيات عناوين الـ IPv4 (IPv4 Addressing)
- يتكون عنوان الـ IPv4 من **`32 Bits`** (أصفار ووحايد).
- لتبسيط القراءة للبشر، يتم تقسيمه إلى **4 خانات (Octets)**، كل خانة تتكون من `8 Bits`.
- إجمالي عدد الاحتمالات (عناوين الـ IP المتاحة) في الإصدار الرابع = $2^{32}$ = حوالي **4.3 مليار عنوان IP**.
- **طريقة التحويل من Binary إلى Decimal:** نستخدم نظام المضاعفات في كل خانة: `(1, 2, 4, 8, 16, 32, 64, 128)`.
  * *أقل احتمال (كلها أصفار) = `0`.*
  * *أعلى احتمال (كلها وحايد) = `255`.*

---

### 4. فئات الـ IP وتصنيفاتها (IPv4 Classes)
قامت منظمة **`IANA`** بتقسيم الـ 4 مليار IP إلى فئات (Classes) لتسهيل بيعها للشركات حسب حجمها. نُحدد الـ Class من خلال النظر لـ **الخانة الأولى (First Octet)** فقط:

<div align="right"><strong>• فئة Class A (للشركات العملاقة)</strong></div>

- **نطاق الخانة الأولى:** من **`1`** إلى **`126`**.
- **طريقة قراءة الرواتر:** الخانة الأولى لاسم الشبكة (`Network`)، والثلاث خانات الباقية للأجهزة (`Hosts`). (N . H . H . H).
- **عدد الأجهزة المتاحة:** $2^{24} - 2$ = حوالي **16 مليون جهاز** للشبكة الواحدة.

<br>

<div align="right"><strong>• فئة Class B (للشركات المتوسطة)</strong></div>

- **نطاق الخانة الأولى:** من **`128`** إلى **`191`**.
- **طريقة قراءة الرواتر:** أول خانتين للشبكة (`Network`)، وآخر خانتين للأجهزة (`Hosts`). (N . N . H . H).
- **عدد الأجهزة المتاحة:** $2^{16} - 2$ = **65,534 جهاز** للشبكة الواحدة.

<br>

<div align="right"><strong>• فئة Class C (للشركات الصغيرة)</strong></div>

- **نطاق الخانة الأولى:** من **`192`** إلى **`223`**.
- **طريقة قراءة الرواتر:** أول 3 خانات للشبكة (`Network`)، والخانة الأخيرة فقط للأجهزة (`Hosts`). (N . N . N . H).
- **عدد الأجهزة المتاحة:** $2^8 - 2$ = **254 جهاز** للشبكة الواحدة.

<br>

<div align="right"><strong>• العناوين الاستثنائية والخاصة (Special & Reserved IPs)</strong></div>

- **عنوان الـ Loopback (127.x.x.x):** محجوز بالكامل للكمبيوتر لعمل اختبار (`Test / Ping`) لكارت الشبكة والـ TCP/IP Stack الداخلي (Localhost).
- **فئة Class D (224 إلى 239):** محجوزة لعمليات البث الجماعي (`Multicast`) مثل تطبيقات الـ Zoom وبروتوكولات الرواتر.
- **فئة Class E (240 إلى 255):** محجوزة للتجارب العلمية والتطبيقات العسكرية (`Military & Research`).
- **عناوين الأصفار (0.x.x.x):** محجوزة ولا يمكن إعطاؤها لأي جهاز.

---

### 5. قاعدة الـ Minus 2 (لماذا نطرح 2؟)
عند حساب عدد الأجهزة المتاحة في أي شبكة، نستخدم المعادلة **$2^H - 2$**. سبب طرح جهازين هو أن:
1. **أول IP (كل الـ Hosts أصفار):** محجوز ليكون اسم/عنوان الشبكة بالكامل (`Network ID`) ولا يأخذه جهاز.
2. **آخر IP (كل الـ Hosts وحايد):** محجوز ليكون عنوان البث الجماعي للشبكة (`Broadcast ID`) ولا يُعطى لجهاز.

<br>
***
<br>

### 6. جداول التلخيص (Summary Tables)

#### الجدول الأول: أهم محتويات الـ IP Header
<div dir="rtl">

<table border="1" cellpadding="8" cellspacing="0" width="100%">
  <thead>
    <tr bgcolor="#161b22">
      <th align="center">اسم الخانة</th>
      <th align="center">الوظيفة الأساسية</th>
      <th align="center">ملاحظات</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="center"><strong>Source IP</strong></td>
      <td>عنوان الجهاز المُرسل للبيانات.</td>
      <td align="center">ثابت لا يتغير أثناء الرحلة.</td>
    </tr>
    <tr>
      <td align="center"><strong>Destination IP</strong></td>
      <td>عنوان الجهاز المُستقبل للبيانات.</td>
      <td align="center">ثابت لا يتغير أثناء الرحلة.</td>
    </tr>
    <tr>
      <td align="center"><strong>TTL (Time To Live)</strong></td>
      <td>منع دوران الباكيت في حلقة مفرغة (Layer 3 Loops).</td>
      <td align="center">قيمته القياسية 255 (وفي الويندوز 128) وينقص مع كل راوتر.</td>
    </tr>
    <tr>
      <td align="center"><strong>TOS (Type of Service)</strong></td>
      <td>تحديد أولوية وأهمية البيانات (QoS).</td>
      <td align="center">الرواتر 7، الفويس/فيديو 5 و 4، الداتا العادية 0 إلى 3.</td>
    </tr>
  </tbody>
</table>

</div>

<br>

#### الجدول الثاني: فئات عناوين الـ IPv4 (Classes)
<div dir="rtl">

<table border="1" cellpadding="8" cellspacing="0" width="100%">
  <thead>
    <tr bgcolor="#161b22">
      <th align="center">الفئة (Class)</th>
      <th align="center">نطاق الخانة الأولى</th>
      <th align="center">شكل القراءة (Net.Host)</th>
      <th align="center">عدد الأجهزة (Hosts)</th>
      <th align="center">الاستخدام</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="center"><strong>Class A</strong></td>
      <td align="center"><code>1</code> إلى <code>126</code></td>
      <td align="center"><strong>N</strong> . H . H . H</td>
      <td align="center">~ 16 مليون</td>
      <td>الشركات الضخمة ومزودي الخدمة.</td>
    </tr>
    <tr>
      <td align="center"><strong>Class B</strong></td>
      <td align="center"><code>128</code> إلى <code>191</code></td>
      <td align="center"><strong>N . N</strong> . H . H</td>
      <td align="center">65,534</td>
      <td>الشركات والمؤسسات المتوسطة.</td>
    </tr>
    <tr>
      <td align="center"><strong>Class C</strong></td>
      <td align="center"><code>192</code> إلى <code>223</code></td>
      <td align="center"><strong>N . N . N</strong> . H</td>
      <td align="center">254</td>
      <td>الشركات الصغيرة والمنازل.</td>
    </tr>
    <tr>
      <td align="center"><strong>Loopback</strong></td>
      <td align="center"><code>127</code></td>
      <td align="center">-</td>
      <td align="center">-</td>
      <td>اختبار كارت الشبكة محلياً (Localhost).</td>
    </tr>
    <tr>
      <td align="center"><strong>Class D</strong></td>
      <td align="center"><code>224</code> إلى <code>239</code></td>
      <td align="center">-</td>
      <td align="center">-</td>
      <td>البث الجماعي (Multicast).</td>
    </tr>
    <tr>
      <td align="center"><strong>Class E</strong></td>
      <td align="center"><code>240</code> إلى <code>255</code></td>
      <td align="center">-</td>
      <td align="center">-</td>
      <td>التجارب العلمية والعسكرية.</td>
    </tr>
  </tbody>
</table>

</div>