# 07 - Subnetting Deep Dive (Part 1)

### 1. مقدمة: لماذا احتجنا إلى الـ Subnetting؟
في عام 2000، واجه العالم مشكلة استنفاد عناوين الـ `IPv4` بسبب إهدارها بطريقة التوزيع القديمة (بيع فئات كاملة `Classes` للشركات حتى لو لم تكن بحاجة إليها). 
لحل هذه الأزمة، تم تقديم 3 حلول:
1. **اختراع IPv6:** لزيادة عدد العناوين بشكل لانهائي (مؤجل للمستقبل).
2. **العناوين الخاصة (Private IPs):** استخدام عناوين مجانية تتكرر داخلياً ولا تخرج للإنترنت.
3. **تقسيم الشبكات (Subnetting):** وهو الحل الأهم لتقليل الإهدار في العناوين العامة (Public IPs).

- **مفهوم الـ Subnetting:** بدلاً من إجبار شركة تحتاج إلى 60 آي بي على شراء شبكة كاملة من كلاس C (256 آي بي) وإهدار الباقي، نقوم بـ "تقسيم" الشبكة الأصلية (الميجر نتورك `Major Network`) إلى شبكات أصغر تُسمى `Subnets` لتُباع على قدر الحاجة.

---

### 2. قناع الشبكة (Subnet Mask)
لا يستطيع الكمبيوتر أو الراوتر معرفة ما إذا كانت الشبكة مقسمة أم لا إلا من خلال الـ `Subnet Mask`.

<div align="right"><strong>أ. الأقنعة الافتراضية (Default Masks)</strong></div>

- هي الأقنعة التي تدل على أن الشبكة غير مقسمة ومُستخدمة بحجمها الكامل:
  * **Class A:** القناع الافتراضي هو `/8` (أو `255.0.0.0`). يُعطي حوالي 16 مليون IP.
  * **Class B:** القناع الافتراضي هو `/16` (أو `255.255.0.0`). يُعطي 65,536 IP.
  * **Class C:** القناع الافتراضي هو `/24` (أو `255.255.255.0`). يُعطي 256 IP.

<br>

<div align="right"><strong>ب. طرق كتابة الـ Mask</strong></div>

- **الشكل القديم (Dotted Decimal):** مثل `255.255.255.0` (الأرقام 255 تعني جزء الـ Network، والأصفار تعني جزء الـ Host).
- **الشكل الحديث (CIDR - Slash Notation):** مثل `/24` أو `/26` (يشير الرقم إلى عدد البتات المخصصة لجزء الـ Network).

---

### 3. قوانين الـ Subnetting الذهبية
لحساب أي تقسيم لشبكة، نستخدم هذين القانونين السحريين اللذين يعتمدان على الأسس (Powers of 2):

<div align="right"><strong>القانون الأول: حساب عدد الـ IPs في كل شبكة مُقسمة (Subnet)</strong></div>

- **المعادلة:** عدد الـ IPs = **$2^{(32 - \text{الماسك الجديد})}$**
- **السبب:** الرقم 32 يُمثل إجمالي بتات الـ IP. نطرح منه الماسك لنحصل على البتات المتبقية للأجهزة (`Host Bits`).

<br>

<div align="right"><strong>القانون الثاني: حساب عدد الشبكات الناتجة من التقسيم (Number of Subnets)</strong></div>

- **المعادلة:** عدد الشبكات = **$2^{(\text{الماسك الجديد} - \text{الماسك القديم/الأساسي})}$**
- **السبب:** نطرح الماسك الأصلي من الجديد لنعرف كم `Bit` استلفناها للتقسيم.

---

### 4. أمثلة تطبيقية على التقسيم (Practical Examples)

<div align="right"><strong>المثال الأول: تقسيم شبكة Class C</strong></div>

- **المعطيات:** تملك شبكة `200.7.8.0` (ديفولت ماسك `/24`). أردت تقسيمها باستخدام ماسك جديد `/26`.
- **حساب عدد الـ IPs في كل شبكة جديدة:** 
  * $2^{(32 - 26)} = 2^6 = \text{64 IPs}$ في كل شبكة فرعية.
- **حساب عدد الشبكات الناتجة:**
  * $2^{(26 - 24)} = 2^2 = \text{4 Subnets}$.
- **النتيجة:** تم تقسيم الـ 256 آي بي إلى 4 شبكات صغيرة، كل شبكة تحتوي على 64 آي بي.

<br>

<div align="right"><strong>المثال الثاني: تقسيم شبكة Class B</strong></div>

- **المعطيات:** تملك شبكة `173.15.0.0` (ديفولت ماسك `/16`). أردت تقسيمها باستخدام ماسك جديد `/23`.
- **حساب عدد الـ IPs في كل شبكة جديدة:** 
  * $2^{(32 - 23)} = 2^9 = \text{512 IPs}$ في كل شبكة فرعية.
- **حساب عدد الشبكات الناتجة:**
  * $2^{(23 - 16)} = 2^7 = \text{128 Subnets}$.
- **النتيجة:** تم تقسيم الـ 65,536 آي بي إلى 128 شبكة، كل شبكة تحتوي على 512 آي بي.

---

### 5. بروتوكول الـ APIPA (تفاصيل إضافية)
- **وظيفته:** إذا فشل جهازك في الحصول على آي بي من سيرفر الـ `DHCP` (بسبب عطل أو انقطاع كابل)، يقوم نظام التشغيل بمنح نفسه آي بي مؤقت يسمى **APIPA**.
- **نطاقه:** يبدأ دائماً بـ **`169.254.x.x`** وماسك `/16`.
- **العيب الأساسي (الميزة الأمنية):** أجهزة الـ APIPA **لا يمكنها أبداً الخروج للإنترنت** أو تخطي الراوتر، لأن هذه التقنية لا تمنح الجهاز لا `Default Gateway` ولا `DNS`. دورها يقتصر على ربط الأجهزة ببعضها محلياً داخل الـ `LAN` فقط لتبادل الملفات.

<br>
***
<br>

### 6. جداول التلخيص (Summary Tables)

#### الجدول الأول: قوانين الـ Subnetting
<div dir="rtl">

<table border="1" cellpadding="8" cellspacing="0" width="100%">
  <thead>
    <tr bgcolor="#161b22">
      <th align="center">الهدف من الحساب</th>
      <th align="center">المعادلة (القانون)</th>
      <th align="center">مثال توضيحي (لشبكة <code>/24</code> قُسمت بـ <code>/26</code>)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="center"><strong>عدد الأجهزة (IPs) في كل Subnet</strong></td>
      <td align="center"><strong>$2^{(32 - \text{New Mask})}$</strong></td>
      <td align="center">$2^{(32 - 26)} = 2^6 = \text{64 IPs}$</td>
    </tr>
    <tr>
      <td align="center"><strong>عدد الشبكات الناتجة (Subnets)</strong></td>
      <td align="center"><strong>$2^{(\text{New Mask} - \text{Old Mask})}$</strong></td>
      <td align="center">$2^{(26 - 24)} = 2^2 = \text{4 Subnets}$</td>
    </tr>
    <tr>
      <td align="center"><strong>عدد الأجهزة الفعالة (Valid Hosts)</strong></td>
      <td align="center"><strong>عدد الـ IPs - 2</strong></td>
      <td align="center">$64 - 2 = \text{62 Valid Hosts}$</td>
    </tr>
  </tbody>
</table>

</div>

<br>

#### الجدول الثاني: تحويلات الماسك الشائعة وحجمها
<div dir="rtl">

<table border="1" cellpadding="8" cellspacing="0" width="100%">
  <thead>
    <tr bgcolor="#161b22">
      <th align="center">الماسك (CIDR)</th>
      <th align="center">عدد الآيبيهات الناتجة ($2^{(32-Mask)}$)</th>
      <th align="center">ملاحظات واستخدامات</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="center"><strong><code>/30</code></strong></td>
      <td align="center">4 IPs</td>
      <td>يُستخدم للربط المباشر بين راوترين (P2P).</td>
    </tr>
    <tr>
      <td align="center"><strong><code>/29</code></strong></td>
      <td align="center">8 IPs</td>
      <td>مكاتب صغيرة جداً.</td>
    </tr>
    <tr>
      <td align="center"><strong><code>/28</code></strong></td>
      <td align="center">16 IPs</td>
      <td>أقسام صغيرة في شركة.</td>
    </tr>
    <tr>
      <td align="center"><strong><code>/27</code></strong></td>
      <td align="center">32 IPs</td>
      <td>فروع متوسطة.</td>
    </tr>
    <tr>
      <td align="center"><strong><code>/26</code></strong></td>
      <td align="center">64 IPs</td>
      <td>تقسيم كلاس C إلى 4 أرباع.</td>
    </tr>
    <tr>
      <td align="center"><strong><code>/25</code></strong></td>
      <td align="center">128 IPs</td>
      <td>تقسيم كلاس C إلى نصفين.</td>
    </tr>
    <tr>
      <td align="center"><strong><code>/24</code></strong></td>
      <td align="center">256 IPs</td>
      <td>القناع الافتراضي لكلاس C.</td>
    </tr>
  </tbody>
</table>

</div>