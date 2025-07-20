# 📘 OSSEC HIDS v3.7.0 Installation Guide

Panduan ini mencakup instalasi **OSSEC Server** dan **Agent**, baik secara manual di sistem operasi maupun menggunakan container Docker.

---

## 📌 Prasyarat Sistem

- **OS:** Ubuntu 20.04 / 22.04 (Server & Agent)
- **Koneksi Internet:** Diperlukan
- **Akses:** Root atau sudo
- **Port yang Dibuka:** `1514/udp`, `1515/tcp`

---

## 🛡️ Instalasi OSSEC Server (Manual)

### 1. Update Sistem

```bash
sudo apt update
sudo apt upgrade -y
```

### 2. Instalasi Dependency

```bash
sudo apt install -y php php-cli php-common libapache2-mod-php apache2-utils sendmail \
inotify-tools apache2 build-essential gcc make wget tar zlib1g-dev libpcre2-dev \
libpcre3-dev unzip libz-dev libssl-dev libevent-dev libsystemd-dev
```

### 3. Setup Apache

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

## 🌐 Setup OSSEC Web UI (WUI)

### 1. Salin dan Ekstrak File WUI

> Salin file `ossec-wui.tar.gz` ke `/tmp` menggunakan WinSCP atau metode lain.

```bash
sudo tar -xvzf ossec-wui.tar.gz
sudo mv ossec-wui /var/www/html
```

### 2. Atur Izin Akses

```bash
cd /var/www/html/ossec-wui
sudo chown -R www-data:www-data .
sudo chmod -R 755 .
sudo usermod -a -G ossec www-data
```

### 3. Menjalankan OSSEC Server

```bash
cd /var/ossec/bin/
./ossec-control start
sudo systemctl restart apache2
```

---

## 🐳 Instalasi & Setup Docker (Sample Container: Nginx)

### 1. Instalasi Docker di Ubuntu

#### a. Update Sistem

```bash
sudo apt update
sudo apt upgrade -y
```

#### b. Install Dependency

```bash
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common lsb-release gnupg
```

#### c. Tambahkan GPG Key Docker

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

#### d. Tambahkan Repository Docker

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

#### f. Cek Versi Docker

```bash
docker --version
```

#### g. (Opsional) Tambahkan user ke grup docker

```bash
sudo usermod -aG docker $USER
newgrp docker
```

---

## 🚀 Menjalankan Container Contoh (Nginx)

### 1. Jalankan Container Nginx

```bash
docker run -d --name web-nginx -p 8080:80 nginx
```

### 2. Akses Web Nginx

Buka browser dan akses: [http://localhost:8080](http://localhost:8080)

### 3. Cek Status Container

```bash
docker ps
```

### 4. Masuk ke dalam Container

```bash
docker exec -it <nama_container> bash
```

---

## 💻 Instalasi OSSEC Agent

### 1. Update Sistem pada Container

```bash
apt update
apt upgrade -y
```

### 2. Instalasi Dependency

```bash
sudo apt install -y build-essential make zlib1g-dev libpcre2-dev libevent-dev libssl-dev libsystemd-dev
```

### 3. Download & Install OSSEC Agent (Pilih "agent" saat instalasi)

```bash
cd /tmp
wget https://github.com/ossec/ossec-hids/archive/3.7.0.tar.gz
tar -xvzf 3.7.0.tar.gz
cd ossec-hids-3.7.0
./install.sh
```

### 4. Registrasi Agen ke Server

```bash
sudo su
cd /var/ossec/bin/
./manage_agents    # Masukkan key dari server
./ossec-control restart
```

---

## 📑 Referensi

- [OSSEC Documentation](https://www.ossec.net/docs/)
- [OSSEC GitHub](https://github.com/ossec/ossec-hids)
- [Docker Documentation](https://docs.docker.com/)

---

> **Catatan:**  
> Pastikan untuk menyesuaikan konfigurasi sesuai kebutuhan keamanan dan arsitektur sistem Anda.
