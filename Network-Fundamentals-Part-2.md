# Network Fundamentals - Part 2

### كارت الشبكة (Network Interface Card - NIC)
هو الـ Hardware الأساسي المسؤول عن توصيل أي جهاز (PC أو Laptop) بالشبكة لنقل واستقبال البيانات

---

### سرعات كارت الشبكة (NIC Speeds)
سرعة نقل البيانات في الشبكات بتتقاس بوحدة `Bits per second` (`bps`) مش بالـ `Byte` ومتقسمة لثلاث فئات أساسية:
* **Ethernet:** سرعته تصل إلى 10 Mbps
* **Fast Ethernet:** سرعته تصل إلى 100 Mbps
* **Gigabit Ethernet:** سرعته تصل إلى 1000 Mbps (1 Gbps)

---

### الماك أدرس (MAC Address)
هو الـ `Physical Address` الخاص بكارت الشبكة وهو عبارة عن معرف فريد ومستحيل يتكرر عالمياً (`Globally Unique Identifier`) والشركة المصنعة بتحرقه مباشرة على الكارت (`Burned-In Address - BIA`)

* **الصيغة والحجم:** بيتكون من 48 Bits ومكتوب بنظام الـ Hexadecimal (أرقام 0-9 وحروف A-F) في 12 خانة
* **تركيب العنوان:** ينقسم إلى جزئين رئيسيين:
  * **OUI (Organizationally Unique Identifier):** أول 6 خانات (24 bits) كود محجوز من منظمة IEEE لتحديد الشركة المصنعة (`Vendor`)
  * **Vendor Assigned:** آخر 6 خانات (24 bits) بيمثلوا السيريال نمبر الخاص بالكارت نفسه (`Serial Number`)

---

### طرق إرسال البيانات (Transmission Modes)
* **Simplex:** اتصال أحادي الاتجاه فقط (`Unidirectional`) البيانات بتتحرك في مسار واحد مستحيل ترجع فيه زي الراديو والتلفزيون
* **Half-Duplex:** اتصال في الاتجاهين ثنائي (`Bidirectional`) ولكن ليس في نفس الوقت (`Not Simultaneously`) لو جهازين حاولوا يعملوا Send في نفس الوقت بيحصل تصادم للبيانات (`Collision`) زي أجهزة اللاسلكي (`Walkie-Talkie`) وجهاز الـ Hub
* **Full-Duplex:** اتصال في الاتجاهين ثنائي في نفس الوقت (`Simultaneously`) لوجود مسار مستقل للإرسال ومسار مستقل للاستقبال زي التليفون وجهاز الـ Switch

---

### أنواع الاتصال (Communication Types)
* **Unicast:** إرسال `One-to-One` من جهاز مرسل إلى جهاز مستقبل واحد محدد على الشبكة
* **Multicast:** إرسال `One-to-Many` من جهاز مرسل إلى مجموعة أجهزة معينة مشتركة في نفس الخدمة (`Multicast Group`)
* **Broadcast:** إرسال `One-to-All` من جهاز مرسل لجميع الأجهزة المتصلة داخل نطاق الشبكة المحلية بلا استثناء

---

### مقارنة الأجهزة الأساسية (Network Devices Comparison: Hub vs Switch)

| وجه المقارنة | Legacy Hub | Modern Switch |
| :--- | :--- | :--- |
| **أسلوب التوجيه** | بيشتغل بنظام الـ **Broadcast** دايماً وبيعمل إغراق للداتا (`Flooding`) على كل الـ Ports | بيشتغل بنظام الـ **Unicast** وبيوجه الـ Frame للبورت المطلوب فقط |
| **نمط النقل (Duplex)** | شغال بنظام الـ **Half-Duplex** | شغال بنظام الـ **Full-Duplex** |
| **فهم العناوين (Addressing)** | جهاز **Layer 1** غبي مبيفهمش الماك أدرس (`No MAC Address Processing`) | جهاز **Layer 2** ذكي بيبني ويفحص جدول الماك أدرس (`MAC Address Table`) |
| **نطاق التصادم (Collision)** | بيمثل **Single Collision Domain** واحد لكل الأجهزة مما يسبب بطء وتصادم عالي | بيعمل عزل تام وتجزيء لكل منفذ بحيث كل Port هو **Independent Collision Domain** |