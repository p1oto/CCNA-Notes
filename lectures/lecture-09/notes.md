# 09 - Fixed Subnetting

### مفهوم تقسيم الشبكات (`Subnetting`)

تُقسم عناوين الـ `IP` إلى فئات (`Classes`) رئيسية، وبدون تقسيم إضافي يتم إهدار عدد كبير من العناوين (`IP Waste`) عند إعطاء فئة كاملة لشبكة صغيرة
* تقنية الـ `Subnetting` تُستخدم لتقسيم شبكة رئيسية واحدة إلى مجموعة من الشبكات الأصغر، سواء كانت متساوية الحجم أو غير متساوية
* في التقسيم الثابت (`Fixed Subnetting`)، يتم تقسيم الشبكة إلى مجموعة من الشبكات الفرعية التي تحتوي جميعها على عدد متساوٍ من الـ `Hosts`

---

### خطوات حساب التقسيم الثابت (`Subnetting Steps`)

1. تحديد عدد الأجهزة (`Hosts`) المطلوبة في كل شبكة فرعية
2. إيجاد أقرب مضاعف للرقم 2 بحيث يكون ناتجه أكبر من أو يساوي عدد الـ `Hosts` المطلوب مضافاً إليه 2 (لتعويض الـ `IPs` المحجوزة للـ `Network ID` والـ `Broadcast ID`)
3. حساب قناع الشبكة الجديد (`New Subnet Mask`) عن طريق طرح عدد بتات الـ `Hosts` المستعارة من 32 للحصول على البادئة الجديدة (`Prefix Length`)
4. تقسيم الشبكة الأصلية إلى شبكات فرعية (`Subnets`) بناءً على حجم القفزة (`Block Size`) الذي تم حسابه

---

### تقسيم الشبكات الثابت (`Fixed Subnetting`)

في الـ `Fixed Subnetting` بنقسم الشبكة إلى مجموعة من الشبكات فيها عدد متساوي من الـ `Hosts`
مثلاً لو عايزين نقسم الشبكة `192.168.1.0/24` لمجموعة من الشبكات من 15 `IP`
الرمز `/24` يسمى `Prefix` وهو طريقة أخرى لتمثيل الـ `Subnet Mask` وبيمثل عدد الـ `Bits` اللي بتساوي 1 من الشمال لليمين

أولاً لتقسيم الشبكة لمجموعة متساوية من الشبكات من 15 `IP` هنستخدم المعادلة $2^n - 2 = x$ حيث $x$ هي عدد الـ `IPs` اللي إحنا عايزينه، و $n$ هو عدد الـ `Bits` المحجوزة للـ `Hosts`، و 2 هو الـ `IP` المحجوز للـ `Broadcast` والـ `IP` المحجوز لعنوان الشبكة

ثانياً هنشوف أقرب رقم للـ $n$ يخلي ناتج المعادلة أكبر من أو يساوي 15:
$$2^n \ge 15 + 2$$

أقرب رقم للـ $n$ هو 5 واللي هيدينا 32 `IP` في الشبكة الواحدة
وبالتالي آخر `Octet` في منه 5 `Bit` من اليمين محجوزين لعدد الـ `Hosts`

الـ `Subnet Mask` مع الـ `Prefix` `/24` كان `11111111.11111111.11111111.00000000`
بعد الـ `Subnetting` يتم التحديث إلى `/27` ليصبح `255.255.255.224`

أول شبكة هتكون:
* **`Network address`**: `192.168.1.0`
* **`Usable IP range`**: `192.168.1.1` إلى `192.168.1.30`
* **`Broadcast address`**: `192.168.1.31`

يتم اعتبار العنوان الصفري، لذلك نزيد بمقدار 32 في كل مرة للحصول على الـ `Block` الجديد
---

### استخراج نطاقات الشبكات الفرعية (`Subnet Ranges`)

مع كل قفزة بمقدار 32، نستخرج الـ `Network ID` (أول عنوان) والـ `Broadcast Address` (آخر عنوان)، وما بينهما هو النطاق المتاح للأجهزة (`Usable IP Range`)

<div dir="ltr">

<table border="1" cellpadding="8" cellspacing="0" width="100%">
  <thead>
    <tr bgcolor="#161b22">
      <th align="center">Subnet</th>
      <th align="center">Network ID (First IP)</th>
      <th align="center">Usable IP Range</th>
      <th align="center">Broadcast Address (Last IP)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="center"><strong>N1</strong></td>
      <td align="center"><code>192.168.1.0</code></td>
      <td align="center"><code>192.168.1.1</code> - <code>192.168.1.30</code></td>
      <td align="center"><code>192.168.1.31</code></td>
    </tr>
    <tr>
      <td align="center"><strong>N2</strong></td>
      <td align="center"><code>192.168.1.32</code></td>
      <td align="center"><code>192.168.1.33</code> - <code>192.168.1.62</code></td>
      <td align="center"><code>192.168.1.63</code></td>
    </tr>
    <tr>
      <td align="center"><strong>N3</strong></td>
      <td align="center"><code>192.168.1.64</code></td>
      <td align="center"><code>192.168.1.65</code> - <code>192.168.1.94</code></td>
      <td align="center"><code>192.168.1.95</code></td>
    </tr>
    <tr>
      <td align="center"><strong>N4</strong></td>
      <td align="center"><code>192.168.1.96</code></td>
      <td align="center"><code>192.168.1.97</code> - <code>192.168.1.126</code></td>
      <td align="center"><code>192.168.1.127</code></td>
    </tr>
    <tr>
      <td align="center"><strong>N5</strong></td>
      <td align="center"><code>192.168.1.128</code></td>
      <td align="center"><code>192.168.1.129</code> - <code>192.168.1.158</code></td>
      <td align="center"><code>192.168.1.159</code></td>
    </tr>
    <tr>
      <td align="center"><strong>N6</strong></td>
      <td align="center"><code>192.168.1.160</code></td>
      <td align="center"><code>192.168.1.161</code> - <code>192.168.1.190</code></td>
      <td align="center"><code>192.168.1.191</code></td>
    </tr>
    <tr>
      <td align="center"><strong>N7</strong></td>
      <td align="center"><code>192.168.1.192</code></td>
      <td align="center"><code>192.168.1.193</code> - <code>192.168.1.222</code></td>
      <td align="center"><code>192.168.1.223</code></td>
    </tr>
    <tr>
      <td align="center"><strong>N8</strong></td>
      <td align="center"><code>192.168.1.224</code></td>
      <td align="center"><code>192.168.1.225</code> - <code>192.168.1.254</code></td>
      <td align="center"><code>192.168.1.255</code></td>
    </tr>
  </tbody>
</table>

</div>