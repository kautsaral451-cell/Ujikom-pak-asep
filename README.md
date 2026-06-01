# Ujikom-pak-asep
contekan
# Praktik Administrasi Server Debian & Proxmox

## Informasi Login Proxmox

* URL Proxmox: `https://172.16.51.199:8006`
* Username: `242510226`
* Password: `Siswa@2026!`
* Realm: `Proxmox VE`

### Ganti Password (Opsional)

```
Datacenter
└── Permissions
    └── Users
        └── Pilih User
            └── Change Password
```

---

# 1. Membuat Container (CT) di Proxmox

## Konfigurasi Container

```
CT ID      : 110115
Hostname   : kautsar
Password   : admin
Resource   : kautsar
Template   : Debian Custom for Kahiang
Storage    : local
Disk       : local-lvm
CPU        : 1 Core
RAM        : 512 MB

IPv4/CIDR  : 172.16.51.115/24
Gateway    : 172.16.51.1

✓ Start after created
```

Setelah selesai:

```
Klik Finish
Tunggu status OK
Klik kanan CT
Start
Console
```

---

# 2. Install Webmin

## Membuat User

```bash
adduser kautsar
```

## Login SSH dari Windows

```bash
ssh kautsar@172.16.51.115
```

## Jika ssh eror
```bash
ssh-keygen -R 172.16.51.199
```

## Menjadi Root

```bash
su -
```

Password:

```text
admin
```

## Update Sistem

```bash
apt update
apt upgrade 
```

## Download Webmin

Akses server repository:

```
http://172.16.51.199
```

Copy link Webmin lalu jalankan:

```bash
wget LINK_WEBMIN
```

## Install Webmin

```bash
dpkg -i webmi tab
```
## Jika install webmin eror
```bash
apt install -f 
```

## Akses Webmin

```
https://172.16.51.115:10000
```

Login:

```text
Username : root
Password : admin
```

---

# 3. Install Web Server

## Install Apache, MariaDB dan PHP

```bash
apt install apache2 mariadb-server php-fpm -y
```
## Install mysql
```bash
apt install php-mysql -y
```

## Aktifkan PHP-FPM

```bash
a2enmod proxy_fcgi
a2enconf php8.4-fpm
systemctl reload apache2
```

## Membuat File PHP Info

Masuk ke:

```
Webmin
└── Tools
    └── File Manager
```

Buka:

```
/var/www/html
```

Rename:

```
html -> _html
```

Buat file:

```
info.php
```

Isi:

```php
<?php
phpinfo();
?>
```

---

# 4. SSL HTTPS Self Signed

## Aktifkan SSL Apache

```bash
a2enmod ssl
systemctl reload apache2
```

## Membuat Sertifikat

```bash
mkdir ssl
cd ssl
```

```bash
openssl req -x509 -nodes -days 90 -newkey rsa:2048 -keyout self.key -out self.crt
```

Isi data:

```text
Country Name            : Indonesia
State/Province          : Jawa Barat
Locality                : Kabupaten Bandung
Organization            : SMKN 1 Soreang
Organizational Unit     : TJKT
Common Name             : kautsar
Email Address           : rahasia
```

## Edit Konfigurasi SSL

```
webmin
└──tools
    └──file manager
```
Masuk ke:

```text
/etc/apache2/sites-enabled/default-ssl.conf
```

Cari:

```apache
SSLCertificateFile
SSLCertificateKeyFile
```

Ganti menjadi:

```apache
SSLCertificateFile      /root/ssl/self.crt
SSLCertificateKeyFile   /root/ssl/self.key
```

## Aktifkan SSL Site

```bash
a2ensite default-ssl.conf
systemctl reload apache2
```


---

# 5. Install Prometheus

## Login SSH

```bash
ssh kautsar@172.16.51.115
```

## Menjadi Root

```bash
su -
```

## Install Prometheus

```bash
apt install prometheus prometheus-node-exporter -y
```

## Akses Prometheus

```
http://172.16.51.115:9090
```

Cek:

```
Status
└── Targets
```

---

# 6. Install Grafana

## Download Grafana

Akses repository:

```
http://172.16.51.199
```

Copy link Grafana lalu:

```bash
wget LINK_GRAFANA
```

## Install

```bash
dpkg -i grafana*.deb
```

## Jalankan Service

```bash
systemctl enable grafana-server
systemctl restart grafana-server
```

## Akses Grafana

```
http://172.16.51.115:3000
```

Login:

```text
Username : admin
Password : admin
```

## Tambahkan Prometheus

```
Menu
└── Connections
    └── Add New Connection
        └── Prometheus
```

Isi:

```text
Name : prometheus -kautsar

URL  : http://172.16.51.115:9090
```

Klik:

```text
Save & Test
```

## Import Dashboard Node Exporter

```
Dashboard
└── New
    └── Import
```

ID Dashboard:

```text
1860
```

Klik:

```text
Load
Import
```

# 9. Install WordPress (Bonus)

## Buat Database

Nama database:

```text
wp_kautsar
```

## Buat User Database

```text
Username : kautsar
Password : kautsar
```
ATAu
```text
Change administrator
pw : admin
```

Berikan seluruh privilege.

## Upload WordPress

Upload file:

```text
wordpress-6.9.x-id_ID.zip
```

Lokasi:

```text
/var/www/html
```

## Extract

```text
Klik kanan
Extract
Yes, Overwrite
```

Rename folder sesuai nama masing-masing.

Contoh:

```text
kautsar
```

## Akses WordPress

```text
http://172.16.51.115/kautsar
```

Masukkan informasi database.

## Buat File Konfigurasi di folder wordpress yang sudah di rename jadi kautsar

File:

```text
wp-config.php
```

Lokasi:

```text
/var/www/html/kautsar/
```

Paste konfigurasi yang diberikan WordPress.

## Instalasi

Klik:

```text
Run Installation
```

Isi:

```text
Site Title
Username
Password
```

Selesai.

Login:

```text
http://172.16.51.115/kautsar/wp-admin
```

---

## Upload WEB Bu Vidya

## 1. Membuat Database

Masuk ke:

```text
Webmin
└── Servers
    └── MySQL Database Server
```

Buat database baru sesuai nama website masing-masing.

Contoh:

```text
Database : db_kautsar
```

Klik:

```text
Create Database
```

---

## 2. Import Database

Pilih database yang sudah dibuat.

Klik:

```text
Execute SQL
```

Lalu import file database:

```text
database.sql
```

Jalankan:

```text
Run Database
```

Pastikan seluruh tabel berhasil dibuat.

---

## 3. Menyiapkan File Website

Pastikan seluruh source code website berada dalam satu folder.

Contoh:

```text
kautsar_project
```

Kemudian jadikan ZIP terlebih dahulu.

Contoh:

```text
kautsar_project.zip
```

---

## 4. Upload Website

Masuk ke:

```text
Webmin
└── Tools
    └── File Manager
```

Masuk ke folder:

```text
/var/www/html
```

Upload file:

```text
kautsar_project.zip
```

---

## 5. Extract File

Setelah upload selesai:

```text
Klik kanan file ZIP
└── Extract
    └── Yes, Overwrite
```

Tunggu proses ekstraksi selesai.

---

## 6. Rename Folder

Rename folder hasil ekstraksi menjadi:

```text
kautsar
```

Sehingga struktur folder menjadi:

```text
/var/www/html/kautsar
```

---

## 7. Edit Koneksi Database

Cari file konfigurasi database.

Contoh:

```php
$conn = mysqli_connect(
    "localhost",
    "kautsar",
    "admin",
    "db_kautsar"
);
```

Sesuaikan:

```text
Host      : localhost
Username  : user database
Password  : password database
Database  : nama database
```

Contoh:

```text
Username : kautsar
Password : admin
Database : db_kautsar
```

Simpan perubahan.

---

## 8. Akses Website

Buka browser:

```text
http://172.16.51.115/kautsar
```

Jika halaman website muncul dengan normal, maka proses upload website berhasil.

