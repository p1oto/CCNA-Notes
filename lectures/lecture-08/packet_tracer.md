# 01 - Cisco Packet Tracer & CLI Basics

### 1. مقدمة عن برنامج Cisco Packet Tracer
برنامج المحاكاة الرسمي من سيسكو، يُستخدم لبناء وتجربة الشبكات الافتراضية قبل تطبيقها على أرض الواقع. يحتوي على أجهزة سيسكو (رواتر، سويتشات، أجهزة حماية) تعمل بنفس نظام التشغيل الحقيقي (IOS).

---

### 2. أنواع الكابلات وقواعد التوصيل
عند ربط الأجهزة ببعضها داخل البرنامج، نستخدم الكابلات من قسم الـ `Connections` (أيقونة الصاعقة). القاعدة الذهبية للتوصيل تعتمد على تشابه الأجهزة:


<div dir="rtl">

<table border="1" cellpadding="8" cellspacing="0" width="100%">
  <thead>
    <tr bgcolor="#161b22">
      <th align="center">نوع الكابل</th>
      <th align="center">القاعدة العامة للاستخدام</th>
      <th align="center">أمثلة التوصيل</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="center"><strong>Straight-through (عدل)</strong></td>
      <td>يُستخدم لربط الأجهزة <strong>المختلفة</strong> في النوع أو الطبقة.</td>
      <td>كمبيوتر بسويتش / سويتش براوتر.</td>
    </tr>
    <tr>
      <td align="center"><strong>Crossover (معكوس)</strong></td>
      <td>يُستخدم لربط الأجهزة <strong>المتشابهة</strong> لضمان تقاطع خطوط الإرسال والاستقبال.</td>
      <td>كمبيوتر بكمبيوتر / سويتش بسويتش / راوتر براوتر.</td>
    </tr>
  </tbody>
</table>

</div>

---

### 3. مستويات الأوامر (Cisco CLI Modes)
نظام تشغيل رواتر وسويتشات سيسكو يعتمد على سطر الأوامر (`Command Line Interface`). لضمان الأمان، تم تقسيم الواجهة إلى 3 مستويات متدرجة الصلاحيات:

<div align="right"><strong>المستوى الأول: User EXEC Mode</strong></div>

- **شكله:** <span dir="ltr"><code>Router></code></span>
- **صلاحياته:** مستوى ضعيف جداً (حوالي 500 أمر). يُستخدم فقط للعرض السطحي ولا يمكنه تغيير أي إعدادات.

<br>

<div align="right"><strong>المستوى الثاني: Privileged EXEC Mode</strong></div>

- **شكله:** <span dir="ltr"><code>Router#</code></span>
- **كيفية الدخول إليه:** بكتابة أمر <span dir="ltr"><code>enable</code></span> من المستوى الأول.
- **صلاحياته:** مستوى متوسط (حوالي 15,000 أمر). يُستخدم لعرض كل تفاصيل الراوتر (أوامر الـ `Show`) ومسح الإعدادات، لكنه لا يمكنه التعديل عليها.

<br>

<div align="right"><strong>المستوى الثالث: Global Configuration Mode</strong></div>

- **شكله:** <span dir="ltr"><code>Router(config)#</code></span>
- **كيفية الدخول إليه:** بكتابة أمر <span dir="ltr"><code>configure terminal</code></span> من المستوى الثاني.
- **صلاحياته:** مستوى الإدارة الكامل (نصف مليون أمر). هنا نقوم بتغيير اسم الراوتر، إعطاء الـ IPs، وفتح المنافذ (أي تعديل جذري يتم هنا). للخروج منه نكتب <span dir="ltr"><code>exit</code></span>.

---

### 4. الأوامر الأساسية للتشغيل (Basic Configuration Commands)
هذا الجدول يلخص الأوامر الأولى التي يجب كتابتها عند التعامل مع أي راوتر جديد:

<div dir="rtl">

<table border="1" cellpadding="8" cellspacing="0" width="100%">
  <thead>
    <tr bgcolor="#161b22">
      <th align="center">الأمر (Command)</th>
      <th align="center">المستوى المطلوب</th>
      <th align="center">الوظيفة</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="center"><span dir="ltr"><code>enable</code></span></td>
      <td align="center">المستوى الأول <code>></code></td>
      <td>ينقلك من مستوى الـ User إلى مستوى الـ Privileged <code>#</code>.</td>
    </tr>
    <tr>
      <td align="center"><span dir="ltr"><code>configure terminal</code></span></td>
      <td align="center">المستوى الثاني <code>#</code></td>
      <td>ينقلك لمستوى الـ Configuration <code>(config)#</code> لعمل الإعدادات.</td>
    </tr>
    <tr>
      <td align="center"><span dir="ltr"><code>hostname [Name]</code></span></td>
      <td align="center">المستوى الثالث <code>(config)#</code></td>
      <td>لتغيير اسم الراوتر (يُفضل تسميته باسم فرع الشركة).</td>
    </tr>
    <tr>
      <td align="center"><span dir="ltr"><code>enable secret [Password]</code></span></td>
      <td align="center">المستوى الثالث <code>(config)#</code></td>
      <td>لإنشاء رقم سري مُشفر يحمي الدخول للمستوى الثاني (enable).</td>
    </tr>
    <tr>
      <td align="center"><span dir="ltr"><code>interface [Name] [Port]</code></span></td>
      <td align="center">المستوى الثالث <code>(config)#</code></td>
      <td>للدخول إلى إعدادات كارت شبكة محدد (مثال: <code>interface g0/0</code>).</td>
    </tr>
    <tr>
      <td align="center"><span dir="ltr"><code>ip address [IP] [Mask]</code></span></td>
      <td align="center">مستوى الـ Interface</td>
      <td>لإعطاء الكارت عنوان IP وقناع (Subnet Mask).</td>
    </tr>
    <tr>
      <td align="center"><span dir="ltr"><code>no shutdown</code></span></td>
      <td align="center">مستوى الـ Interface</td>
      <td>لتشغيل كارت الشبكة وفتحه (إلغاء وضع الإغلاق الافتراضي <code>shutdown</code>).</td>
    </tr>
    <tr>
      <td align="center"><span dir="ltr"><code>no [Command]</code></span></td>
      <td align="center">أي مستوى إعدادات</td>
      <td>كتابة <code>no</code> قبل أي أمر تقوم بإلغائه وإعادته لضبط المصنع.</td>
    </tr>
  </tbody>
</table>

</div>