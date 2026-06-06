# Stratum Mining Proxy

A lightweight TCP proxy server for forwarding Stratum mining connections from a local port to a remote mining pool. Useful for hiding the actual pool address or routing miner traffic through a VPS.

---

## How It Works

```
[Miner] ──► [VPS:LOCAL_PORT] ──► [Mining Pool:REMOTE_PORT]
```

The proxy transparently forwards all TCP traffic between miners and the remote pool, acting as a middle relay.

---

## Requirements

- Ubuntu 20.04 / 22.04 / 24.04 (or any systemd-based distro)
- Node.js v18+ (LTS recommended)
- npm

---

## Installation on Ubuntu VPS

### 1. Install Node.js

```bash
curl -fsSL https://deb.nodesource.com/setup_lts.x | bash -
apt-get install -y nodejs
```

Verify:

```bash
node -v
npm -v
```

### 2. Clone the Repository

```bash
git clone https://IKSANNURPADILLAH/trw.git /root/trw-main
cd /root/trw-main
```

### 3. Install Dependencies

```bash
npm install
```

### 4. Configure Environment Variables

Copy the example env file and edit it:

```bash
cp .env.example .env
nano .env
```

Fill in your values:

```env
REMOTE_HOST=your-pool.example.com
REMOTE_PORT=1155
REMOTE_PASSWORD=x
LOCAL_HOST=0.0.0.0
LOCAL_PORT=80
```

| Variable          | Description                              |
|-------------------|------------------------------------------|
| `REMOTE_HOST`     | Mining pool hostname or IP               |
| `REMOTE_PORT`     | Mining pool port                         |
| `REMOTE_PASSWORD` | Pool password (usually `x`)              |
| `LOCAL_HOST`      | Interface to listen on (`0.0.0.0` = all) |
| `LOCAL_PORT`      | Port miners will connect to on your VPS  |

### 5. Open Firewall Port

```bash
ufw allow <LOCAL_PORT>/tcp
ufw reload
```

---

## Running as a systemd Service (Recommended)

Running as a service ensures the proxy **automatically starts on boot** and **restarts on crash**.

### Install the Service

```bash
cp trw-proxy.service /etc/systemd/system/
systemctl daemon-reload
systemctl enable trw-proxy
systemctl start trw-proxy
```

### Check Status

```bash
systemctl status trw-proxy
```

### Useful Commands

| Command                                  | Description              |
|------------------------------------------|--------------------------|
| `systemctl start trw-proxy`              | Start the proxy          |
| `systemctl stop trw-proxy`               | Stop the proxy           |
| `systemctl restart trw-proxy`            | Restart the proxy        |
| `journalctl -u trw-proxy -f`             | View live logs           |
| `journalctl -u trw-proxy -n 50`          | View last 50 log entries |

---

## Running Manually (without service)

```bash
node server.js
```

---

## Miner Configuration

Point your miner to the VPS instead of the pool directly:

```
Pool URL  : stratum+tcp://YOUR_VPS_IP:LOCAL_PORT
Password  : x
```

---

## License

ISC
