🇮🇷 فارسی | 🌍 [English Version](README.md)

**ریپازیتوری:** [github.com/akaravi/k-n](https://github.com/akaravi/k-n)

# Karavi Tunnel — دستورات

## سرور ایران — نصب

```bash
bash <(curl -Ls https://raw.githubusercontent.com/akaravi/k-n/main/install.sh)
sudo karavi-tunnel
```

## سرور ایران — تنظیم

```
1
2
1
7000
7001
y
```

## سرور خارج — نصب

```bash
bash <(curl -Ls https://raw.githubusercontent.com/akaravi/k-n/main/install.sh)
sudo karavi-tunnel
```

## سرور خارج — تنظیم

```
1
1
1
IR_IP
7000
7001
```

## سرور ایران — اجرا

```
2
2
1
2
5
```

## سرور خارج — اجرا

```
2
1
1
2
5
```

## BBR

```
8
```

## Cron

```
3
1
```

## عملکرد (اختیاری)

```bash
export KARAVI_POOL=512
export USER_WORKERS=128
export AUTO_SOCKBUF=1
export BUF_COPY_BYTES=262144
export METRICS_PORT=9109
```

## عیب‌یابی

```bash
systemctl status karavi
ss -lntp
nc -zv IR_IP 7000
screen -ls
sudo karavi-tunnel
```
