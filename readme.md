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
