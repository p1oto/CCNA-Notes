# 13 - Packet Tracer Lab: GRE Tunnel VPN & Standard ACL

<div dir="rtl" align="right">

## أولاً: إعدادات نفق الـ VPN (GRE Tunnel)
نفترض أننا على الراوتر الأول (الفرع الرئيسي) ونريد ربطه بالراوتر الثاني (الفرع الفرعي) عبر الإنترنت.

**1. إنشاء واجهة النفق الوهمية والدخول لإعداداتها:**
</div>
<div dir="ltr" align="left">

```text
Router(config)# interface tunnel 0
```
</div>
<div dir="rtl" align="right">

**2. إعطاء آي بي للنفق (يجب أن يكون من نفس شبكة النفق في الراوتر المقابل):**
</div>
<div dir="ltr" align="left">

```text
Router(config-if)# ip address 10.0.0.1 255.255.255.0
```
</div>
<div dir="rtl" align="right">

**3. تحديد كارت الخروج أو الآي بي العام (Public IP) كبداية للنفق (Source):**
</div>
<div dir="ltr" align="left">

```text
Router(config-if)# tunnel source 11.0.0.1
```
</div>
<div dir="rtl" align="right">

**4. تحديد الآي بي العام (Public IP) للراوتر الآخر كنهاية للنفق (Destination):**
</div>
<div dir="ltr" align="left">

```text
Router(config-if)# tunnel destination 12.0.0.1
```
</div>
<div dir="rtl" align="right">
*(ملاحظة: يتم تكرار نفس الأوامر على الراوتر الثاني مع عكس الـ Source والـ Destination، وإعطائه آي بي مختلف في النفق مثل `10.0.0.2`)*

---

## ثانياً: توجيه البيانات داخل النفق (Routing via Tunnel)
بعد بناء النفق، يجب إخبار الراوتر بأن يرسل بيانات الشبكة الداخلية الخاصة بالفرع الآخر عبر هذا النفق (باستخدام التوجيه اليدوي كالتالي، أو بروتوكول توجيه مثل OSPF).

**1. توجيه الترافيك الذاهب لشبكة الفرع الثاني ليمر من النفق:**
</div>
<div dir="ltr" align="left">

```text
Router(config)# ip route 192.168.2.0 255.255.255.0 tunnel 0
```
</div>
<div dir="rtl" align="right">

---

## ثالثاً: إعداد الـ Standard ACL (قائمة التحكم)
مثال: نريد منع الجهاز `192.168.1.10` من الوصول للشبكة، والسماح لباقي الأجهزة بالمرور.

**1. إنشاء قائمة تحكم برقم (من 1 إلى 99) ومنع الجهاز المحدد:**
</div>
<div dir="ltr" align="left">

```text
Router(config)# access-list 1 deny host 192.168.1.10
```
</div>
<div dir="rtl" align="right">

**2. السماح لأي جهاز آخر بالمرور (لتفادي قاعدة المنع الضمني Implicit Deny):**
</div>
<div dir="ltr" align="left">

```text
Router(config)# access-list 1 permit any
```
</div>
<div dir="rtl" align="right">

---

## رابعاً: تطبيق الـ ACL على الكارت (Interface Application)
الـ ACL لن تعمل بمجرد كتابتها؛ يجب تطبيقها على الواجهة المناسبة (يفضل أقرب نقطة للهدف في حالة الـ Standard ACL).

**1. الدخول على الواجهة المواجهة للشبكة الداخلية (مثلاً G0/1):**
</div>
<div dir="ltr" align="left">

```text
Router(config)# interface gigabitethernet 0/1
```
</div>
<div dir="rtl" align="right">

**2. تطبيق القائمة رقم 1 على الترافيك الخارج (Out) أو الداخل (In) بناءً على مسار الداتا:**
</div>
<div dir="ltr" align="left">

```text
Router(config-if)# ip access-group 1 out
```
</div>
