🌍 English | 🇮🇷 [نسخه فارسی](README_FA.md)

**Repository:** [github.com/akaravi/k-n](https://github.com/akaravi/k-n)

# Karavi Tunnel — Commands

## IR Server — Install

```bash
bash <(curl -Ls https://raw.githubusercontent.com/akaravi/k-n/main/install.sh)
sudo karavi-tunnel
```

## IR Server — Setup

```
1
2
1
7000
7001
y
```

## EU Server — Install

```bash
bash <(curl -Ls https://raw.githubusercontent.com/akaravi/k-n/main/install.sh)
sudo karavi-tunnel
```

## EU Server — Setup

```
1
1
1
IR_IP
7000
7001
```

## IR Server — Start

```
2
2
1
2
5
```

## EU Server — Start

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

## Cron Health Check

```
3
1
```

## Performance (optional)

```bash
export KARAVI_POOL=512
export USER_WORKERS=128
export AUTO_SOCKBUF=1
export BUF_COPY_BYTES=262144
export METRICS_PORT=9109
```

## Troubleshooting

```bash
systemctl status karavi
ss -lntp
nc -zv IR_IP 7000
screen -ls
sudo karavi-tunnel
```
