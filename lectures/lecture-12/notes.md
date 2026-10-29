# 12 - OSPF (Open Shortest Path First) Concepts

<div dir="rtl" align="right">

### 1. مقدمة عن بروتوكولات الـ Link-State
<ul>
  <li>بروتوكولات الـ <strong>Link-State</strong> (مثل <strong>OSPF</strong> و <strong>IS-IS</strong>) تعتمد على رسم خريطة كاملة للشبكة (Topology) في الرامات بالذكاء الاصطناعي قبل استخراج جدول التوجيه.</li>
  <li><strong>بروتوكول IS-IS:</strong> هو الأقدم، ولكنه ظهر قبل ابتكار الـ Subnetting بشكل كامل، ورغم احتوائه على عيوب إلا أنه يُستخدم بكثرة في شبكات مزودي الخدمة (ISPs) الكبرى.</li>
  <li><strong>بروتوكول OSPFv2:</strong> هو البروتوكول الأشهر (يُشغل 99% من الشركات في العالم). كلمة Open تعني أنه <em>Open Standard</em> (مفتوح المصدر ويعمل على جميع أجهزة الرواتر مثل Cisco و Huawei) وليس معناها "فتح الطريق" كما يترجمها البعض خطأً. أما SPF فتعني خوارزمية حساب أقصر مسار.</li>
</ul>

<hr>

### 2. كيف يعمل OSPF؟ (خطوات بناء جدول التوجيه)
يمر الـ OSPF بعدة مراحل دقيقة لبناء الجدول:
<ol>
  <li><strong>رسائل الترحيب (Hello Packets):</strong> يرسل الراوتر رسالة Hello على عنوان <span dir="ltr"><code>224.0.0.5</code></span> لاكتشاف الجيران، وينتظر 40 ثانية (4 رسائل) ليتأكد إذا كان هناك راوتر رئيسي موجود أم لا.</li>
  <li><strong>انتخاب الريس (DR Election):</strong>
    <ul>
      <li>الـ <strong>DR (Designated Router):</strong> هو الراوتر الرئيس. الراوتر الأقدم (أول من يعمل) يصبح الرئيس.</li>
      <li>إذا عملت الرواتر في نفس الوقت، يتم الانتخاب بناءً على الأعلى في الـ <strong>Priority</strong> (من 0 لـ 255).</li>
      <li>إذا تساوت الأولوية، يفوز الراوتر صاحب أعلى <strong>Router ID</strong> (رقم البطاقة).</li>
    </ul>
  </li>
  <li><strong>تبادل الـ LSA (Link-State Advertisements):</strong> كل راوتر يرسل قصة حياته (LSA) والتي تحتوي على حالة الكروت، الآيبيهات، وسرعة الانترفيس (بالجيجا أو الميجا) على عنوان <span dir="ltr"><code>224.0.0.6</code></span>.</li>
  <li><strong>بناء الـ LSDB (Link-State Database):</strong> تجمع الرواتر هذه القصص في قاعدة بيانات موحدة (مسودة)، وتكون متطابقة تماماً في جميع الرواتر.</li>
  <li><strong>خوارزمية Dijkstra (SPF Algorithm):</strong> يحلل هذا البرنامج الـ LSDB ويرسم شكل الشبكة. يأخذ تقريباً <strong>5 ثوانٍ لكل 20 راوتر</strong> لاستخراج جدول التوجيه النهائي (بينما EIGRP يأخذ ثانيتين فقط).</li>
</ol>

<hr>

### 3. رقم البطاقة (Router ID) والتزوير القانوني
هو رقم يعرّف الراوتر في الانتخابات، ويتم تحديده كالتالي:
<ul>
  <li>الراوتر يختار تلقائياً أعلى IP حقيقي موجود على كروته ليكون هو الـ Router ID الخاص به.</li>
  <li><strong>التزوير بـ Loopback Interface:</strong> لضمان فوز راوتر معين، نُنشئ له كارت وهمي (Loopback) ونعطيه IP عالي جداً (مثل <span dir="ltr"><code>22.22.22.22</code></span>) وقناع شبكة لجهاز واحد (Host Mask) <span dir="ltr"><code>255.255.255.255</code></span> حتى لا نهدر الآيبيهات. هذا الكارت يعمل فوراً ويفوز الراوتر بالرئاسة.</li>
</ul>

<hr>

### 4. مفاهيم حيوية في الـ OSPF
<ul>
  <li><strong>الـ Process ID:</strong> رقم (من 1 إلى 65535). يُستخدم لتقسيم الراوتر الحقيقي الواحد إلى رواتر افتراضية (Virtual Routers) لخدمة عملاء مختلفين. في الـ CCNA نستخدم الرقم 1.</li>
  <li><strong>الـ Wildcard Mask:</strong> هو معكوس الـ Subnet Mask. لتسهيل تفعيل الـ OSPF على جميع كروت الراوتر دفعة واحدة، نستخدم الأمر <span dir="ltr"><code>network 0.0.0.0 255.255.255.255</code></span>.</li>
  <li><strong>الـ Area 0 (Backbone Area):</strong> إذا زاد عدد الرواتر في الـ OSPF عن 100، يبدأ الراوتر في التهنيج. لذا نلجأ لتقسيم الشبكة لمناطق (Areas). إذا كان عددنا أقل من 100، نضع الجميع في المنطقة الصفرية <strong>Area 0</strong>.</li>
</ul>

<hr>

### 5. المسافة الإدارية (Administrative Distance - AD)
إذا تم تشغيل أكثر من بروتوكول معاً، يختار الراوتر البروتوكول صاحب الـ AD <strong>الأقل</strong> (الأفضلية).
<ul>
  <li>الشبكات المتصلة مباشرة (Connected): <strong>0</strong></li>
  <li>التوجيه اليدوي (Static): <strong>1</strong></li>
  <li>بروتوكول BGP: <strong>20</strong></li>
  <li>بروتوكول EIGRP: <strong>90</strong></li>
  <li>بروتوكول OSPF: <strong>110</strong></li>
  <li>بروتوكول IS-IS: <strong>115</strong></li>
  <li>بروتوكول RIP: <strong>120</strong> (وهو الأسوأ بينهم)</li>
</ul>

</div>