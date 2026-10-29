# 11 - Advanced Static Routing & Dynamic Routing Protocols

<div dir="rtl" align="right">

### 1. المسار الاحتياطي (Floating Static Route)
<ul>
  <li><strong>الفكرة:</strong> عمل طريق أساسي (Primary) وطريق احتياطي (Backup) يعمل فقط في حالة تعطل أو انقطاع كابل الطريق الأساسي (لتوفير الـ Redundancy).</li>
  <li><strong>كيفية التنفيذ:</strong> تتم عن طريق إضافة رقم (Distance) في نهاية أمر التوجيه اليدوي. الراوتر يفضل دائماً الطريق صاحب الرقم الأقل. القيمة الافتراضية للـ Static هي <code>1</code>.</li>
  <li><strong>مثال عملي:</strong>
    <ul>
      <li>الطريق الأساسي: <span dir="ltr"><code>ip route 192.168.2.0 255.255.255.0 g0/1</code></span> (المسافة هنا افتراضياً 1).</li>
      <li>الطريق الاحتياطي: <span dir="ltr"><code>ip route 192.168.2.0 255.255.255.0 g0/2 3</code></span> (تم رفع المسافة إلى 3، لذلك لن يظهر في الجدول إلا إذا تعطل الكابل الأول).</li>
    </ul>
  </li>
</ul>

<hr>

### 2. توزيع الحمل (Load Balancing / Load Sharing)
<ul>
  <li>إذا توفر أكثر من مسار للوصول لنفس الشبكة وبنفس رقم الـ Distance (مثلاً كلاهما 1)، يقوم الراوتر بتوزيع البيانات بالتساوي (Packet by Packet) على المسارين معاً.</li>
  <li>الراوتر يستطيع توزيع الحمل ومشاركة الداتا على عدد يصل إلى <strong>16 مسار</strong> متطابق في نفس الوقت.</li>
</ul>

<hr>

### 3. التوجيه الديناميكي (Dynamic Routing)
بدلاً من كتابة كل مسار يدوياً، نستخدم بروتوكولات (سوفت وير) داخل الراوتر لتبادل "تحديثات" (Updates) بمسارات الشبكات المتاحة.
<ul>
  <li>الرواتر لا ترسل هذه التحديثات بشكل عشوائي، بل تستخدم عناوين <strong>Multicast</strong> محجوزة ليتحدث كل بروتوكول مع الرواتر التي تفهم نفس لغته (مثال: بروتوكول RIPv2 يستخدم آي بي <span dir="ltr"><code>224.0.0.9</code></span>).</li>
  <li>يتعامل الراوتر مع جدول التوجيه المُرسل إليه كأنه بيانات عادية، يغلفه بـ Headers و يرسله في Frame.</li>
</ul>

<hr>

### 4. جدول بروتوكولات التوجيه الثمانية (Routing Protocols)
تنقسم البروتوكولات من حيث فهمها لقناع الشبكة (Subnet Mask) إلى فئتين:
<ul>
  <li><strong>القديمة (<span dir="ltr">Classful</span>):</strong> لا تفهم الـ Subnet Mask، وتتعامل مع الشبكات القياسية فقط (Major Networks)، وانقرض استخدامها.</li>
  <li><strong>الحديثة (<span dir="ltr">Classless</span>):</strong> تفهم الـ Subnet Mask وتدعم تقسيم الشبكات (VLSM).</li>
</ul>

<table border="1" width="100%">
  <thead>
    <tr bgcolor="#161b22">
      <th align="center">اسم البروتوكول</th>
      <th align="center">الفئة (Category)</th>
      <th align="right">الخصائص والملاحظات الأساسية</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="center"><strong><span dir="ltr">RIP v1</span></strong></td>
      <td align="center"><span dir="ltr"><code>Classful</code></span></td>
      <td align="right">بروتوكول قديم يعتمد على عدد القفزات ولا يفهم التقسيمات الحديثة للشبكات.</td>
    </tr>
    <tr>
      <td align="center"><strong><span dir="ltr">IGRP</span></strong></td>
      <td align="center"><span dir="ltr"><code>Classful</code></span></td>
      <td align="right">بروتوكول قديم خاص بشركة سيسكو وتم إيقاف دعمه.</td>
    </tr>
    <tr>
      <td align="center"><strong><span dir="ltr">EGP</span></strong></td>
      <td align="center"><span dir="ltr"><code>Classful</code></span></td>
      <td align="right">بروتوكول قديم جداً كان يُستخدم للربط بين مزودي الخدمة (ISPs).</td>
    </tr>
    <tr>
      <td align="center"><strong><span dir="ltr">RIP v2</span></strong></td>
      <td align="center"><span dir="ltr"><code>Classless</code></span></td>
      <td align="right">النسخة الأحدث من RIP. أقصى مدى له <strong>15 قفزة (Hops)</strong>، ويرسل تحديثاته على <span dir="ltr"><code>224.0.0.9</code></span>.</td>
    </tr>
    <tr>
      <td align="center"><strong><span dir="ltr">EIGRP</span></strong></td>
      <td align="center"><span dir="ltr"><code>Classless</code></span></td>
      <td align="right">النسخة المتطورة من IGRP. يتميز بسرعته الفائقة، ويرسل تحديثاته على <span dir="ltr"><code>224.0.0.10</code></span>.</td>
    </tr>
    <tr>
      <td align="center"><strong><span dir="ltr">OSPF</span></strong></td>
      <td align="center"><span dir="ltr"><code>Classless</code></span></td>
      <td align="right">البروتوكول الأشهر والأكثر استخداماً داخل الشركات. يرسم خريطة دقيقة للشبكة، ويرسل تحديثاته على <span dir="ltr"><code>224.0.0.5</code></span>.</td>
    </tr>
    <tr>
      <td align="center"><strong><span dir="ltr">IS-IS</span></strong></td>
      <td align="center"><span dir="ltr"><code>Classless</code></span></td>
      <td align="right">مشابه جداً لبروتوكول OSPF ويستخدم بكثرة في شبكات مزودي الخدمة (ISPs).</td>
    </tr>
    <tr>
      <td align="center"><strong><span dir="ltr">BGP</span></strong></td>
      <td align="center"><span dir="ltr"><code>Classless</code></span></td>
      <td align="right">البروتوكول العملاق المُستخدم لربط شبكات الإنترنت ومزودي الخدمة ببعضهم (بين الدول والشركات الكبرى).</td>
    </tr>
  </tbody>
</table>

<hr>

### 5. التوجيه الافتراضي (Default Static Route)
<ul>
  <li><strong>الفكرة:</strong> يُستخدم لتعريف الراوتر بمسار يرسل إليه أي بيانات متجهة لشبكة مجهولة لا يعرفها (يُستخدم بكثرة في راوتر البيت للوصول للإنترنت حيث توجد ملايين الشبكات).</li>
  <li><strong>الصيغة:</strong> نعبر عن "كل الشبكات" بـ <span dir="ltr"><code>0.0.0.0</code></span> وكل الماسكات بـ <span dir="ltr"><code>0.0.0.0</code></span>.</li>
  <li><strong>مثال للأمر:</strong><br>
  <span dir="ltr"><code>ip route 0.0.0.0 0.0.0.0 g0/1</code></span></li>
  <li>يظهر هذا المسار في جدول التوجيه بحرف <strong>*S</strong> (بحيث ترمز النجمة إلى أنه مسار افتراضي).</li>
</ul>

</div>