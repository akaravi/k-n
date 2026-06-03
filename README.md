🌍 English | 🇮🇷 [نسخه فارسی](README_FA.md)

**Repository:** [github.com/akaravi/k-n](https://github.com/akaravi/k-n)

# Karavi Tunnel

High-performance reverse TCP tunnel manager for connecting an **IR (Iran)** server with an **EU (outside)** server.

Multi-slot profiles • AutoSync • Cron health-check • BBR optimization

---

## Overview

Karavi Tunnel keeps a persistent reverse TCP link between two servers:

- **IR server** — accepts user traffic and forwards it through the tunnel
- **EU server** — connects outward and bridges traffic to the IR side

Each server can store up to **10 independent slot profiles** (`iran1`…`iran10`, `eu1`…`eu10`).

---

## Architecture

```
Client → IR Server ⇄ EU Server
             │
        Bridge Port (main tunnel)
             │
         Sync Port (AutoSync)
```

| Port | Role |
|------|------|
| **Bridge** | Main persistent TCP tunnel (default `7000`) |
| **Sync** | Automatic port synchronization (default `7001`) |

Bridge and Sync ports must match on both servers.

---

## Features

| Feature | Description |
|---------|-------------|
| Reverse TCP tunnel | Persistent IR ⇄ EU connection |
| Multi-slot (1–10) | Up to 10 saved configs per role |
| AutoSync | EU pushes open ports to IR automatically |
| Cron health-check | Restarts stopped tunnel sessions |
| BBR optimization | Optional sysctl / BBR tuning |
| Screen sessions | Lightweight process management |
| Self-update | Pull latest manager from GitHub |

---

## Quick install

On **both** IR and EU servers:

```bash
bash <(curl -Ls https://raw.githubusercontent.com/akaravi/k-n/main/install.sh)
sudo karavi-tunnel
```

**Full install** (extra deps: cron, iptables, nftables, haproxy, socat):

```bash
bash <(curl -Ls https://raw.githubusercontent.com/akaravi/k-n/main/install.sh) full
sudo karavi-tunnel
```

---

## Step 1 — IR server setup

Open the manager:

```bash
sudo karavi-tunnel
```

Create a profile:

```
1          → Create/Update profile
2          → IRAN
1          → slot 1 (or any slot 1–10)
7000       → Bridge port
7001       → Sync port
y          → Enable AutoSync
```

---

## Step 2 — EU server setup

Same install command, then:

```
1          → Create/Update profile
1          → EU
1          → same slot number as IR
IR_IP      → Iran server public IP
7000       → same Bridge port
7001       → same Sync port
```

---

## Start the tunnel

### On IR

```
2          → Manage tunnel
2          → IRAN
1          → slot number
2          → Start
5          → Status   (should show Running: ON)
```

### On EU

```
2          → Manage tunnel
1          → EU
1          → same slot
2          → Start
5          → Status
```

---

## Main menu reference

| # | Action |
|---|--------|
| 1 | Create/Update profile |
| 2 | Manage tunnel (start / stop / status / logs) |
| 3 | Enable cron health-check |
| 4 | Disable cron health-check |
| 5 | Install script system-wide (`/usr/local/bin/karavi-tunnel`) |
| 6 | Update script from GitHub |
| 7 | Uninstall script |
| 8 | Optimize server (BBR + sysctl) |
| 0 | Exit |

---

## Optional: BBR optimization

```
8          → Optimize server (BBR + sysctl)
```

---

## Optional: cron health-check

```
3          → Enable cron health-check
1          → interval in minutes (default 1)
```

Disable with menu option **4**.

---

## Performance tuning

Override the automatic worker pool size:

```bash
export KARAVI_POOL=512
sudo karavi-tunnel
```

`KARAVI_POOL=0` (default) lets the engine pick a size from system limits.

---

## Project structure

```
Karavi-Tunnel.sh   → interactive manager (menu, profiles, cron, BBR)
Karavi.py          → tunnel core engine
install.sh         → one-line installer
```

Installed paths:

| Path | Purpose |
|------|---------|
| `/usr/local/bin/karavi-tunnel` | Manager binary |
| `/opt/karavi/Karavi.py` | Python core |
| `/etc/karavi_manager/profiles/` | Saved slot configs |

---

## Troubleshooting

```bash
screen -ls
ss -lntp
nc -zv IR_IP 7000
sudo karavi-tunnel
```

Attach to a running session from the manager: **Manage tunnel → Logs** (detach with `Ctrl+A` then `D`).

---

## FAQ

**Must Bridge and Sync ports match?**  
Yes — identical values on IR and EU.

**Multiple tunnels?**  
Yes — use different slot numbers.

**Tunnel stopped after reboot?**  
Enable cron health-check (menu 3) or start the slot again manually.

**Manual ports instead of AutoSync?**  
On IR profile creation, answer `n` to AutoSync and enter a CSV list (e.g. `80,443,2083`).

---

## Links

- GitHub: [akaravi/k-n](https://github.com/akaravi/k-n)
- Telegram: [@IlyaahD](https://t.me/IlyaahD)
