🇮🇷 فارسی | 🌍 [English Version](README.md)

**ریپازیتوری:** [github.com/akaravi/k-n](https://github.com/akaravi/k-n)

# Karavi Tunnel

مدیریت‌کننده Reverse TCP Tunnel برای اتصال **سرور ایران (IR)** و **سرور خارج (EU)**.

چند اسلات • AutoSync • بررسی سلامت Cron • بهینه‌سازی BBR

---

## معرفی

Karavi Tunnel یک ارتباط TCP معکوس پایدار بین دو سرور برقرار می‌کند:

- **سرور IR** — ترافیک کاربر را دریافت و از طریق تونل ارسال می‌کند
- **سرور EU** — به سمت خارج متصل می‌شود و ترافیک را به IR می‌رساند

هر سرور تا **۱۰ پروفایل مستقل** ذخیره می‌کند (`iran1`…`iran10`، `eu1`…`eu10`).

---

## معماری

```
کاربر → سرور IR ⇄ سرور EU
                │
        Bridge Port (تونل اصلی)
                │
         Sync Port (AutoSync)
```

| پورت | نقش |
|------|-----|
| **Bridge** | تونل TCP اصلی (پیش‌فرض `7000`) |
| **Sync** | همگام‌سازی خودکار پورت‌ها (پیش‌فرض `7001`) |

Bridge و Sync باید در هر دو سرور یکسان باشند.

---

## قابلیت‌ها

| قابلیت | توضیح |
|--------|--------|
| Reverse TCP Tunnel | اتصال پایدار IR ⇄ EU |
| Multi-Slot (1–10) | تا ۱۰ کانفیگ جدا برای هر نقش |
| AutoSync | EU پورت‌های باز را خودکار به IR می‌فرستد |
| Cron Health Check | ریستارت خودکار session متوقف‌شده |
| بهینه‌سازی BBR | تنظیم sysctl و BBR |
| Screen | مدیریت سبک پروسه‌ها |
| Self-update | به‌روزرسانی منیجر از GitHub |

---

## نصب سریع

روی **هر دو** سرور IR و EU:

```bash
bash <(curl -Ls https://raw.githubusercontent.com/akaravi/k-n/main/install.sh)
sudo karavi-tunnel
```

**نصب کامل** (وابستگی‌های اضافه: cron، iptables، nftables، haproxy، socat):

```bash
bash <(curl -Ls https://raw.githubusercontent.com/akaravi/k-n/main/install.sh) full
sudo karavi-tunnel
```

---

## مرحله ۱ — تنظیم سرور ایران

اجرای منیجر:

```bash
sudo karavi-tunnel
```

ایجاد پروفایل:

```
1          → Create/Update profile
2          → IRAN
1          → اسلات ۱ (یا هر اسلات ۱ تا ۱۰)
7000       → Bridge port
7001       → Sync port
y          → فعال‌سازی AutoSync
```

---

## مرحله ۲ — تنظیم سرور خارج

همان دستور نصب، سپس:

```
1          → Create/Update profile
1          → EU
1          → همان شماره اسلات IR
IR_IP      → IP عمومی سرور ایران
7000       → همان Bridge port
7001       → همان Sync port
```

---

## اجرای تونل

### روی IR

```
2          → Manage tunnel
2          → IRAN
1          → شماره اسلات
2          → Start
5          → Status   (باید Running: ON باشد)
```

### روی EU

```
2          → Manage tunnel
1          → EU
1          → همان اسلات
2          → Start
5          → Status
```

---

## مرجع منوی اصلی

| # | عملیات |
|---|--------|
| 1 | ایجاد/ویرایش پروفایل |
| 2 | مدیریت تونل (شروع / توقف / وضعیت / لاگ) |
| 3 | فعال‌سازی cron health-check |
| 4 | غیرفعال‌سازی cron health-check |
| 5 | نصب اسکریپت در سیستم (`/usr/local/bin/karavi-tunnel`) |
| 6 | به‌روزرسانی از GitHub |
| 7 | حذف نصب |
| 8 | بهینه‌سازی سرور (BBR + sysctl) |
| 0 | خروج |

---

## اختیاری: بهینه‌سازی BBR

```
8          → Optimize server (BBR + sysctl)
```

---

## اختیاری: cron health-check

```
3          → Enable cron health-check
1          → فاصله بررسی به دقیقه (پیش‌فرض ۱)
```

غیرفعال‌سازی با گزینه **۴** منو.

---

## تنظیمات عملکرد

تغییر اندازه pool کارگرها:

```bash
export KARAVI_POOL=512
sudo karavi-tunnel
```

مقدار `KARAVI_POOL=0` (پیش‌فرض) اندازه را بر اساس محدودیت سیستم انتخاب می‌کند.

---

## ساختار پروژه

```
Karavi-Tunnel.sh   → منیجر تعاملی (منو، پروفایل، cron، BBR)
Karavi.py          → موتور اصلی تونل
install.sh         → نصب یک‌خطی
```

مسیرهای نصب:

| مسیر | کاربرد |
|------|--------|
| `/usr/local/bin/karavi-tunnel` | باینری منیجر |
| `/opt/karavi/Karavi.py` | هسته Python |
| `/etc/karavi_manager/profiles/` | کانفیگ اسلات‌ها |

---

## عیب‌یابی

```bash
screen -ls
ss -lntp
nc -zv IR_IP 7000
sudo karavi-tunnel
```

اتصال به session در حال اجرا از منو: **Manage tunnel → Logs** (خروج با `Ctrl+A` سپس `D`).

---

## سوالات متداول

**Bridge و Sync باید یکسان باشند؟**  
بله — در IR و EU دقیقاً یک مقدار.

**چند تونل هم‌زمان؟**  
بله — با اسلات‌های مختلف.

**بعد از ریبوت تونل قطع شد؟**  
cron health-check (منو ۳) را فعال کنید یا اسلات را دستی Start کنید.

**پورت دستی به‌جای AutoSync؟**  
هنگام ساخت پروفایل IR به AutoSync `n` بدهید و لیست CSV وارد کنید (مثلاً `80,443,2083`).

---

## لینک‌ها

- GitHub: [akaravi/k-n](https://github.com/akaravi/k-n)
- تلگرام: [@IlyaahD](https://t.me/IlyaahD)
