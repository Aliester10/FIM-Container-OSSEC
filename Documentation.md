# 📘 OSSEC HIDS v3.7.0 Installation Guide

This guide covers the installation of **OSSEC Server** and **Agent**, either manually on the operating system or using Docker containers.

---

## 📌 System Requirements

- **OS:** Ubuntu 20.04 / 22.04 (Server & Agent)
- **Internet Connection:** Required
- **Access:** Root or sudo privileges
- **Open Ports:** `1514/udp`, `1515/tcp`

---

## 🛡️ Manual Installation of OSSEC Server

### 1. Update the System

```bash
sudo apt update
sudo apt upgrade -y
```

### 2. Install Dependencies

```bash
sudo apt install -y php php-cli php-common libapache2-mod-php apache2-utils sendmail \
inotify-tools apache2 build-essential gcc make wget tar zlib1g-dev libpcre2-dev \
libpcre3-dev unzip libz-dev libssl-dev libevent-dev libsystemd-dev
```

### 3. Set Up Apache

```bash
sudo systemctl enable apache2
sudo systemctl start apache2
sudo a2enmod rewrite
sudo systemctl restart apache2
```

### 4. Download & Install OSSEC

```bash
cd /tmp
wget https://github.com/ossec/ossec-hids/archive/3.7.0.tar.gz
sudo tar -xvzf 3.7.0.tar.gz
cd ossec-hids-3.7.0
./install.sh
```

---

## 🌐 OSSEC Web UI (WUI) Setup

### 1. Copy and Extract the WUI Files

> Copy the `ossec-wui.tar.gz` file to `/tmp` using WinSCP or another method.

```bash
sudo tar -xvzf ossec-wui.tar.gz
sudo mv ossec-wui /var/www/html
```

### 2. Set Permissions

```bash
cd /var/www/html/ossec-wui
sudo chown -R www-data:www-data .
sudo chmod -R 755 .
sudo usermod -a -G ossec www-data
```

### 3. Start the OSSEC Server

```bash
cd /var/ossec/bin/
./ossec-control start
sudo systemctl restart apache2
```

---

## 🐳 Docker Installation & Nginx Sample Container Setup

### 1. Install Docker on Ubuntu

#### a. Update the System

```bash
sudo apt update
sudo apt upgrade -y
```

#### b. Install Docker Dependencies

```bash
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common lsb-release gnupg
```

#### c. Add Docker GPG Key

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

#### d. Add Docker Repository

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

#### e. Install Docker Engine

```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io
```

#### f. Check Docker Version

```bash
docker --version
```

#### g. (Optional) Add user to the docker group

```bash
sudo usermod -aG docker $USER
newgrp docker
```

---

## 🚀 Run Sample Container (Nginx)

### 1. Start Nginx Container

```bash
docker run -d --name web-nginx -p 8080:80 nginx
```

### 2. Access Nginx Web

Open your browser and visit: [http://localhost:8080](http://localhost:8080)

### 3. Check Container Status

```bash
docker ps
```

### 4. Enter the Container

```bash
docker exec -it <container_name> bash
```

---

## 💻 OSSEC Agent Installation

### 1. Update the System inside the Container

```bash
apt update
apt upgrade -y
```

### 2. Install Dependencies

```bash
sudo apt install -y build-essential make zlib1g-dev libpcre2-dev libevent-dev libssl-dev libsystemd-dev
```

### 3. Download & Install OSSEC Agent (choose "agent" during installation)

```bash
cd /tmp
wget https://github.com/ossec/ossec-hids/archive/3.7.0.tar.gz
tar -xvzf 3.7.0.tar.gz
cd ossec-hids-3.7.0
./install.sh
```

### 4. Register Agent to Server

```bash
sudo su
cd /var/ossec/bin/
./manage_agents    # Enter the key from the server
./ossec-control restart
```

---

# 📧 OSSEC Email Notification Configuration

OSSEC has a built-in feature to send alerts via email. Below is the procedure to set up email notifications from the OSSEC Server.

### 📝 1. Edit the `ossec.conf` Configuration File

Open the OSSEC configuration file:

```bash
sudo nano /var/ossec/etc/ossec.conf
```

#### Add or modify the following sections inside the `<ossec_config>` tag:

```xml
<global>
  <email_notification>yes</email_notification>
  <email_to>tugasakhir20824@gmail.com</email_to>
  <email_from>ossecm@faberttt</email_from>
  <smtp_server>smtp.gmail.com</smtp_server>
  <email_maxperhour>100</email_maxperhour>
</global>

<alerts>
  <log_alert_level>7</log_alert_level>
  <email_alert_level>7</email_alert_level>
</alerts>
```

---

## ✉️ 2. (Optional) Use HTML Email Format (Cleaner Look)

If you want to change the email format to HTML, follow these steps:

### Add Custom Command in `ossec.conf`:

```xml
<command>
  <name>html-email</name>
  <executable>mail-html.sh</executable>
  <expect>email</expect>
</command>
```

### Add to rules, for example in `local_rules.xml`:

```xml
<rule id="100001" level="10">
  <decoded_as>syslog</decoded_as>
  <options>alert_by_email</options>
  <command>html-email</command>
</rule>
```

### Ensure the `mail-html.sh` file exists in `/var/ossec/active-response/bin` and is executable:

```bash
chmod +x /var/ossec/active-response/bin/mail-html.sh
```

### Restart OSSEC

```bash
sudo /var/ossec/bin/ossec-control restart
```

---

## 🧪 4. Test Email Delivery

### Check OSSEC Logs

```bash
tail -f /var/ossec/logs/ossec.log
```

---

## 📑 References

- [OSSEC Documentation](https://www.ossec.net/docs/)
- [OSSEC GitHub](https://github.com/ossec/ossec-hids)
- [Docker Documentation](https://docs.docker.com/)

---

> **Note:**  
> Make sure to adjust the configuration according to your system architecture and security requirements.

---
