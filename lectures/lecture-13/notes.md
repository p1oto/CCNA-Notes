# 13 - Site-to-Site VPN & ACL (Access Control List) Concepts

<div dir="rtl" align="right">

### 1. الـ VPN (Virtual Private Network)
بدلاً من شراء خطوط ربط خاصة (Leased Lines) مكلفة جداً (قد تصل لآلاف الجنيهات شهرياً) لربط فروع الشركة، نستخدم الـ VPN لربط الفروع عبر شبكة عامة (الإنترنت) بتكلفة رخيصة جداً (خطوط DSL العادية).
<ul>
  <li><strong>فكرة العمل (Tunneling):</strong> يتم بناء "نفق وهمي" (Tunnel) يمر عبر الإنترنت. الراوتر يعامل هذا النفق كأنه كابل مباشر بين الفرعين (Site-to-Site).</li>
  <li><strong>كيف يخدع الراوتر شبكة الإنترنت؟ (Encapsulation):</strong> الداتا الأصلية (التي تحتوي على Private IPs) يتم تغليفها بهيدر جديد يُسمى <strong>GRE (Generic Routing Encapsulation)</strong>، ثم تُغلف بهيدر IP جديد يحتوي على الـ Public IPs الخاصة بالراوترات. راوترات الإنترنت لا ترى سوى الـ Public IPs وتوصل البيانات للفرع الآخر.</li>
  <li><strong>بروتوكول GRE:</strong> يضيف هيدر بحجم 24 بايت (20 بايت تشبه الـ IP و 4 بايت إضافية)، وعيبه القاتل أن البيانات تمر بداخله <em>Clear Text</em> (نص صريح غير مشفر)، مما يعرضها للاختراق بسهولة من أي هاكر.</li>
</ul>

<hr>

### 2. بروتوكول IPsec وتشفير البيانات
لحل مشكلة الـ Clear Text في الـ GRE، نستخدم بروتوكول <strong>IPsec (IP Security)</strong> لحماية النفق (Tunnel Protection).
يقدم الـ IPsec ميزتين أساسيتين:
<ol>
  <li><strong>التوثيق (Authentication):</strong> التأكد من هوية الراوتر الآخر عبر كلمة سر موحدة (Pre-shared Key) لضمان عدم وجود راوتر غريب في النص.</li>
  <li><strong>التشفير (Confidentiality/Encryption):</strong> يتم تشفير البيانات بخوارزميات قوية. تطور هذه الخوارزميات كالتالي:
    <ul>
      <li><strong>DES:</strong> خوارزمية ضعيفة، تم كسر شفرتها (من قِبل المافيا الروسية).</li>
      <li><strong>3DES:</strong> أقوى 3 مرات من سابقتها، ولكن تم كسرها أيضاً (المافيا الإيطالية).</li>
      <li><strong>AES (Advanced Encryption Standard):</strong> الأقوى على الإطلاق ولم تُكسر حتى الآن، وتُستخدم بكثرة في الـ VPN والـ Wi-Fi.</li>
    </ul>
  </li>
</ol>

<hr>

### 3. قوائم التحكم بالوصول (Access Control Lists - ACL)
هي أداة (تُشبه البودي جارد) توضع على منافذ الراوتر أو الفايرول للتحكم في البيانات المارة: إما السماح (<strong>Permit</strong>) أو المنع (<strong>Deny</strong>).

<strong>قواعد هامة في الـ ACL:</strong>
<ul>
  <li><strong>الترتيب يهم:</strong> الراوتر يقرأ السطور من أعلى لأسفل. يجب كتابة السطور المحددة جداً (أشخاص معينين) في الأعلى، والسطور العامة (الشبكة كلها) في الأسفل.</li>
  <li><strong>المنع الضمني (Implicit Deny):</strong> في نهاية أي ليسته، يوجد سطر مخفي يمنع أي شخص لم يُذكر اسمه (Deny Any). لذا يجب دائماً وضع سطر Permit في الليست وإلا ستقع الشبكة بالكامل ولن يمر أي ترافيك.</li>
  <li><strong>اتجاه التفتيش (In أو Out):</strong> يجب تحديد هل التفتيش يتم والداتا "داخلة" للراوتر (In) أم وهي "خارجة" منه (Out) بناءً على اتجاه مرور الترافيك في الرسمة.</li>
</ul>

<strong>أنواع الـ ACL المقررة في الـ CCNA:</strong>
<ol>
  <li><strong>Standard ACL:</strong> وتُسمى "القفوشة"، لأنها تمنع أو تسمح بناءً على <strong>السورس آي بي (Source IP) فقط</strong>، ولا تهتم بنوع الخدمة أو الوجهة. <em>(أرقامها في الطريقة القديمة: 1-99 و 1300-1999)</em>.</li>
  <li><strong>Extended ACL:</strong> أكثر مرونة وذكاءً، تتحكم بناءً على السورس، والوجهة (Destination)، وبروتوكول النقل (TCP/UDP)، ونوع التطبيق (HTTP, FTP, etc). <em>(أرقامها: 100-199 و 2000-2699)</em>.</li>
</ol>

<hr>

### 4. الوايلد كارد ماسك (Wildcard Mask) في الـ ACL
يُستخدم لتحديد نطاق الآيبيهات (Range) المسموح أو الممنوع.
<ul>
  <li>الرقم <strong>0</strong> يعني Exact Match (تطابق تام).</li>
  <li>أي رقم آخر (مثل 255) يعني Don't Care (لا يهم).</li>
  <li><strong>كيف تحسب عدد الأجهزة الممنوعة/المسموحة؟ (اجمع 1 على آخر رقم):</strong>
    <ul>
      <li><span dir="ltr"><code>0.0.0.0</code></span> = يطابق جهاز واحد فقط (1 IP).</li>
      <li><span dir="ltr"><code>0.0.0.3</code></span> = يطابق 4 أجهزة.</li>
      <li><span dir="ltr"><code>0.0.0.15</code></span> = يطابق 16 جهاز.</li>
      <li><span dir="ltr"><code>0.0.0.63</code></span> = يطابق 64 جهاز.</li>
      <li><span dir="ltr"><code>0.0.0.255</code></span> = يطابق 256 جهاز (شبكة كاملة).</li>
    </ul>
  </li>
</ul>

<hr>

### 5. إعداد وتوزيع الآيبيهات تلقائياً (DHCP)
يمكن تحويل الراوتر لـ DHCP Server لتوزيع الآيبيهات على الأجهزة في الشبكة تلقائياً بدلاً من إدخالها يدوياً (Static).

<strong>أهم أوامر الـ DHCP:</strong>
</div>

<div dir="ltr" align="left">

```text
! حجز مجموعة آيبيهات (استثناء) لعدم توزيعها على الأجهزة (مثل آيبيهات الراوتر أو السيرفرات)
Router(config)# ip dhcp excluded-address 192.168.1.1 192.168.1.3

! إنشاء وعاء (Pool) لتوزيع الآيبيهات وتسميته (مثال: fady)
Router(config)# ip dhcp pool fady

! تحديد الشبكة التي سيتم التوزيع منها مع الـ Subnet Mask
Router(dhcp-config)# network 192.168.1.0 255.255.255.0

! تحديد الـ Default Gateway (الراوتر) الذي ستأخذه الأجهزة
Router(dhcp-config)# default-router 192.168.1.1

! تحديد سيرفر الـ DNS (اختياري)
Router(dhcp-config)# dns-server 8.8.8.8
```

</div>

<div dir="rtl" align="right">
<em>