# 12 - Packet Tracer Lab: Configuring OSPFv2

<div dir="rtl" align="right">

### 1. إعداد الـ Loopback Interface (تزوير الـ Router ID)
لضمان فوز راوتر معين بالرئاسة (DR)، نُنشئ واجهة وهمية ونعطيها IP عالٍ جداً وقناع شبكة لجهاز واحد (Host Mask) حتى لا نهدر الآيبيهات.

<ul>
  <li><strong>الأوامر (تُكتب في وضع الإعدادات):</strong></li>
</ul>

<div dir="ltr" align="left">

```text
Router(config)# interface loopback 0
Router(config-if)# ip address 22.22.22.22 255.255.255.255
```

</div>

<em>(ملاحظة: الواجهة الوهمية تعمل فوراً بمجرد إنشائها ولا تحتاج لأمر <code>no shutdown</code>)</em>

<hr>

### 2. تفعيل بروتوكول OSPF (الطريقة العادية)
نشغل الـ OSPF باستخدام رقم العملية (Process ID)، ونعلن عن الشبكات المتصلة باستخدام الـ Wildcard Mask (معكوس الـ Subnet Mask) مع تحديد الـ Area.

<ul>
  <li><strong>الأوامر:</strong></li>
</ul>

<div dir="ltr" align="left">

```text
Router(config)# router ospf 1
Router(config-router)# network 192.168.1.0 0.0.0.255 area 0
Router(config-router)# network 2.2.2.0 0.0.0.3 area 0
```

</div>

<hr>

### 3. تفعيل بروتوكول OSPF (الطريقة الشاملة الأسرع)
بدلاً من كتابة أمر لكل شبكة أو حساب الـ Wildcard Mask لكل واجهة، يمكننا تشغيل الـ OSPF على <strong>جميع واجهات الراوتر الحالية</strong> بضغطة واحدة.

<ul>
  <li><strong>الأمر:</strong></li>
</ul>

<div dir="ltr" align="left">

```text
Router(config)# router ospf 1
Router(config-router)# network 0.0.0.0 255.255.255.255 area 0
```

</div>

<em>(هذا الأمر معناه: قم بتفعيل الـ OSPF في Area 0 على أي IP وأي كارت متصل بهذا الراوتر).</em>

<hr>

### 4. الفحص والتحقق (Verification)
للتأكد من أن الـ OSPF يعمل بشكل صحيح وقام ببناء جدول التوجيه:

<ul>
  <li><strong>أمر الفحص:</strong> <span dir="ltr"><code>show ip route</code></span></li>
  <li><strong>ما الذي سنراه في الجدول؟</strong>
    <ul>
      <li>ستظهر المسارات البعيدة متبوعة بحرف <strong>O</strong> (دلالة على أن الراوتر تعلمها عبر بروتوكول OSPF).</li>
      <li>ستلاحظ أن رقم الـ Admin Distance المكتوب بين القوسين بجوار الـ OSPF هو <strong>110</strong>.</li>
    </ul>
  </li>
  <li><strong>اختبار الاتصال:</strong> جرب عمل <span dir="ltr"><code>ping</code></span> من جهاز في الفرع الأول لجهاز في الفرع البعيد، وسترى الاستجابة بنجاح تام.</li>
</ul>

</div>