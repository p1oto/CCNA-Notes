# 09 - Packet Tracer Lab 1: Initial Router Configuration

### 1. تخطيط الشبكة (Topology Design)
في هذا اللاب، قمنا ببناء شبكة مكونة من فرعين (Headquarters و Branch)، وكل فرع يمتلك شبكة محلية (LAN) موصلة براوتر، ويرتبط الراوتران معاً بخط (WAN).

- **الأجهزة المستخدمة:** <span dir="ltr"><code>2 Routers</code></span> (موديل 2911)، <span dir="ltr"><code>2 Switches</code></span> (موديل 2960)، <span dir="ltr"><code>2 PCs</code></span>.
- **الآيبيهات المستخدمة:**
  * **الفرع الأول (LAN 1):** شبكة <span dir="ltr"><code>192.168.1.0 /24</code></span>.
  * **الفرع الثاني (LAN 2):** شبكة <span dir="ltr"><code>192.168.2.0 /24</code></span>.
  * **نقطة الربط (WAN):** شبكة <span dir="ltr"><code>2.2.2.0 /30</code></span> (استخدمنا ماسك 30 لأنه يوفر 4 آيبيهات فقط لتجنب الإهدار).

![Topology](images/lab1_topology.png)

### 2. إعدادات الكمبيوتر (PC Configuration)
ندخل على جهاز الكمبيوتر في البرنامج، ثم <span dir="ltr"><code>Desktop -> IP Configuration</code></span>:
- نكتب الـ IP بناءً على الفرع (مثال للفرع الأول: <span dir="ltr"><code>192.168.1.2</code></span>).
- الـ Mask يُكتب بالنظام القديم: <span dir="ltr"><code>255.255.255.0</code></span>.
- الـ <span dir="ltr"><code>Default Gateway</code></span>: هو عنوان واجهة الراوتر الداخلي (مثال: <span dir="ltr"><code>192.168.1.1</code></span>).

### 3. إعدادات الرواتر (Router CLI Configuration)
ندخل إلى واجهة سطر الأوامر للراوتر الأول ونطبق الآتي:

<div dir="rtl">
<table border="1" cellpadding="8" cellspacing="0" width="100%">
  <thead>
    <tr bgcolor="#161b22">
      <th align="center">الخطوة / الأمر</th>
      <th align="center">الشرح</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><span dir="ltr"><code>enable</code></span><br><span dir="ltr"><code>configure terminal</code></span></td>
      <td>الدخول لوضع الإعدادات الشاملة (ليفل 3).</td>
    </tr>
    <tr>
      <td><span dir="ltr"><code>hostname HQ</code></span></td>
      <td>تغيير اسم الراوتر ليدل على الفرع.</td>
    </tr>
    <tr>
      <td><span dir="ltr"><code>enable secret cisco</code></span></td>
      <td>حماية الراوتر بكلمة سر مُشفرة.</td>
    </tr>
    <tr>
      <td><span dir="ltr"><code>interface g0/0</code></span><br><span dir="ltr"><code>ip address 192.168.1.1 255.255.255.0</code></span><br><span dir="ltr"><code>no shutdown</code></span></td>
      <td>الدخول لكارت الـ LAN وإعطاؤه IP وتشغيله.</td>
    </tr>
    <tr>
      <td><span dir="ltr"><code>exit</code></span></td>
      <td>الخروج من الكارت للدخول في الكارت الآخر.</td>
    </tr>
    <tr>
      <td><span dir="ltr"><code>interface g0/1</code></span><br><span dir="ltr"><code>ip address 2.2.2.1 255.255.255.252</code></span><br><span dir="ltr"><code>no shutdown</code></span></td>
      <td>برمجة كارت الـ WAN (لن يضيء بالأخضر إلا بعد تفعيل الكارت المقابل في الراوتر الآخر).</td>
    </tr>
    <tr>
      <td><span dir="ltr"><code>exit</code></span><br><span dir="ltr"><code>write</code></span></td>
      <td>لحفظ جميع الإعدادات حتى لا تضيع عند إعادة التشغيل.</td>
    </tr>
  </tbody>
</table>
</div>

### 4. اختبار الاتصال (Troubleshooting via Ping)
بعد تنفيذ نفس الخطوات على الفرع الآخر بآيبيهاته المختلفة، نفتح الـ <span dir="ltr"><code>Command Prompt</code></span> لعمل <span dir="ltr"><code>ping</code></span>:
- <span dir="ltr"><code>ping 192.168.1.255</code></span>: بنج للبرودكاست للتأكد من أن جميع الأجهزة المتصلة بالسويتش حية.
- <span dir="ltr"><code>ping 192.168.2.2</code></span>: بنج لجهاز في الفرع الآخر **سيفشل** لأن الراوتر لا يعرف مسار الفرع الآخر بعد (يُحل بالـ Routing لاحقاً).