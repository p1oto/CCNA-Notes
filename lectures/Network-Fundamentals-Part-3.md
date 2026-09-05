# Network Fundamentals - Part 3

### 1. بيانات الفريم والباكيت (Frame & Packet Headers)
أي جهاز عشان يبعت داتا لجهاز تاني داخل الشبكة المحلية لازم الفريم يحتوي على 4 عناصر رئيسية:
* Source IP Address
* Destination IP Address
* Source MAC Address
* Destination MAC Address

---

### 2. بروتوكول ARP (Address Resolution Protocol)
* وظيفته الأساسية هي معرفة الـ MAC Address المقابل لـ IP Address معروف داخل نفس الـ Local Subnet
* آلية العمل:
  1. لو الجهاز عارف الـ IP بس مش عارف الـ MAC للهدف بيبعت طلب اسمه `ARP Request`
  2. رسالة الـ `ARP Request` بتتبعت بنظام الـ **Broadcast** لكل الأجهزة في الشبكة
  3. الجهاز صاحب الـ IP المطلوب بيرد لوحده برسالة `ARP Reply` بنظام الـ **Unicast** موضحاً فيها الـ MAC Address بتاعه
  4. الجهاز المرسل بيخزن الناتج عنده في جدول اسمه `ARP Cache / ARP Table` عشان ميكررش الطلب كل مرة

---

### 3. آلية عمل السويتش 
السويتش بيبني جدول اسمه `MAC Address Table` عشان يعرف كل بورت متوصل عليه أنهي MAC:
* **تسجيل العناوين:** السويتش بيبص دايماً على الـ **Source MAC** للفريم اللي داخل ويسجله في الجدول مع رقم المنفذ (Port) المقابل ليه
* **التوجيه:**
  * لو الـ Destination MAC متسجل في الجدول: السويتش بيعمل Forward للفريم مباشرة للبورت المطلوب (Unicast)
  * لو الـ Destination MAC غير موجود في الجدول: السويتش بيبعت الفريم لكل البورتات ما عدا البورت اللي استقبل منه الداتا، وتسمى هذه العملية `Unknown Unicast Flooding` لحد ما الجهاز يرد ويتسجل في الجدول

---

### 4. Collision Domain vs Broadcast Domain

**Collision Domain:**
* المساحة أو النطاق في الشبكة اللي ممكن يحصل فيها تصادم للبيانات لو جهازين بعتوا مع بعض
* الـ Hub: الشبكة كلها بالكامل تعتبر **Single Collision Domain**
* الـ Switch: كل منفذ (Port) مستقل يعتبر **Independent Collision Domain** منفصل تماماً (Zero Collisions في وضع Full-Duplex)

**Broadcast Domain:**
* النطاق أو المجموعة من الأجهزة اللي بتستقبل رسالة الـ Broadcast لو جهاز واحد بعتها
* الـ Switch: افتراضياً السويتش بالكامل وجميع منافذه تعتبر **Single Broadcast Domain** واحد
* حل مشكلة اتساع الـ Broadcast Domain: يتم تقطيع الشبكة وعزلها منطقياً باستخدام الـ **VLANs (Virtual LANs)** أو بوضع **Router** لأن الراوتر هو الجهاز الذي يكسر ويوقف انتشار رسائل الـ Broadcast