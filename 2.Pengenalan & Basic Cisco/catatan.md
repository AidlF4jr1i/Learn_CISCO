
# Cisco Certification & Configuration Guide

---

### **Level Sertifikasi CISCO**
📷 [Link Gambar Sertifikasi](https://drive.google.com/open?id=11MH6-U6q_fek45HRZRksQWRQCyBHNNop&usp=drive_fs)

---

## A. Remote Cisco Real Device

📷 [Gambar Ilustrasi](https://drive.google.com/open?id=1053IcDyeJYAMRVpmJM3jhOdFvbuQVWCw&usp=drive_fs)

Untuk melakukan remote perangkat Cisco secara fisik (real device), kita memerlukan:

1. **Kabel Console** – konektor RJ45 ke port console Cisco, dan ujung lainnya DB-9 (serial).
2. **Converter Serial to USB** – digunakan jika laptop tidak memiliki port serial.
3. **Perangkat Cisco** – seperti Router atau Switch.
4. **Aplikasi Terminal Emulator** – misalnya PuTTY, Tera Term, atau SecureCRT.

### Langkah Remote:
1. Sambungkan kabel console (RJ45 ke perangkat Cisco, serial ke laptop via converter jika perlu).
2. Cek **Device Manager** untuk melihat port COM yang terdeteksi (misalnya COM3).
3. Buka aplikasi PuTTY, pilih koneksi **Serial**, isi "Serial line" dengan COM yang ditemukan, biarkan **Speed = 9600**, lalu klik **Open**.

> **Perbedaan Router vs Switch:**
> - **Router**: Menghubungkan antar jaringan (subnet berbeda), memiliki port WAN (Serial, FastEthernet, GigabitEthernet).
> - **Switch**: Menghubungkan banyak perangkat dalam satu jaringan lokal (subnet sama), memiliki banyak port Ethernet (misal 24/48 port).
> - Keduanya memiliki satu port console untuk manajemen.

---

## B. Remote Cisco pada Packet Tracer

📷 [Gambar Simulasi](https://drive.google.com/open?id=1EDqu16P1l40Oq-6C2yx0zAz0jfRoD8Om&usp=drive_fs)

Simulasi remote di Packet Tracer hampir sama dengan perangkat fisik:

1. Hubungkan PC/Laptop ke perangkat Cisco menggunakan **Console Cable**.
2. Klik pada PC > tab **Desktop** > **Terminal**.
3. Biarkan pengaturan default > klik **OK**, langsung masuk ke CLI perangkat.

---

## C. Perintah Dasar Cisco

CLI Cisco memiliki beberapa level mode:

### 1. User EXEC Mode (`>`)
- Mode awal setelah login.
- Akses terbatas, hanya untuk melihat informasi dasar.

### 2. Privileged EXEC Mode (`#`)
- Masuk dengan perintah `enable` atau `en`.
- Akses penuh untuk perintah `show`, `ping`, `traceroute`, dan menyimpan konfigurasi.

### 3. Global Configuration Mode (`(config)#`)
- Masuk dengan `configure terminal` atau `conf t`.
- Untuk konfigurasi global (hostname, password, IP, dll).

### Navigasi Mode:
- `exit`: Keluar dari mode saat ini.
- `end`: Kembali ke Privileged Mode langsung.
- `disable`: Keluar ke User Mode.

### Keamanan Akses:
- `enable password <password>`: Password biasa (terlihat jelas di konfigurasi).
- `enable secret <password>`: Lebih aman, dienkripsi MD5.
- `service password-encryption`: Mengenkripsi password biasa (tapi lemah).

### Perintah Umum:
- `enable` / `en`: Masuk ke Privileged Mode.
- `configure terminal` / `conf t`: Masuk ke Global Config.
- `exit`: Keluar dari mode.
- `hostname <nama>`: Ubah hostname perangkat.
- `show running-config` / `sh run`: Lihat konfigurasi aktif.
- `do <perintah>`: Jalankan perintah dari Privileged Mode saat di Global Config.
- `no <perintah>`: Menghapus konfigurasi.

> **Tips CLI:**
> - Gunakan **Tab** untuk auto-complete.
> - Gunakan tanda tanya (**?**) untuk melihat opsi yang tersedia.

---

## D. Konfigurasi IP Address pada Cisco Router

### Langkah:
1. Masuk ke konfigurasi interface:
    ```
    interface FastEthernet0/0
    atau
    int fa0/0
    ```
2. Aktifkan interface:
    ```
    no shutdown
    ```
3. Berikan IP address:
    ```
    ip address 192.168.1.1 255.255.255.0
    ```
4. Lihat hasil konfigurasi:
    ```
    do show ip interface brief
    atau
    do sh ip int br
    ```

Setelah router dikonfigurasi, jangan lupa atur IP dan **Default Gateway** pada klien (PC/Laptop).

### Topologi Latihan:
📷 [Gambar Topologi](https://drive.google.com/open?id=1-xjBmEO2yv8M9Vvrt21fZEkqHYpGyx2w&usp=drive_fs)

- **1 Router, 1 Switch, 1 Laptop, 2 PC**
- **Router ↔ Laptop**: `192.168.2.1/24`
- **Router ↔ PC via Switch**: `192.168.1.1/24`

---

## Tambahan

1. **Perintah `end`**  
   Untuk langsung kembali ke Privileged EXEC Mode dari mode konfigurasi manapun.

2. **Menyimpan Konfigurasi**  
   Konfigurasi akan hilang jika tidak disimpan. Untuk menyimpan:
   ```
   copy running-config startup-config
   atau
   copy run start
   ```
   Alternatif:
   ```
   write memory
   atau
   wr
   ```
   Dari Global Config, gunakan:
   ```
   do write
   ```

---

Selamat berlatih dan semoga sukses jadi Network Engineer handal! 💻🌐🔧
