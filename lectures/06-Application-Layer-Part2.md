# 06 - Application Layer Protocols (Part 2)

### 1. بروتوكول الـ DHCP (Dynamic Host Configuration Protocol)

#### 1. الوظيفة والأهمية
* يوفر إعدادات الشبكة للأجهزة تلقائياً دون تدخل يدوي، لتفادي تكرار العناوين (`IP Conflict`) وتوفير الوقت.
* **الإعدادات التي يوزعها الـ DHCP:**
  * الـ `IP Address` الخاص بالجهاز.
  * الـ `Subnet Mask`.
  * الـ `Default Gateway` (الـ IP الخاص بمنفذ الـ Router).
  * عناوين الـ `DNS Servers`.
  * فترة السماح باستخدام العنوان (`Lease Time`).

#### 2. الـ Ports و بروتوكول الـ Transport
* يعتمد الـ DHCP بالكامل على بروتوكول **UDP**.
* يستخدم منفذين منفصلين:
  * **UDP Port 67:** مخصص للاستماع واستقبال الطلبات على الـ `DHCP Server`.
  * **UDP Port 68:** مخصص لاستقبال الردود على أجهزة الـ `DHCP Client`.

#### 3. مراحل الحصول على الـ IP (DORA Process)
تتم عملية التوزيع عبر أربع رسائل رئيسية بالترتيب:

1. **Discover (DHCPDISCOVER):**
   * يرسل الجهاز طلباً يبحث فيه عن أي `DHCP Server` متاح في الشبكة.
   * تخرج الرسالة كـ `Broadcast` (الـ Source IP `0.0.0.0` والـ Destination IP `255.255.255.255`، والـ Source MAC هو ماك الجهاز والـ Destination MAC `FF:FF:FF:FF:FF:FF`) لأن الجهاز لا يمتلك IP بعد.
2. **Offer (DHCPOFFER):**
   * يستجيب الـ `DHCP Server` بحجز `IP Address` غير مستخدم من الـ `Pool` المتاح وإرساله للجهاز في صورة عرض يتضمن الإعدادات والـ `Lease Time`.
3. **Request (DHCPREQUEST):**
   * يرسل الجهاز رسالة `Broadcast` تؤكد موافقته وقبوله لعرض سيرفر معين، وذلك لإعلام باقي السيرفرات في الشبكة بإلغاء عروضها.
4. **Acknowledge (DHCPACK):**
   * يرسل السيرفر المختار تأكيداً نهائياً للجهاز بأن العنوان تم حجزه له بنجاح، ليبدأ الجهاز استخدام الـ IP رسمياً على الشبكة.

---

### 2. منظومة أسماء النطاقات (DNS - Domain Name System)

#### 1. المفهوم والهدف الجوهري
* أجهزة الكمبيوتر والـ Routers تتفاهم وتوجه البيانات عبر الـ `IP Addresses` فقط، في حين يفضل البشر التعامل بالأسماء النصية للسهولة (مثل `google.com`).
* يعمل الـ DNS كدليل الهاتف للشبكة؛ وظيفته الأساسية تحويل الاسم المقروء أو الـ `FQDN` (Fully Qualified Domain Name) إلى `IP Address` رقمي، وهي عملية تسمى `Name Resolution`.

#### 2. الـ Ports و بروتوكولات الـ Transport
يعتمد الـ DNS على **Port 53** باستخدام نوعين من البروتوكولات:
* **UDP Port 53:** يُستخدم في الـ `DNS Queries / Lookups` اليومية العادية نظراً لحجمها الصغير وسرعة الاستجابة المطلوبة.
* **TCP Port 53:** يُستخدم في عمليات الـ `Zone Transfers` (نقل ومزامنة سجلات الـ DNS بالكامل بين الـ Servers) أو عندما يتجاوز حجم الرد 512 بايت لضمان الـ Reliability.

#### 3. البنية الهرمية لمنظومة الـ DNS (`Hierarchical Architecture`)
تتبع شجرة الـ DNS ترتيباً هرمياً يبدأ من الأعلى إلى الأسفل:
1. **Root Domain (`.`):** نقطة البداية، تديرها الـ `Root Hints Servers` (13 سيرفر مرجعي حول العالم).
2. **Top-Level Domains (`TLD`):** نطاقات المستوى الأعلى، مثل:
   * عامة (`gTLD`): `.com`، `.org`، `.net`.
   * جغرافية (`ccTLD`): `.eg`، `.sa`، `.uk`.
3. **Second-Level Domain:** اسم النطاق الخاص بالشركة (مثل: `cisco` أو `google`).
4. **Subdomain / Host:** الخادم الداخلي (مثل: `www` أو `mail`).

#### 4. آلية الـ DNS Query و الـ Resolution
عند كتابة `www.cisco.com` في الـ Browser:
1. يفحص الجهاز الـ `DNS Cache` المحلي وملف الـ `Hosts`.
2. إذا لم يعثر عليه، يرسل طلباً إلى الـ `Local DNS Server` (المعين في إعدادات الشبكة) وهو ما يُعرف بالـ `Recursive Query`.
3. إذا لم يجد الـ Local Server الإجابة، يبدأ بـ `Iterative Queries`:
   * يستعلم من الـ `Root Server` الذي يوجهه لـ `TLD Server` (الخاص بـ `.com`).
   * الـ `TLD Server` يوجهه إلى الـ `Authoritative DNS Server` (الخاص بـ Cisco).
4. يُرجع الـ Authoritative Server الـ IP الفعلي، فيقوم الـ Browser ببدء الاتصال.

#### 5. أوامر الـ DNS (DNS Commands)
* `ipconfig /displaydns`: لعرض محتوى الـ DNS Cache المحلي على نظام Windows.
* `nslookup`: أداة للاستعلام المباشر من الـ DNS Server عن IP لموقع معين (مثال: `nslookup www.google.com`).

---

### 3. بروتوكولات المزامنة والإدارة ومراقبة الشبكة

#### 1. بروتوكول مزامنة الوقت (NTP - Network Time Protocol)
* **الوظيفة:** مزامنة الساعة والتاريخ بدقة فائقة بين كافة أجهزة وسيرفرات الشبكة.
* **الـ Port و بروتوكول الـ Transport:** يعمل عبر **UDP Port 123**.
* **الأهمية:**
  * حيوي لسجلات الأحداث (`Syslog Timestamps`) لتتبع الأعطال والهجمات الأمنية بشكل زمني دقيق.
  * ضروري لصحة عمل الشهادات الرقمية والمصادقة.
* **الـ Stratum Value:**
  * مقياس لدقة الساعة ومدى بعدها عن المصدر المرجعي الأساسي للوقت (مثل الساعات الذرية).
  * `Stratum 0`: المصدر المرجعي عالي الدقة.
  * `Stratum 1`: أجهزة متصلة مباشرة بمصادر `Stratum 0`.
  * `Stratum 2`: أجهزة تأخذ الوقت من أجهزة `Stratum 1` عبر الشبكة.
* **NTP Modes:**
  * `Client/Server Mode`: الـ Client يطلب الوقت من الـ Server.
  * `Symmetric Active Mode`: أجهزة الـ NTP تتبادل الوقت فيما بينها (تُستخدم بين الـ Routers).
  * `Broadcast Mode`: الـ Server يرسل تحديثات الوقت للجميع (`Broadcast`) في الشبكات المحلية.

#### 2. بروتوكول إدارة الشبكة البسيط (SNMP - Simple Network Management Protocol)
* **الوظيفة:** مراقبة وإدارة أجهزة الشبكة مركزياً.
* **الـ Port و بروتوكول الـ Transport:**
  * **UDP Port 161:** مخصص لتبادل استعلامات الإدارة (`Polling / Get & Set Requests`).
  * **UDP Port 162:** مخصص لاستقبال التنبيهات الفورية من الأجهزة (`SNMP Traps`).
* **مكونات الـ SNMP:**
  * **NMS (Network Management Station):** السيرفر أو البرنامج المركزي الذي يقوم بمراقبة الشبكة.
  * **SNMP Agent:** البرنامج أو الخدمة التي تعمل على أجهزة الشبكة وتقوم بجمع البيانات.
  * **MIB (Management Information Base):** قاعدة البيانات داخل الجهاز التي تخزن المعلومات التي يمكن للـ NMS قراءتها أو تعديلها.
* **رسائل الـ SNMP:**
  * `Get Request`: طلب NMS لقراءة معلومة من الـ Agent.
  * `Set Request`: طلب NMS لتعديل معلومة على الـ Agent.
  * `Trap`: رسالة تنبيه غير مطلوبة يرسلها الـ Agent للـ NMS عند حدوث حدث هام (لا تتطلب تأكيد استلام).
  * `Inform`: رسالة تنبيه مشابهة للـ Trap، لكنها تتطلب تأكيد استلام (`Acknowledgment`) من الـ NMS (متوفرة في SNMPv2c و SNMPv3).
* **إصدارات البروتوكول:**
  * `SNMPv1` و `SNMPv2c`: تستخدمان كلمة مرور بنص صريح تسمى `Community String` (`Clear-Text`).
  * `SNMPv3`: الإصدار الآمن، يدعم التوثيق والتشفير (`Authentication & Encryption`).

#### 3. بروتوكول سجلات النظام (Syslog)
* **الوظيفة:** تجميع وتوثيق رسائل التنبيهات والأحداث البرمجية الصادرة من أجهزة الشبكة وإرسالها إلى سيرفر مركزي (`Syslog Server`).
* **الـ Port و بروتوكول الـ Transport:** يعمل عبر **UDP Port 514**.
* **الأهمية:** حفظ السجلات في مكان آمن خارج الجهاز حتى لا تُفقد عند الـ `Reboot`، للرجوع إليها في الـ `Troubleshooting`.

---

### 4. جدول خلاصة بروتوكولات الجزء الثاني

<div dir="rtl">

<table border="1" cellpadding="8" cellspacing="0" width="100%">
  <thead>
    <tr bgcolor="#161b22">
      <th align="center">البروتوكول</th>
      <th align="center">الـ Port</th>
      <th align="center">الـ Transport Protocol</th>
      <th align="center">الوظيفة الأساسية</th>
      <th align="center">ملاحظات</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="center"><strong>DHCP</strong></td>
      <td align="center"><code>67</code> (Server)<br><code>68</code> (Client)</td>
      <td align="center"><code>UDP</code></td>
      <td>توزيع عناوين IP وإعدادات الشبكة ديناميكياً</td>
      <td align="center">يعتمد على خطوات DORA الأربعة</td>
    </tr>
    <tr>
      <td align="center"><strong>DNS</strong></td>
      <td align="center"><code>53</code></td>
      <td align="center"><code>UDP</code> (Queries)<br><code>TCP</code> (Zone Transfers)</td>
      <td>تحويل الـ FQDN إلى عناوين IP</td>
      <td align="center">يعتمد على بنية هرمية (Root, TLD)</td>
    </tr>
    <tr>
      <td align="center"><strong>NTP</strong></td>
      <td align="center"><code>123</code></td>
      <td align="center"><code>UDP</code></td>
      <td>مزامنة الوقت والتاريخ بين أجهزة الشبكة</td>
      <td align="center">يعتمد على الـ Stratum Value لتحديد الدقة</td>
    </tr>
    <tr>
      <td align="center"><strong>SNMP</strong></td>
      <td align="center"><code>161</code> (Get/Set)<br><code>162</code> (Traps)</td>
      <td align="center"><code>UDP</code></td>
      <td>مراقبة وإدارة أجهزة الشبكة مركزياً عبر NMS</td>
      <td align="center">الإصدار v3 يدعم التشفير (Encrypted)</td>
    </tr>
    <tr>
      <td align="center"><strong>Syslog</strong></td>
      <td align="center"><code>514</code></td>
      <td align="center"><code>UDP</code></td>
      <td>تجميع وحفظ سجلات الأجهزة في سيرفر مركزي</td>
      <td align="center">أساسي لعمليات الـ Troubleshooting</td>
    </tr>
  </tbody>
</table>

</div>