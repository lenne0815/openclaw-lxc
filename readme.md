# OpenClaw Installation Guide: Debian 13 (LXC)

This guide covers the installation of OpenClaw on a **Debian 13 (Trixie)** unprivileged LXC container.

## 🖥️ System Requirements

| Resource | Specification |
| --- | --- |
| **OS** | Debian 13 (Unprivileged LXC) |
| **Disk** | 20 GB |
| **RAM** | 4 GB |
| **CPU** | 2 Cores |

---

## 🛠️ Step 1: Initial System Prep

Run these commands as `root` to prepare the environment.

```bash
# Update and install core utilities
apt-get update && apt-get upgrade -y
apt-get install -y sudo curl systemd systemd-sysv openssh-server

```

### Configure SSH (Root Access)

If you need to access the container via SSH as root:

1. Open the config: `nano /etc/ssh/sshd_config`
2. Set `PermitRootLogin yes`
3. Restart the service:

```bash
systemctl enable ssh
systemctl start ssh

```

---

## 🚀 Step 2: Install OpenClaw (Native Method)

Since Homebrew is not recommended for `root` users, use the native `apt` and `npm` route.

### 1. Install Node.js 22

```bash
curl -fsSL https://deb.nodesource.com/setup_22.x | bash -
apt-get install -y nodejs build-essential git

```

### 2. Run Installer

```bash
curl -fsSL https://openclaw.ai/install.sh | bash

```

---

## ⚙️ Step 3: Configure Systemd Service

To ensure OpenClaw runs in the background and starts on boot, create a service file.

**Create the file:**
`nano /etc/systemd/system/openclaw.service`

**Paste the following configuration:**

```ini
[Unit]
Description=OpenClaw Gateway Service
After=network.target

[Service]
# Running as root for container simplicity
User=root
Group=root
ExecStart=/usr/bin/openclaw gateway
Restart=always
RestartSec=5
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target

```

**Enable and Start:**

```bash
systemctl daemon-reload
systemctl enable openclaw
systemctl start openclaw

```
**Would you like me to generate a shell script that automates this entire process from start to finish?**
