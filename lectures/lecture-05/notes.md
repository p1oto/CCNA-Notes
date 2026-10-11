# 05 - Application Layer Protocols (Part 1)

### 1. مفهوم الـ Port Numbers و الـ Multiplexing
* **المشكلة:** قد يستقبل جهاز الكمبيوتر في نفس اللحظة حركة مرور تخص تطبيقات متعددة (تصفح ويب، تحميل ملف، جلسة إدارة عن بعد، وتشغيل فيديو). لا يكفي عنوان الـ `IP Address` وحده لتحديد أي تطبيق يجب أن يستلم هذه البيانات داخل نظام التشغيل.
* **الحل الفني (Port Numbers):**
  * يتم تمييز كل خدمة أو تطبيق شبكي برقم محدد يسمى **Port Number**.
  * يضاف الـ Port Number في الـ `Transport Layer Header` سواء عبر بروتوكول `TCP` أو `UDP`.
  * حجم حقل الـ Port Number: **16 Bits**، مما يتيح مدى يتراوح بين **0 إلى 65535**.
* **مفهوم الـ Socket:**
  * يتكون الـ Socket من دمج الـ IP Address مع الـ Port Number: `IP:Port` (مثال: `192.168.1.10:80`).
  * يضمن الـ Socket توجيه حزم البيانات القادمة إلى جلسة التطبيق المحددة بدقة داخل نظام التشغيل.
* **علاقة الـ Ports بالمرسل والمستقبل:**
  * **Destination Port:** يكون دائماً Port الخدمة المعروف والمفتوح على الـ Server (مثل Port 80 لتصفح الويب أو 23 للـ Telnet).
  * **Source Port:** رقم عشوائي ديناميكي يختاره جهاز الـ Client من النطاق الديناميكي المتاح، ويستخدمه الـ Server عند الرد ليعرف الجهاز أي علامة تبويب أو برنامج طلب هذه البيانات.

---

### 2. تصنيفات وتقسيمات الـ Port Numbers (IANA Ranges)
قامت منظمة `IANA` بتقسيم الـ Port Numbers البالغ عددها 65536 إلى ثلاث فئات رئيسية:

1. **Well-Known Ports:**
   * المدى: من **0 إلى 1023**.
   * مخصصة للخدمات والبروتوكولات المعيارية والأساسية في الشبكات والإنترنت (مثل HTTP, HTTPS, DNS, DHCP, Telnet, SSH).
2. **Registered Ports:**
   * المدى: من **1024 إلى 49151**.
   * مخصصة لشركات البرمجيات والتطبيقات الخاصة التي تسجل البورتات رسمياً لدى IANA (مثل قواعد بيانات Oracle أو Microsoft SQL أو برامج المحادثات).
3. **Dynamic / Private / Ephemeral Ports:**
   * المدى: من **49152 إلى 65535**.
   * تستخدمها أجهزة الـ Clients كـ `Source Ports` عشوائية ومؤقتة، يتم فتحها مع بدء الجلسة وإغلاقها فور اكتمال النقل.

---

### 3. بروتوكولات الـ Web Browsing

#### 1. بروتوكول HTTP (`HyperText Transfer Protocol`)
* **الوظيفة:** نقل وعرض صفحات الويب ومحتويات الـ HTML والوسائط بين متصفح الـ Client والـ `Web Server`.
* **الـ Port و بروتوكول الـ Transport:** يعمل عبر **Port 80** بالاعتماد على بروتوكول **TCP**.
* **مكونات الـ HTTP Request:**
  * **Request Method:** لتحديد الإجراء المطلوب (مثل `GET` لطلب بيانات، `POST` لإرسال بيانات، `PUT` لتحديث بيانات).
  * **Request-URL:** المسار المحدد للمورد أو الصفحة المطلوبة.
  * **HTTP Version:** إصدار البروتوكول المستخدم.
* **مكونات الـ HTTP Response (Status Codes):**
  * يرد الـ Server بـ `Status Code` لتوضيح حالة الطلب، أشهرها:
    * `200 OK`: الطلب سليم وتم استلام المحتوى.
    * `404 Not Found`: الصفحة غير موجودة أو الرابط خطأ.
* **الوضع الأمني والعيوب:**
  * ينقل البيانات بالكامل **Clear Text / Plain Text**.
  * أي شخص على الشبكة المحلية يمكنه عبر برامج الـ Sniffing قراءة الصفحات ومحتويات الاستمارات وكلمات المرور المرسلة مما يجعله عرضة لهجمات `Man-in-the-Middle`.

#### 2. بروتوكول HTTPS (`HyperText Transfer Protocol Secure`)
* **الوظيفة:** التصفح المشفر والآمن لمواقع الويب لحماية الخصوصية والمعاملات الحساسة.
* **الـ Port و بروتوكول الـ Transport:** يعمل عبر **Port 443** بالاعتماد على بروتوكول **TCP**.
* **آلية الحماية والتشفير:**
  * دمج الـ HTTP مع بروتوكولات التشفير: تاريخياً `SSL` وحديثاً المعيار المعتمد `TLS`.
  * يعتمد على الشهادات الرقمية الصادرة من جهات معتمدة لإثبات هوية السيرفر وتشفير جلسة الاتصال بمفاتيح غير متماثلة (`Asymmetric Encryption`).

---

### 4. بروتوكولات الـ Remote Management

#### 1. بروتوكول Telnet (Telecommunication Network)
* **الوظيفة:** إدارة أجهزة الشبكات (الراوترات والسويتشات والسيرفرات) والتحكم فيها عن بعد عبر واجهة سطر الأوامر (`CLI`).
* **الـ Port و بروتوكول الـ Transport:** يعمل عبر **Port 23** بالاعتماد على بروتوكول **TCP**.
* **العيوب الأمنية:** يرسل كافة البيانات وحركات لوحة المفاتيح وكلمات المرور **Clear Text**.

#### 2. بروتوكول SSH (Secure Shell)
* **الوظيفة:** البديل الآمن والحديث لبروتوكول `Telnet` لإدارة وضبط أجهزة الشبكة.
* **الـ Port و بروتوكول الـ Transport:** يعمل عبر **Port 22** بالاعتماد على بروتوكول **TCP**.
* **المزايا الأمنية:** تشفير كامل وشامل لكافة جلسات التخاطب وكلمات المرور المتبادلة بين العميل والجهاز (`Encrypted Channel`).

---

### 5. بروتوكولات الـ File Transfer & Sharing

#### 1. بروتوكول FTP (File Transfer Protocol)
* **الوظيفة:** تحميل ونقل الملفات من وإلى السيرفرات المخصصة.
* **آلية العمل:** يعتمد على بروتوكول **TCP** ويستخدم منفذين منفصلين:
  * **Port 21 (Control Connection):** مخصص لإرسال أوامر التحكم والتحقق من الـ Authentication.
  * **Port 20 (Data Connection):** مخصص لنقل وتدفق ملفات البيانات الفعلية.
* **العيوب الأمنية:** يرسل البيانات غير مشفرة (Clear Text).

#### 2. بروتوكول TFTP (Trivial File Transfer Protocol)
* **الوظيفة:** بروتوكول خفيف وبسيط يستخدم في أجهزة الشبكة لحفظ النسخ الاحتياطية للإعدادات (`Running-config Backup`) وترقية أنظمة التشغيل (`IOS Images`).
* **الـ Port و بروتوكول الـ Transport:** يعمل عبر **Port 69** بالاعتماد على بروتوكول **UDP**.
* **الخصائص:** سريع، لا يتطلب `Authentication`، ويستخدم في الشبكات المحلية الموثوقة.

#### 3. بروتوكولات مشاركة الملفات (SMP, NFS, P2P)
* **SMP (Server Message Block):** لمشاركة الملفات في أنظمة Windows.
* **NFS (Network File System):** لمشاركة الملفات في أنظمة Linux.
* **P2P (Peer-to-Peer):** بروتوكول التورنت لمشاركة وتنزيل الملفات عبر أجهزة متعددة متصلة معاً؛ حيث يتشارك الجميع في الرفع والتحميل، مما يزيد السرعة مع زيادة عدد المستخدمين بعكس سيرفرات الـ FTP المركزية.

---

### 6. بروتوكولات الـ E-mail

#### 1. بروتوكول SMTP (Simple Mail Transfer Protocol)
* **الوظيفة:** إرسال ونقل رسائل البريد الإلكتروني (`Push Protocol`). ينقل الرسائل من الـ `Mail Client` إلى الـ `Mail Server`، وبين السيرفرات المختلفة.
* **الـ Port و بروتوكول الـ Transport:** يعمل عبر **Port 25** بالاعتماد على بروتوكول **TCP**.

#### 2. بروتوكول POP3 (Post Office Protocol version 3)
* **الوظيفة:** استقبال وتنزيل رسائل البريد الإلكتروني من السيرفر إلى جهاز المستخدم (`Pull Protocol`).
* **الـ Port و بروتوكول الـ Transport:** يعمل عبر **Port 110** بالاعتماد على بروتوكول **TCP**.
* **الخصائص:** يقوم بتحميل الرسائل بالكامل وحذفها من السيرفر (`Cut`)، ولا يدعم المزامنة `Sync` بين أجهزة متعددة.

#### 3. بروتوكول IMAP4 (Internet Message Access Protocol version 4)
* **الوظيفة:** استقبال وتصفح رسائل البريد الإلكتروني مع دعم المزامنة السحابية الكاملة.
* **الـ Port و بروتوكول الـ Transport:** يعمل عبر **Port 143** بالاعتماد على بروتوكول **TCP**.
* **الخصائص:** يتيح إدارة الرسائل مباشرة أثناء بقائها مخزنة على السيرفر (`Copy`)، مما يسمح بفتح البريد من عدة أجهزة في نفس الوقت.

---

### 7. Quick Architecture Summary

<div dir="rtl">

<table border="1" cellpadding="8" cellspacing="0" width="100%">
  <thead>
    <tr bgcolor="#161b22">
      <th align="center">البروتوكول</th>
      <th align="center">الـ Port</th>
      <th align="center">الـ Transport Protocol</th>
      <th align="center">الوظيفة</th>
      <th align="center">الحالة الأمنية والملاحظات</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="center"><strong>HTTP</strong></td>
      <td align="center"><code>80</code></td>
      <td align="center"><code>TCP</code></td>
      <td>نقل وتصفح صفحات الويب ومحتوى HTML</td>
      <td align="center">Clear-Text</td>
    </tr>
    <tr>
      <td align="center"><strong>HTTPS</strong></td>
      <td align="center"><code>443</code></td>
      <td align="center"><code>TCP</code></td>
      <td>التصفح الآمن لصفحات الويب</td>
      <td align="center"><strong>Encrypted (TLS / SSL)</strong></td>
    </tr>
    <tr>
      <td align="center"><strong>SSH</strong></td>
      <td align="center"><code>22</code></td>
      <td align="center"><code>TCP</code></td>
      <td>إدارة عن بعد عبر الـ CLI</td>
      <td align="center"><strong>Encrypted</strong></td>
    </tr>
    <tr>
      <td align="center"><strong>Telnet</strong></td>
      <td align="center"><code>23</code></td>
      <td align="center"><code>TCP</code></td>
      <td>إدارة عن بعد عبر الـ CLI</td>
      <td align="center"><strong>Clear-Text</strong></td>
    </tr>
    <tr>
      <td align="center"><strong>FTP</strong></td>
      <td align="center"><code>20</code> (Data)<br><code>21</code> (Control)</td>
      <td align="center"><code>TCP</code></td>
      <td>نقل وتحميل الملفات</td>
      <td align="center"><strong>Clear-Text</strong></td>
    </tr>
    <tr>
      <td align="center"><strong>TFTP</strong></td>
      <td align="center"><code>69</code></td>
      <td align="center"><code>UDP</code></td>
      <td>نقل نسخ الـ IOS والـ Config بسرعة</td>
      <td align="center"><strong>No Authentication</strong></td>
    </tr>
    <tr>
      <td align="center"><strong>SMTP</strong></td>
      <td align="center"><code>25</code></td>
      <td align="center"><code>TCP</code></td>
      <td>إرسال البريد الإلكتروني (Push)</td>
      <td align="center">نقل قياسي</td>
    </tr>
    <tr>
      <td align="center"><strong>POP3</strong></td>
      <td align="center"><code>110</code></td>
      <td align="center"><code>TCP</code></td>
      <td>تنزيل البريد محلياً مع حذفه من السيرفر</td>
      <td align="center">بدون Sync</td>
    </tr>
    <tr>
      <td align="center"><strong>IMAP4</strong></td>
      <td align="center"><code>143</code></td>
      <td align="center"><code>TCP</code></td>
      <td>تصفح البريد مع المزامنة المباشرة</td>
      <td align="center">دعم الـ Sync للأجهزة المتعددة</td>
    </tr>
  </tbody>
</table>

</div>