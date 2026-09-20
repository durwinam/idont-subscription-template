<h1 align="center">idont-subscription</h1>

<p align="center">
  نسخهٔ پایدار 1.2.3 — داشبورد اشتراک مدرن و Glass با APIهای اصلی Pasarguard
</p>

<p align="center">
  <a href="#نصب-خودکار">نصب خودکار</a> ·
  <a href="#نصب-دستی">نصب دستی</a> ·
  <a href="#سفارشی‌سازی-برند">سفارشی‌سازی برند</a> ·
  <a href="#تنظیمات-پنل">تنظیمات پنل</a> ·
  <a href="#نسخه‌های-دیگر">نسخه‌های دیگر</a>
</p>

---

## ویژگی‌ها

- همهٔ امکانات نسخهٔ استاندارد
- نام برند، زیرعنوان و لوگوی سفارشی
- اعمال خودکار برندینگ با اسکریپت نصب
- داشبورد Glassmorphism قرمز/مشکی با نورپردازی متحرک
- حالت Light با سفید/قرمز و حالت Dark با مشکی/قرمز
- نمودار مصرف Smooth روزانه و هفتگی بر پایه API خود Pasarguard
- نمودار دایره‌ای واقعی برای روزهای باقی‌مانده
- QR اشتراک و QR کانفیگ با همان لینک‌های واقعی Subscription
- کپی لینک، کانفیگ‌ها، اطلاعات اتصال و اپلیکیشن‌ها
- بخش اپلیکیشن‌ها با فیلتر سیستم‌عامل، کارت‌های Glass و چیدمان واکنش‌گرا
- رابط واکنش‌گرا برای موبایل، Telegram WebView، تبلت و دسکتاپ
- یک فایل HTML — بدون Node.js و build

---

## پایداری و امنیت

- این قالب از **Subscription Token** همان URL اشتراک استفاده می‌کند و هیچ API Key مدیریتی را داخل Frontend قرار نمی‌دهد.
- Subscription Token در `localStorage` ذخیره نمی‌شود؛ صفحه آن را از URL اشتراک دریافت می‌کند.
- Endpointهای اطلاعات، کانفیگ، برنامه‌ها و مصرف از مسیر واقعی Subscription همان پنل خوانده می‌شوند.
- QR Code فقط پس از درخواست کاربر ساخته می‌شود و متن QR از همان لینک واقعی Subscription/Config گرفته می‌شود.
- قالب با ساختارهای رایج Pasarguard مانند `/sub/<token>/info` و مسیرهای سفارشی مانند `/info/<token>/info` سازگار است.


## نصب خودکار

روی سرور **Ubuntu** با Pasarguard نصب‌شده:

```bash
curl -fsSL https://raw.githubusercontent.com/durwinam/idont-subscription-template/main/install.sh -o /tmp/idont-subscription-install.sh && sudo bash /tmp/idont-subscription-install.sh
```

یا:

```bash
wget -qO /tmp/idont-subscription-install.sh https://raw.githubusercontent.com/durwinam/idont-subscription-template/main/install.sh && sudo bash /tmp/idont-subscription-install.sh
```


در منو گزینه **۳) idont-subscription** را انتخاب کنید. سپس از شما پرسیده می‌شود:

- **نام برند** (FA و EN)
- **زیرعنوان / توضیح کوتاه** (FA و EN)
- **آدرس لوگو** (`https://` — قبل از نصب اعتبارسنجی می‌شود)

### اسکریپت چه کار می‌کند؟

1. منوی انتخاب نسخه (`Lite` / `idont-subscription` / `Pro`)
2. برای Pro: دریافت اطلاعات برند و patch خودکار روی `index.html`
3. ذخیره در:

```text
/var/lib/pasarguard/templates/subscription/index.html
```

4. به‌روزرسانی `/opt/pasarguard/.env`:

```env
CUSTOM_TEMPLATES_DIRECTORY="/var/lib/pasarguard/templates/"
SUBSCRIPTION_PAGE_TEMPLATE="subscription/index.html"
```

5. اجرای `pasarguard restart`

**قرارداد قالب (این کلیدها را ثابت نگه دارید):**

```javascript
var IDONT_SUBSCRIPTION_DEFAULT_BRAND = {
  name: "...",
  subtitle: { fa: "...", en: "..." },
  logoUrl: "..."
};
```

شناسه‌های HTML اختیاری: `brand-title`، `brand-subtitle`، `brand-img`

> **پیش‌نیازها:** `wget`، `curl`، `python3`

---

## نصب دستی

### ۱. دانلود قالب

```bash
sudo mkdir -p /var/lib/pasarguard/templates/subscription/
sudo wget -N -O /var/lib/pasarguard/templates/subscription/index.html \
  https://raw.githubusercontent.com/durwinam/idont-subscription-template/main/index.html
```

### ۲. ویرایش برند (اختیاری)

فایل را باز کنید و بلوک `DEFAULT_BRAND` را ویرایش کنید:

```javascript
var IDONT_SUBSCRIPTION_DEFAULT_BRAND = {
  name: "نام برند شما",
  subtitle: {
    fa: "پنل اشتراک",
    en: "Subscription panel"
  },
  logoUrl: "https://example.com/logo.png"
};
```

### ۳. تنظیم Pasarguard

```bash
sudo nano /opt/pasarguard/.env
```

اضافه یا به‌روز کنید:

```env
CUSTOM_TEMPLATES_DIRECTORY="/var/lib/pasarguard/templates/"
SUBSCRIPTION_PAGE_TEMPLATE="subscription/index.html"
```

### ۴. راه‌اندازی مجدد

```bash
sudo pasarguard restart
```

> برای اعمال خودکار برندینگ، از **اسکریپت نصب** استفاده کنید.

---

## سفارشی‌سازی برند

### در کد (`index.html`)

```javascript
var IDONT_SUBSCRIPTION_DEFAULT_BRAND = {
  name: "idont-subscription",
  subtitle: {
    fa: "پنل اشتراک",
    en: "Subscription panel"
  },
  logoUrl: ""
};
```

### Inject قبل از لود صفحه

```html
<script>
  window.IDONT_SUBSCRIPTION_BRAND = {
    name: "نام برند شما",
    subtitle: { fa: "زیرعنوان", en: "Your tagline" },
    logoUrl: "https://example.com/logo.png"
  };
</script>
```

---

## تنظیمات پنل

1. پنل Pasarguard → **Settings → Subscription**
2. ویرایش **announcement** و **announcement link**
3. افزودن/ویرایش اپ‌ها در بخش apps

---


## نسخه‌های دیگر

- [idont-subscription](https://github.com/durwinam/idont-subscription-template) — سبک‌تر و سریع‌تر
- [idont-subscription](https://github.com/durwinam/idont-subscription-template) — نسخهٔ استاندارد

## v1.2.3

- بازطراحی کامل فقط بخش اپلیکیشن‌ها
- فیلتر سریع iOS، Android، Windows، Linux و سایر سیستم‌عامل‌های موجود در API
- کارت‌های اپلیکیشن با آیکن، توضیح، وضعیت پیشنهادی و دکمه‌های واقعی دانلود/افزودن اشتراک
- حفظ کامل داده‌ها و لینک‌های API فعلی اپلیکیشن‌ها
- بهبود نمایش موبایل و حذف چیدمان Accordion قدیمی که باعث فضای خالی و هم‌پوشانی می‌شد
