<div dir="rtl" align="right">

# Lecture 14 (Part 1): Advanced ACL Concepts & Troubleshooting

## 📝 أولاً: ملاحظات هامة (Notes)

* **الطريقة الحديثة لكتابة الـ ACL (Named vs Numbered):**
  الطريقة القديمة تعتمد على الأرقام (1-99 للـ Standard و 100-199 للـ Extended). الطريقة الحديثة تعتمد على تسمية الليست باسم معين وهي الأفضل والأكثر استخداماً في بيئة العمل وامتحانات سيسكو الجديدة.
* **التحكم الدقيق في الـ Extended ACL:**
  يمكنك المنع أو السماح بناءً على البروتوكول ورقم البورت (Layer 3, 4, 7). يمكنك استخدام بروتوكولات مثل `IP` (يشمل ويسمح بكل شيء)، `ICMP` (للبنج)، `TCP`، أو `UDP`.
  كما يمكنك تحديد التطبيقات باستخدام كلمة `eq` (تساوي)، أمثلة: (`eq 80` للـ HTTP)، (`eq 443` للـ HTTPS)، (`eq 53` للـ DNS).
* **اختصارات الـ Wildcard Mask:**
  * كلمة `host`: تستخدم لتحديد جهاز واحد فقط (بدلاً من كتابة المربع الصفري 0.0.0.0).
  * كلمة `any`: تستخدم لتحديد كل الشبكات (بدلاً من كتابة 255.255.255.255).
* **قواعد وضع الـ ACL (Placement Rules):**
  * **الـ Standard ACL:** يُفضل وضعها أقرب ما يكون **للهدف (Destination)**، لأنها تفلتر بناءً على المصدر فقط، فوضعها مبكراً قد يمنع الجهاز من الوصول لشبكات أخرى مسموح له بها.
  * **الـ Extended ACL:** يُفضل وضعها أقرب ما يكون **للمصدر (Source)** لمنع الترافيك غير المرغوب فيه مبكراً وتوفير استهلاك موارد الشبكة.
* **ملاحظة تتبع الأخطاء (Troubleshooting):**
  عند عمل Ping، تذكر أن الرسالة تذهب (Echo Request) وتعود (Echo Reply). إذا تم منع مسار العودة في الراوتر، سيفشل البنج رغم وصول الرسالة، لذلك يجب تتبع مسار الداتا ذهاباً وإياباً باستخدام وضع الـ Simulation.

---

## ⚙️ ثانياً: أوامر الإعداد (Configuration Commands)

1. إنشاء أكسس ليست عادية (Standard) بالاسم (Named ACL):
(تُكتب في وضع الـ Global Config)
</div>

```bash
Router(config)# ip access-list standard MOHSEN
```

<div dir="rtl" align="right">
2. إنشاء أكسس ليست متقدمة (Extended) بالاسم:
(تُكتب في وضع الـ Global Config)
</div>

```bash
Router(config)# ip access-list extended AMIR
```

<div dir="rtl" align="right">
3. السماح لبروتوكول معين (مثل TCP) بالمرور لتطبيق معين (مثل HTTP بورت 80):
(تُكتب داخل إعدادات الـ Extended ACL، مع استخدام اختصار host لتحديد الجهاز الهدف)
</div>

```bash
Router(config-ext-nacl)# permit tcp 192.168.1.0 0.0.0.255 host 10.0.0.1 eq 80
```

<div dir="rtl" align="right">
4. تطبيق الـ ACL على بورت معين (Interface) وتحديد اتجاه الفلترة (in أو out):
(تُكتب داخل إعدادات الـ Interface، ويجب كتابة اسم الـ ACL بنفس حالة الأحرف كابيتال أو سمول)
</div>

```bash
Router(config)# interface gigabitEthernet 0/0
Router(config-if)# ip access-group AMIR in
```

</div>