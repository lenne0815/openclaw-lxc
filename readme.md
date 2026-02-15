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


The error message **"Don't run this as root!"** is happening because Homebrew explicitly forbids being installed by the `root` user for security reasons.

Since you are logged in as `root` (likely on a VPS or container), **you should stop trying to use Homebrew.** It is much faster and cleaner to use Debian's native package manager (`apt`) instead.

Here are your two best options to fix this:

### Option 1: The "Native" Fix (Recommended)

**Forget about Homebrew.** Since you are already `root`, you can install everything OpenClaw needs directly using `apt`. This avoids the error entirely.

Run these 3 commands in order:

1. **Install Git and Tools:**
```bash
apt update && apt install -y git curl build-essential

```


2. **Install Node.js 22 (Required for OpenClaw):**
```bash
curl -fsSL https://deb.nodesource.com/setup_22.x | bash -
apt install -y nodejs

```


3. **Install OpenClaw:**
Now that Node is installed, the OpenClaw installer won't look for `brew`:
```bash
npm install -g openclaw
# OR if you are using the install script:
curl -fsSL https://openclaw.ai/install.sh | bash

```



---

### Option 2: The "I Must Use Brew" Fix

If you absolutely require Homebrew for some specific reason, you must create a new "regular" user to run it.

1. **Create a new user (e.g., "clawuser"):**
```bash
adduser clawuser

```


2. **Give the user permission to use sudo:**
```bash
usermod -aG sudo clawuser

```


3. **Switch to that user:**
```bash
su - clawuser

```


4. **Run the Brew command again:**
Now that your prompt is `clawuser@Openclaw`, the command will work:
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

```



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
