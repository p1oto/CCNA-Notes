# 13 - Packet Tracer Lab: GRE Tunnel VPN & Standard ACL

<div dir="rtl" align="right">

### 1. إعداد نفق الـ VPN (GRE Tunnel)
لربط فرعين عبر الإنترنت باستخدام نفق (Tunnel)، نقوم بإنشاء واجهة وهمية للنفق وتحديد مسار البداية والنهاية باستخدام الآيبيهات العامة (Public IPs).

<ul>
  <li><strong>الأوامر (تُنفذ على الراوتر الأول R1):</strong></li>
</ul>

</div>

<div dir="ltr" align="left">

```text
! إنشاء واجهة النفق الوهمية والدخول لإعداداتها
Router(config)# interface tunnel 0

! إعطاء آي بي للنفق (يجب أن يكون من نفس شبكة النفق في الراوتر المقابل)
Router(config-if)# ip address 10.0.0.1 255.255.255.0

! تحديد كارت الخروج أو الآي بي العام (Public IP) الخاص بهذا الراوتر (بداية النفق)
Router(config-if)# tunnel source 11.0.0.1

! تحديد الآي بي العام (Public IP) للراوتر الآخر (نهاية النفق)
Router(config-if)# tunnel destination 12.0.0.1
```

</div>

<div dir="rtl" align="right">
<em>(ملاحظة: يتم تكرار نفس الأوامر على الراوتر الثاني مع عكس الـ Source والـ Destination، وإعطائه آي بي 10.0.0.2).</em>

<hr>

### 2. توجيه البيانات داخل النفق (Routing via Tunnel)
بعد بناء النفق، يجب إخبار الراوتر بأن يرسل بيانات الشبكة الداخلية الخاصة بالفرع الآخر عبر هذا النفق (باستخدام التوجيه اليدوي أو بروتوكول توجيه مثل OSPF).

<ul>
  <li><strong>طريقة التوجيه اليدوي (Static Route):</strong></li>
</ul>

</div>

<div dir="ltr" align="left">

```text
! توجيه الترافيك الذاهب لشبكة الفرع الثاني ليمر من النفق (tunnel 0)
Router(config)# ip route 192.168.2.0 255.255.255.0 tunnel 0
```

</div>

<div dir="rtl" align="right">

<hr>

### 3. إعداد الـ Standard ACL (قائمة التحكم)
سنقوم بإنشاء قائمة تحكم بالوصول لمنع جهاز معين (مثلاً 192.168.1.10) من الوصول للشبكة، والسماح لباقي الأجهزة. 

<ul>
  <li><strong>أوامر إنشاء القائمة:</strong></li>
</ul>

</div>

<div dir="ltr" align="left">

```text
! إنشاء قائمة تحكم برقم 1 (من 1 إلى 99 تعني Standard) ومنع جهاز محدد
Router(config)# access-list 1 deny host 192.168.1.10

! السماح لأي جهاز آخر بالمرور (لتفادي المنع الضمني Implicit Deny)
Router(config)# access-list 1 permit any
```

</div>

<div dir="rtl" align="right">

<hr>

### 4. تطبيق الـ ACL على الكارت (Interface Application)
الـ ACL لن تعمل بمجرد كتابتها؛ يجب تطبيقها على الواجهة المناسبة (يفضل أقرب نقطة للهدف Destination في الـ Standard ACL) وتحديد اتجاه التفتيش (In أو Out).

<ul>
  <li><strong>أوامر التطبيق:</strong></li>
</ul>

</div>

<div dir="ltr" align="left">

```text
! الدخول على الواجهة المواجهة للشبكة الداخلية (مثلاً G0/1)
Router(config)# interface g0/1

! تطبيق القائمة رقم 1 على الترافيك الخارج (Out) من الراوتر للشبكة
Router(config-if)# ip access-group 1 out
```

</div>

<div dir="rtl" align="right">
<em>(ملاحظة: إذا نسيت إضافة سطر <code>permit any</code>، سيتم منع جميع الأجهزة بسبب وجود <code>deny any</code> مخفي في نهاية أي قائمة).</em>

</div>