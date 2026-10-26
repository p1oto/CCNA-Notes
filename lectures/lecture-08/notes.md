# 08 - MAC Address, Frame Structure, ARP & Network Media

### 1. ما هو الـ MAC Address؟
العنوان الفيزيائي (Physical Address) أو الـ `MAC Address` هو بمثابة "الاسم" الثابت للجهاز الذي لا يتغير بتغير مكانه (على عكس الـ IP).

- **مواصفاته:** 
  * يتكون من **`48 Bits`**، ويُكتب بنظام الـ Hexadecimal (السادس عشري) لتبسيط قراءته.
  * يظهر على شكل **12 حرفاً/رقماً** (مثال: <span dir="ltr"><code>A1:B2:C3:D4:E5:F6</code></span>).
  * مطبوع وثابت من المصنع (Burned-in) على شريحة الـ `ROM` الموجودة داخل كارت الشبكة (`NIC`).
- **كيف يتم تنظيمه؟** منظمة **`IEEE`** هي المسؤولة عن تنظيمه، وتقسمه إلى نصفين:
  1. **نصف للشركة المصنعة (OUI - Organizationally Unique Identifier):** أول `24 Bits` (أول 6 حروف) تمثل كود الشركة (مثل Cisco أو Apple).
  2. **نصف للجهاز (Host Part):** آخر `24 Bits` (آخر 6 حروف) تضعها الشركة برقم تسلسلي لكل كارت لتضمن عدم تكراره. النصف الواحد يعطي الشركة الحق في إنتاج `16.7` مليون كارت، وإذا انتهت تشتري كود `OUI` جديد من المنظمة.

---

### 2. أنواع توجيه الـ MAC Address
كيف يتم تحديد المستلم على مستوى الـ Switch (Layer 2)؟

<div align="right"><strong>أ. الإرسال الفردي (Unicast MAC)</strong></div>

- توجيه رسالة من جهاز لجهاز واحد فقط، ويكون الـ Destination MAC هو عنوان فريد يخص جهازاً بعينه. (باقي الأجهزة في الشبكة تتجاهل الرسالة).

<br>

<div align="right"><strong>ب. البث الإذاعي (Broadcast MAC)</strong></div>

- توجيه رسالة تجبر **جميع الأجهزة** على فتحها ومعالجتها (`Process`).
- يكون الـ Destination MAC عبارة عن حرف F متكرر 12 مرة: <span dir="ltr"><code>FF:FF:FF:FF:FF:FF</code></span>.

<br>

<div align="right"><strong>ج. البث الجماعي (Multicast MAC)</strong></div>

- توجيه رسالة لمجموعة معينة من الأجهزة (مثل الأجهزة المنضمة لاجتماع Zoom).
- يبدأ الماك أدرس دائماً بالكود <span dir="ltr"><code>01:00:5E</code></span> ليدل على أنه مخصص للـ Multicast.

---

### 3. بروتوكول الـ ARP (Address Resolution Protocol)
كيف يعرف جهازك الـ MAC Address الخاص بجهاز آخر أو بالراوتر لكي يُكمل بناء الـ Frame؟ 
الإجابة هي بروتوكول **`ARP`**.

1. **الطلب (ARP Request):** يرسل جهازك رسالة `Broadcast` للجميع يسأل فيها: "من يملك هذا الـ IP؟ أرسل لي الـ MAC الخاص بك".
2. **الرد (ARP Reply):** الجهاز صاحب الـ IP يرى الرسالة ويرد بـ `Unicast` قائلاً: "هذا أنا، وهذا هو الـ MAC الخاص بي".
3. **التخزين (ARP Cache):** يقوم جهازك بتخزين هذه المعلومة في جدول لديه يُسمى `ARP Table` لكي لا يسأل عنها مرة أخرى.

---

### 4. بنية الـ Frame وأحجامها (Frame Structure)
البيانات وهي تخرج من كارت الشبكة للمحطة التالية يجب أن تأخذ شكل الـ `Frame`. 

- **حدود الحجم:**
  * **الحد الأقصى:** `1518 Bytes` (للحفاظ على جودة الأداء في الكابلات).
  * **الحد الأدنى:** `64 Bytes` (البيانات التي تقل عن هذا الحجم تضر بأداء الشبكة وتعتبر إهداراً للموارد، لذلك يجب تجنب إرسال بيانات متناهية الصغر مثل نقطة واحدة في الشات).
- **أجزاء الـ Frame الأساسية:**
  1. **التسخين (Preamble):** حجمه `8 Bytes` (غير محسوب في الـ 1518). وظيفته تهيئة الكارت المستقبل لمعرفة سرعة الإرسال عبر إرسال نبضات `10101010` (قيام/جلوس).
  2. **الهيدر الأخضر (Header):** حجمه `14 Bytes`، ويحتوي على:
     - `Destination MAC` (6 Bytes)
     - `Source MAC` (6 Bytes)
     - `Type` (2 Bytes): لتحديد ما إذا كان الـ IP القادم من نوع IPv4 أو IPv6.
  3. **البيانات (Payload/Packet):** حجمها من `46` إلى `1500 Bytes`.
  4. **الذيل (FCS/Trailer):** حجمه `4 Bytes` ويحتوي على معادلة الـ `CRC` لاكتشاف الأخطاء.
- **وقت الراحة (Idle Time):** بعد إرسال كل فريم، يأخذ الكارت "نَفَس" (استراحة قصيرة) تعادل وقت إرسال `8 Bytes` لتفريغ الكابل من الشحنات الكهربائية قبل إرسال الفريم التالي.

---

### 5. وسائط النقل وكروت الشبكة (Network Media & Interfaces)
منظمة IEEE هي المسؤولة عن معايير الكابلات وكروت الشبكة، وخصصت كود <span dir="ltr"><code>802.3</code></span> لشبكات الـ Wired الإيثرنت.

<div align="right"><strong>أ. الكابلات النحاسية (Twisted Pair)</strong></div>

- تُسمى منافذها بالحرف `T` (مثل <span dir="ltr"><code>100Base-T</code></span>).
- تُقاس جودة الكابل بالفئة (`Category`)، وكلما زادت الفئة زاد نقاء النحاس والسرعة المدعومة:
  * **فئة Cat 3:** سرعة تصل لـ `8 Mbps` (قديم جداً).
  * **فئة Cat 5:** سرعة تصل لـ `100 Mbps`.
  * **فئة Cat 6:** سرعة تصل من `1 Gbps` إلى `4 Gbps`.
  * **فئة Cat 6a / 7 / 8:** سرعات تصل من `10 Gbps` حتى `40 Gbps` (وهذا هو أقصى حد للنحاس).
- **قاعدة هامة:** يجب أن تتوافق سرعة الكابل مع سرعة كارت الشبكة. تركيب كابل ضعيف (مثل فئة Cat 3) على كارت سريع (Gigabit) سيؤدي لفشل الاتصال.

<br>

<div align="right"><strong>ب. كابلات الألياف الضوئية (Fiber Optics)</strong></div>

- تُستخدم للمسافات الطويلة جداً والسرعات الفائقة التي يعجز عنها النحاس. يتكون الكابل من قلب زجاجي (`Core`) وقشرة عاكسة (`Cladding`).
- **أنواعه الأساسية:**
  1. **النوع الأول MMF (Multi-Mode Fiber):** الكور الخاص به سميك (من 50 إلى 62.5 ميكرون). يتشتت فيه الضوء قليلاً، لذا يُستخدم للمسافات القصيرة نسبياً (من `400 متر` إلى `4 كم`).
  2. **النوع الثاني SMF (Single-Mode Fiber):** الكور الخاص به رفيع جداً (من 8 إلى 9 ميكرون). يسير فيه الضوء في خط مستقيم بدون تشتت، ويصل لمسافات شاسعة (من `10 كم` إلى `100 كم`).
- **مسميات كروت الفايبر:**
  * **كارت SX** (Short): للمسافات القصيرة (MMF).
  * **كارت LX** (Long): للمسافات الطويلة (SMF).
  * **كارت ZX** (Extra Long): للمسافات الشاسعة (SMF).

<br>

---

<br>

### 6. جداول التلخيص (Summary Tables)

#### الجدول الأول: سرعات ومسميات كروت الشبكة
<div dir="rtl">

<table border="1" cellpadding="8" cellspacing="0" width="100%">
  <thead>
    <tr bgcolor="#161b22">
      <th align="center">اسم الكارت التقني</th>
      <th align="center">المسمى التجاري</th>
      <th align="center">السرعة</th>
      <th align="center">نوع الكابل</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="center"><span dir="ltr"><code>10Base-T</code></span></td>
      <td align="center">Ethernet</td>
      <td align="center">10 Mbps</td>
      <td>نحاس (Twisted Pair)</td>
    </tr>
    <tr>
      <td align="center"><span dir="ltr"><code>100Base-T</code></span></td>
      <td align="center">Fast Ethernet</td>
      <td align="center">100 Mbps</td>
      <td>نحاس (Twisted Pair)</td>
    </tr>
    <tr>
      <td align="center"><span dir="ltr"><code>1000Base-T</code></span></td>
      <td align="center">Gigabit Ethernet</td>
      <td align="center">1 Gbps (1000 Mbps)</td>
      <td>نحاس (Twisted Pair)</td>
    </tr>
    <tr>
      <td align="center"><span dir="ltr"><code>10GBase-T</code></span></td>
      <td align="center">10 Gigabit Ethernet</td>
      <td align="center">10 Gbps</td>
      <td>نحاس (Twisted Pair)</td>
    </tr>
    <tr>
      <td align="center"><span dir="ltr"><code>1000Base-SX / LX / ZX</code></span></td>
      <td align="center">Fiber Gigabit</td>
      <td align="center">1 Gbps</td>
      <td>ألياف ضوئية (Fiber)</td>
    </tr>
  </tbody>
</table>

</div>