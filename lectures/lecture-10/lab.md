# 10 - Packet Tracer Lab 2: Static Routing & Simulation

### 1. مراجعة إعدادات الأجهزة (Initial Configuration)
لتجهيز اللاب، نطبق نفس خطوات المحاضرة السابقة:
1. توزيع الآيبيهات والـ Gateway لأجهزة الكمبيوتر.
2. الدخول لرواتر الفروع وتسميتها (<span dir="ltr"><code>hostname</code></span>).
3. إعطاء الآيبيهات للـ Interfaces وتشغيلها بأمر <span dir="ltr"><code>no shutdown</code></span>. (الكروت المتجهة لـ LAN تأخذ ماسك `255.255.255.0` والكروت المتجهة للراوتر الآخر تأخذ ماسك `255.255.255.252`).

---

### 2. فحص جدول التوجيه (Viewing the Routing Table)
للتأكد من أن الراوتر يرى شبكاته المتصلة بشكل سليم، نستخدم أهم أمر فحص في سيسكو:

<div dir="rtl">
<table border="1" cellpadding="8" cellspacing="0" width="100%">
  <thead>
    <tr bgcolor="#161b22">
      <th align="center">الأمر</th>
      <th align="center">المستوى</th>
      <th align="center">الشرح</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="center"><span dir="ltr"><code>show ip route</code></span></td>
      <td align="center"><code>#</code></td>
      <td>يعرض جدول التوجيه. سنلاحظ وجود حرف <code>C</code> بجوار الشبكات الموصلة مباشرة بالراوتر.</td>
    </tr>
  </tbody>
</table>
</div>

---

### 3. إعداد الـ Static Routing
لجعل الأجهزة في الفرع الأول تتواصل مع الفرع الثاني، نستخدم أمر <span dir="ltr"><code>ip route</code></span> لتعريف الراوتر بالشبكات البعيدة.

**صيغة الأمر:**
<span dir="ltr"><code>ip route [Destination_Network] [Subnet_Mask] [Exit_Interface]</code></span>

<div dir="rtl">
<table border="1" cellpadding="8" cellspacing="0" width="100%">
  <thead>
    <tr bgcolor="#161b22">
      <th align="center">الراوتر</th>
      <th align="center">الأمر (يُكتب في مستوى <code>config</code>)</th>
      <th align="center">الشرح</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="center"><strong>الراوتر الأول (HQ)</strong></td>
      <td align="center"><span dir="ltr"><code>ip route 192.168.2.0 255.255.255.0 g0/1</code></span></td>
      <td>للوصول لشبكة الفرع الثاني <code>192.168.2.0</code>، أخرج البيانات من منفذ <code>g0/1</code>.</td>
    </tr>
    <tr>
      <td align="center"><strong>الراوتر الثاني (Branch)</strong></td>
      <td align="center"><span dir="ltr"><code>ip route 192.168.1.0 255.255.255.0 g0/1</code></span></td>
      <td>طريق العودة: للوصول لشبكة الفرع الأول <code>192.168.1.0</code>، أخرج البيانات من منفذ <code>g0/1</code>.</td>
    </tr>
  </tbody>
</table>
</div>

بعد تنفيذ الأوامر، إذا كتبنا <span dir="ltr"><code>show ip route</code></span> سنرى الشبكات الجديدة بجوارها حرف <span dir="ltr"><code>S</code></span>.

---

### 4. وضع المحاكاة في Packet Tracer (Simulation Mode)
عند عمل `ping` واستكشاف الأخطاء، يوفر البرنامج وضعاً احترافياً يُدعى المحاكاة.

- **طريقة التفعيل:** نضغط على أيقونة الساعة (Simulation) أسفل يمين الشاشة بجوار كلمة Realtime.
- **الفائدة:** يوقف الزمن ويسمح لك بتتبع مسار رسالة الـ Ping (حزمة ICMP) خطوة بخطوة.
- **التتبع (Troubleshooting):** إذا نسيت عمل `Static Route` في طريق العودة مثلاً، ستشاهد الرسالة تصل بنجاح للفرع الآخر، لكن عند محاولة الرجوع ستجد رسالة الرد أخذت "علامة X حمراء" وتم إتلافها عند الراوتر الثاني، مما يسهل عليك اكتشاف الخطأ.