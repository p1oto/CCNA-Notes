# 11 - Packet Tracer Lab: Configuring RIP v2 Dynamic Routing

### 1. إزالة التوجيه اليدوي القديم (Cleanup)
قبل تفعيل التوجيه الديناميكي، يجب أولاً مسح أي إعدادات توجيه يدوي قديمة قمنا بعملها في اللابات السابقة حتى لا يحدث تعارض في جدول التوجيه.
- **الأمر (يُكتب في وضع الإعدادات config):** 
  <span dir="ltr"><code>no ip route [Destination_Network] [Subnet_Mask] [Exit_Interface]</code></span>

---

### 2. تفعيل بروتوكول الـ RIP v2
بدلاً من كتابة مسارات لكل شبكة، سنقوم فقط بتفعيل البروتوكول وإخباره بالشبكات **المتصلة به مباشرة** (Connected Networks).

**خطوات التفعيل على الراوتر:**
1. <span dir="ltr"><code>router rip</code></span> : الدخول إلى وضع إعدادات بروتوكول الـ RIP.
2. <span dir="ltr"><code>version 2</code></span> : ترقية البروتوكول إلى الإصدار الثاني لكي يفهم الـ Subnet Masks.
3. <span dir="ltr"><code>no auto-summary</code></span> : إلغاء التلخيص التلقائي للشبكات لضمان إرسال تفاصيل الـ Subnet بدقة.
4. <span dir="ltr"><code>network [Directly_Connected_Network]</code></span> : إعلان وتوزيع الشبكات الموصلة بالكروت المباشرة (يُكرر الأمر لكل شبكة متصلة).

---

### 3. مثال عملي على التوزيع (Configuration Example)
إذا كان لدينا راوتر يمتلك شبكة محلية <span dir="ltr"><code>192.168.1.0</code></span> وشبكة ربط WAN هي <span dir="ltr"><code>2.2.2.0</code></span>، نكتب الإعدادات كالتالي:

```text
Router(config)# router rip
Router(config-router)# version 2
Router(config-router)# no auto-summary
Router(config-router)# network 192.168.1.0
Router(config-router)# network 2.2.2.0
```
*(ملاحظة: نكرر نفس الخطوات على الراوتر المقابل ولكن بالشبكات الخاصة به)*

---

### 4. التحقق والفحص (Verification & Routing Table)
بعد تفعيل الـ RIP على جميع الرواتر، سيقوم البروتوكول بنشر التحديثات تلقائياً. للتأكد من نجاح العملية وعرض المسارات الجديدة:

- **أمر الفحص:** <span dir="ltr"><code>show ip route</code></span>
- **ما الذي سنراه في الجدول؟**
  * ستظهر الشبكات البعيدة في الجدول متبوعة بحرف **R** (دلالة على أنها تعلمتها تلقائياً عبر بروتوكول الـ RIP).
  * ستجد بجوارها رقم القفزات (Metric) ومعلومات كارت الخروج، ودون أن تضطر لكتابة أي مسار يدوياً.
- **اختبار الاتصال:** جرب عمل <span dir="ltr"><code>ping</code></span> من جهاز في الفرع الأول لجهاز في الفرع الثاني، وسترى الاستجابة الفورية والناجحة بنسبة 100%.