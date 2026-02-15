install debian 13 lxc 20gb/4gb/2core unprivilegded


install sudo
  apt-get update
  apt-get install -y sudo
install curl
  sudo apt update
  sudo apt install curl
install systemd
  apt-get update
  apt-get install systemd systemd-sysv
install ssh / configure making root login possible
  sudo apt update
  sudo apt install openssh-server
  sudo nano /etc/ssh/sshd_config
    PermitRootLogin yes
  sudo systemctl start ssh
  sudo systemctl enable ssh
use console on your computer to access 


curl -fsSL https://openclaw.ai/install.sh | bash

nano /etc/systemd/system/openclaw.service

[Unit]
Description=OpenClaw Gateway Service
After=network.target

[Service]
# Since you are likely root in this container, we run as root.
# If you created a specific user, change 'root' to that username.
User=root
Group=root

# The command to start the gateway
ExecStart=/usr/bin/openclaw gateway

# Restart automatically if it crashes
Restart=always
RestartSec=5

# Output logs to systemd journal
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target

Here is a clean, GitHub-ready Markdown snippet. You can copy and paste this directly into a `README.md` or a Gist.

I have consolidated the repeated `apt update` commands and grouped the installations for efficiency.

---

# OpenClaw Gateway Setup (LXC)

This guide details the setup of an **Unprivileged LXC Container** running **Debian 13 (Trixie)** to host the OpenClaw Gateway.

## 1. Container Specifications

Create a new LXC container with the following minimum specs:

* **OS:** Debian 13
* **Type:** Unprivileged
* **Disk:** 20 GB
* **RAM:** 4 GB
* **Cores:** 2 CPU Cores
* **Network:** DHCP or Static IP (Ensure internet access)

> **Note:** Ensure your container has **Nesting** enabled in "Options" -> "Features" (Proxmox/LXC) to allow systemd services to run correctly.

## 2. Initial Configuration

Open the container console and run the following commands as `root`.

### Update & Install Dependencies

Install `sudo`, `curl`, `openssh-server`, and ensure `systemd` is fully initialized.

```bash
apt-get update && apt-get upgrade -y
apt-get install -y sudo curl openssh-server systemd systemd-sysv

```

### Configure SSH (Optional)

Allow root login over SSH (useful if you are not creating a separate user account).

1. Edit the SSH config:
```bash
nano /etc/ssh/sshd_config

```


2. Find and change the line `PermitRootLogin` to:
```ini
PermitRootLogin yes

```


3. Restart the SSH service:
```bash
systemctl enable ssh
systemctl restart ssh

```



## 3. Install OpenClaw

Now that the environment is prepared, access the container via your local terminal (SSH) or continue in the console to run the installer.

```bash
curl -fsSL https://openclaw.ai/install.sh | bash

```

### Troubleshooting

If the installer complains about **systemd user services** or permission errors:

1. Ensure the container is running with **Nesting** enabled.
2. Try running the gateway manually in the foreground if the service fails to start:
```bash
/usr/local/bin/openclaw gateway --foreground

```
