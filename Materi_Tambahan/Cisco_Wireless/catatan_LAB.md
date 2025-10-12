# LAB 1: Setup Jaringan Wireless Dasar

## Pendahuluan 📝

Setelah sebelumnya kita banyak membahas dasar teori dari jaringan nirkabel (wireless), pada sesi ini kita akan memulai praktik dengan **Cisco Packet Tracer**. Untuk lab pertama, kita akan membangun sebuah jaringan wireless sederhana menggunakan satu unit **Wireless Home Router (WHR)** dan beberapa *end-device* seperti Laptop dan PC.

Tujuan dari lab ini adalah untuk memahami proses konfigurasi dasar sebuah router wireless, menghubungkan perangkat ke jaringan, dan melakukan verifikasi konektivitas.

---

## 1. Desain Topologi Jaringan 🌐

Langkah pertama adalah mendesain topologi jaringan sesuai dengan panduan pada gambar di tautan berikut. Susunlah satu Wireless Home Router dan beberapa end-device (misalnya, 1 Laptop dan 1 PC) pada lembar kerja Cisco Packet Tracer.

* **Topologi:** [Lihat Desain Topologi Jaringan](https://drive.google.com/open?id=1zM8EFYs53uehDT9Thb2jRb113M3boIrN&usp=drive_fs)



---

## 2. Konfigurasi Wireless Home Router (WHR) ⚙️

Setelah topologi selesai dibuat, kita akan melanjutkan ke tahap konfigurasi router. Cukup klik dua kali pada perangkat WHR, lalu masuk ke tab **'GUI' (Graphical User Interface)**.

### a. Pengaturan IP Address & DHCP Server

Di dalam menu GUI, Anda akan melihat halaman pengaturan dasar. Di sini kita akan mengatur alamat IP router dan rentang IP yang akan didistribusikan oleh DHCP Server ke perangkat klien.

* **IP Address:** Alamat IP unik untuk router itu sendiri, yang juga berfungsi sebagai *gateway* bagi perangkat lain di jaringan.
* **DHCP Server:** Layanan yang secara otomatis memberikan alamat IP kepada setiap perangkat yang terhubung ke jaringan, sehingga Anda tidak perlu mengaturnya secara manual.

Atur konfigurasi sesuai dengan panduan pada gambar berikut:
* **Gambar Konfigurasi IP:** [Lihat Pengaturan IP Address & DHCP](https://drive.google.com/open?id=1oBAGML_Pio_NAX_fWxvMvL8zI8iQyeuK&usp=drive_fs)

### b. Pengaturan Jaringan Wireless

Selanjutnya, navigasikan ke menu **'Wireless'** dan **'Wireless Security'**. Pada kedua bagian ini, kita akan mengonfigurasi nama jaringan, kanal, dan keamanannya.

* **Network Name (SSID):** Ini adalah nama jaringan Wi-Fi Anda yang akan terlihat oleh perangkat lain. Anda bisa menyesuaikannya sesuai keinginan (contoh: `HOME_WIFI`).
* **Channel:** Biarkan pada pengaturan 'Auto' agar router dapat memilih kanal frekuensi yang paling sedikit interferensinya secara otomatis.
* **Security Mode:** Pilih mode keamanan (misalnya, `WPA2 Personal`) untuk melindungi jaringan Anda.
* **Passphrase:** Buat kata sandi yang kuat untuk jaringan nirkabel Anda.

Sesuaikan konfigurasi Anda seperti pada contoh di gambar ini:
* **Gambar Konfigurasi Wireless:** [Lihat Pengaturan Wireless](https://drive.google.com/open?id=1uKFLLYbz3KH0ybYGOq2XaXkEWIyvnsHl&usp=drive_fs)

* **Gambar Konfigurasi Wireless Security:** [Lihat Pengaturan Security](https://drive.google.com/open?id=1Lc9Ibc_ETy5bEDvterxAh9EW_vxnjXlH&usp=drive_fs)

Setelah selesai, jangan lupa untuk menyimpan pengaturan (*Save Settings*).

---

## 3. Konfigurasi End-Device 💻

Langkah terakhir adalah menghubungkan setiap end-device ke jaringan wireless yang baru saja kita buat.

### a. Mengganti Interface Jaringan

Secara default, PC atau Laptop di Packet Tracer menggunakan interface LAN (kabel). Kita perlu menggantinya dengan interface wireless.
1.  Klik pada perangkat (misalnya PC).
2.  Masuk ke tab **'Physical'**.
3.  **Matikan daya** perangkat dengan menekan tombol power.
4.  Lepas modul interface LAN dengan cara menariknya keluar.
5.  Pasang modul interface wireless (misalnya, **WPC300N**) ke slot yang kosong.
6.  **Nyalakan kembali** perangkat.

Untuk lebih jelasnya, Anda bisa mengikuti contoh pada GIF berikut:
* **Contoh Penggantian Interface:** [Lihat GIF Tutorial](https://tinyurl.com/29w4b45h)

### b. Menghubungkan ke Jaringan Wireless

Setelah interface diganti, hubungkan perangkat ke jaringan:
1.  Pada perangkat yang sama, buka tab **'Desktop'**.
2.  Pilih aplikasi **'PC Wireless'**.
3.  Klik tab **'Connect'**.
4.  Tunggu beberapa sat, lalu klik **'Refresh'**. Anda akan melihat daftar jaringan yang tersedia.
5.  Pilih SSID yang sudah Anda buat sebelumnya, lalu klik **'Connect'**.
6.  Masukkan *passphrase* (kata sandi) yang telah Anda atur di router.

Jika berhasil, koneksi nirkabel antara WHR dan end-device telah terjalin.

---

## 4. Verifikasi Koneksi ✅

Untuk memastikan semua perangkat terhubung dengan benar dan dapat berkomunikasi, kita perlu melakukan verifikasi.

### a. Cek Alamat IP

Pertama, periksa alamat IP yang diterima oleh setiap end-device dari DHCP server.
1.  Buka tab **'Desktop'** pada end-device.
2.  Pilih **'Command Prompt'**.
3.  Ketikkan perintah berikut dan tekan Enter:
    ```bash
    # Perintah untuk melihat konfigurasi IP yang didapat oleh perangkat
    ipconfig
    ```
    Pastikan perangkat mendapatkan alamat IP yang sesuai dengan rentang DHCP yang telah Anda atur di router.

### b. Uji Konektivitas (Ping)

Lakukan tes `ping` dari satu perangkat ke perangkat lainnya untuk memastikan keduanya dapat berkomunikasi.
1.  Catat alamat IP dari PC target (misalnya, `192.168.0.101`).
2.  Buka **'Command Prompt'** di PC sumber (misalnya, Laptop dengan IP `192.168.0.100`).
3.  Ketikkan perintah `ping` diikuti alamat IP tujuan:
    ```bash
    # Contoh melakukan ping dari Laptop ke PC
    ping 192.168.0.101
    ```
Jika Anda menerima balasan (*reply*), artinya koneksi jaringan wireless dasar Anda telah berhasil dikonfigurasi.

* **Hasil Akhir:** [Lihat Contoh Hasil Ping yang Berhasil](https://drive.google.com/open?id=1_ffMDpVjKgD5NZLztyZV2_4DMmvPuPk3&usp=drive_fs)


# LAB 2: Setup Jaringan Wireless Lanjutan

## Panduan Lab Packet Tracer: Konfigurasi WLAN WPA2 Enterprise pada WLC

### Tabel Pengalamatan (Addressing Table)

| Perangkat          | Interface | Alamat IP          |
| :----------------- | :-------- | :----------------- |
| R1                 | G0/0/0.5  | `192.168.5.1/24`   |
| R1                 | G0/0/0.200| `192.168.200.1/24` |
| R1                 | G0/0/1    | `172.31.1.1/24`    |
| SW1                | VLAN 200  | `192.168.200.100/24`|
| LAP-1              | G0        | DHCP               |
| WLC-1              | Management| `192.168.200.254/24`|
| RADIUS/SNMP Server | NIC       | `172.31.1.254/24`  |
| Admin PC           | NIC       | `192.168.200.200/24`|

---

### Tujuan 🎯

- Mengkonfigurasi interface VLAN baru pada WLC.
- Mengkonfigurasi WLAN baru pada WLC.
- Mengkonfigurasi _scope_ DHCP baru pada server DHCP internal WLC.
- Mengkonfigurasi WLC dengan pengaturan SNMP.
- Mengkonfigurasi WLC agar menggunakan server RADIUS untuk autentikasi pengguna WLAN.
- Mengamankan WLAN dengan WPA2-Enterprise.
- Menghubungkan host ke WLAN yang baru.

---

### Latar Belakang / Skenario 🌐

Anda diminta untuk mengkonfigurasi topologi WLC untuk digunakan dalam lingkungan enterprise yang lebih besar. WPA2-PSK tidak cocok untuk jaringan enterprise karena tidak dapat diskalakan dengan baik. Oleh karena itu, topologi ini akan menggunakan server RADIUS dan WPA2-Enterprise untuk autentikasi pengguna. Metode ini memungkinkan pengelolaan akun pengguna secara terpusat, memberikan keamanan yang lebih tinggi, dan mencatat aktivitas pengguna di server.

* **Topologi:** [Lihat Desain Topologi Jaringan](https://drive.google.com/open?id=1A9Wc3VnUcxmpvgny_IrSeyiDaVueATnQ&usp=drive_fs)

---

## Instruksi Konfigurasi 🛠️

### Bagian 1: Membuat WLAN Baru

#### **Langkah 1: Membuat Interface VLAN Baru**
Setiap WLAN memerlukan sebuah _virtual interface_ (disebut juga _dynamic interface_) pada WLC. Interface ini akan ditandai dengan VLAN ID tertentu.

1.  Buka browser dari desktop **Admin PC** dan akses alamat IP WLC (`192.168.200.254`) melalui **HTTPS**.
2.  Login dengan username `admin` dan password `Cisco123`.
3.  Klik menu **Controller** > **Interfaces**.
4.  Klik tombol **New** di pojok kanan atas.
5.  Masukkan **Interface Name**: `WLAN-5` dan **VLAN ID**: `5`. Klik **Apply**.
6.  Pada halaman konfigurasi, atur **Port Number** ke `1`.
7.  Konfigurasikan detail alamat IP sebagai berikut:
    -   **IP Address**: `192.168.5.254`
    -   **Netmask**: `255.255.255.0`
    -   **Gateway**: `192.168.5.1`
    -   **Primary DHCP Server**: `192.168.5.1`
8.  Klik **Apply**, lalu klik **OK** pada pesan peringatan. Terakhir, klik **Save Configuration**.

#### **Langkah 2: Mengkonfigurasi Server RADIUS**
WLC perlu mengetahui alamat server RADIUS untuk autentikasi.

1.  Klik menu **Security** > **AAA** > **RADIUS** > **Authentication**.
2.  Klik tombol **New...** dan masukkan **Server IP Address**: `172.31.1.254`.
3.  Masukkan **Shared Secret**: `Cisco123`. Konfirmasi sekali lagi dan klik **Apply**.

#### **Langkah 3: Membuat WLAN Baru**
1.  Klik menu **WLANs**. Di pojok kanan atas, pilih **Create New** dari dropdown, lalu klik **Go**.
2.  Konfigurasikan detail berikut:
    -   **Profile Name**: `Floor 2 Employees`
    -   **SSID**: `SSID-5`
    -   **ID**: `5`
3.  Klik **Apply**.
4.  Pada halaman konfigurasi WLAN, centang kotak **Enabled** untuk mengaktifkan WLAN ini.
5.  Di bawah **Interface/Interface Group (G)**, pilih interface yang telah dibuat: **WLAN-5**.
6.  Pindah ke tab **Advanced** dan gulir ke bagian **FlexConnect**.
7.  Centang **FlexConnect Local Switching** dan **FlexConnect Local Auth**.
8.  Klik **Apply** untuk menyimpan.

#### **Langkah 4: Mengkonfigurasi Keamanan WLAN**
1.  Pindah ke tab **Security** > **Layer 2**.
    -   **Layer 2 Security**: Pilih `WPA+WPA2`.
    -   Di bagian **WPA+WPA2 Parameters**, centang **WPA2 Policy**.
    -   Di bawah **Authentication Key Management**, pilih `802.1X`.
2.  Pindah ke sub-tab **AAA Servers**.
    -   Di sebelah **Server 1**, pilih alamat IP server RADIUS yang telah dikonfigurasi.
3.  Klik **Apply** untuk menerapkan konfigurasi.

---

### Bagian 2: Mengkonfigurasi DHCP Scope dan SNMP

#### **Langkah 1: Mengkonfigurasi DHCP Scope Internal WLC**
WLC dapat memberikan alamat IP kepada Lightweight AP (LAP) di jaringan manajemen.

1.  Kembali ke menu **Controller** > **Interfaces**.
2.  Klik pada interface `management` dan catat informasinya.
3.  Ubah **Primary DHCP server** menjadi alamat IP management WLC: `192.168.200.254`. Klik **Apply**.
4.  Di menu kiri, perluas **Internal DHCP Server** dan klik **DHCP Scope**.
5.  Klik **New…** dan beri nama scope: `Wired Management`. Klik **Apply**.
6.  Klik pada nama scope yang baru dibuat dan konfigurasikan:
    -   **Pool Start Address**: `192.168.200.240`
    -   **Pool End Address**: `192.168.200.249`
    -   **Status**: `Enabled`
    -   **Network**, **Netmask**, dan **Default Routers**: Isikan sesuai data dari interface `management`.
7.  Klik **Apply**, lalu **Save Configuration**.

#### **Langkah 2: Mengkonfigurasi SNMP**
1.  Klik menu **Management**. Di menu kiri, perluas **SNMP** dan klik **Trap Receivers**.
2.  Klik **New…**.
3.  Masukkan:
    -   **Community Name**: `WLAN_SNMP`
    -   **IP Address**: `172.31.1.254`
4.  Klik **Apply**.

---

### Bagian 3: Menghubungkan Host ke Jaringan

#### **Langkah 1: Mengkonfigurasi Host Klien**
1.  Buka **Wireless Host** dan jalankan aplikasi **PC Wireless**.
2.  Pindah ke tab **Profiles** dan klik **New**. Beri nama profil `WLC NET`.
3.  Pilih jaringan `SSID-5` dan klik **Advanced Setup**.
4.  Pastikan SSID sudah benar, klik **Next**.
5.  Pastikan **DHCP** terpilih, klik **Next**.
6.  Pada **Security**, pilih `WPA2-Enterprise`. Klik **Next**.
7.  Masukkan kredensial:
    -   **Login name**: `user1`
    -   **Password**: `User1Pass`
8.  Klik **Next**, lalu **Save**.
9.  Pilih profil **WLC NET** dan klik **Connect to Network**.

#### **Langkah 2: Menguji Konektivitas**
1.  Buka **Command Prompt** pada Wireless Host.
2.  Jalankan `ipconfig` untuk memeriksa alamat IP yang diterima.
3.  Lakukan `ping` ke **default gateway** (`192.168.5.1`), **SW1** (`192.168.200.100`), dan **RADIUS server** (`172.31.1.254`) untuk memastikan konektivitas penuh.

---

### Pertanyaan Refleksi 🤔

1.  **Server RADIUS menggunakan mekanisme autentikasi ganda. Dua hal apa saja yang diautentikasi? Mengapa ini perlu?**
    -   Server RADIUS mengautentikasi **pengguna akhir** (misalnya, `user1`) dan **perangkat jaringan** (WLC) yang meminta layanan. Ini diperlukan untuk memastikan bahwa hanya perangkat jaringan tepercaya yang dapat meminta validasi, dan hanya pengguna sah yang diizinkan masuk.

2.  **Apa saja keuntungan WPA2-Enterprise dibandingkan WPA2-PSK?**
    -   **Manajemen Terpusat**: Akun pengguna dikelola di satu server.
    -   **Keamanan Individual**: Setiap pengguna memiliki kredensial unik.
    -   **Skalabilitas**: Mudah dikelola di jaringan besar.
    -   **Akuntabilitas**: Aktivitas login dapat dicatat di server.

# Ringkasan Materi 📚

Secara keseluruhan, kedua laboratorium ini memberikan gambaran lengkap mengenai evolusi arsitektur jaringan nirkabel, mulai dari skala rumahan yang sederhana hingga solusi enterprise yang kompleks dan terpusat.

Lab pertama memperkenalkan fundamental setup jaringan Wi-Fi menggunakan **Wireless Home Router (WHR)**. Dalam model ini, semua fungsi—seperti routing, switching, access point, dan DHCP—terintegrasi dalam satu perangkat. Keamanan yang digunakan adalah **WPA2-Personal** yang berbasis *passphrase*, sebuah solusi yang efektif dan mudah dikelola untuk lingkungan kecil.

Lab kedua meningkatkan kompleksitas dengan memperkenalkan arsitektur enterprise menggunakan **Wireless LAN Controller (WLC)**. Di sini, manajemen jaringan disentralisasi pada WLC, sementara **Lightweight Access Point (LAP)** hanya bertugas sebagai pemancar sinyal. Model ini memisahkan lalu lintas pengguna ke dalam **VLAN** yang berbeda dan meningkatkan keamanan ke level **WPA2-Enterprise**, yang mengandalkan autentikasi per-pengguna melalui server **RADIUS**. Pendekatan ini memberikan skalabilitas, manajemen terpusat, dan akuntabilitas yang jauh lebih tinggi, yang sangat penting untuk jaringan skala besar.

---

### Poin-Poin Penting

* **Perbedaan Arsitektur:** Memahami perbedaan fundamental antara arsitektur **standalone (WHR)** untuk SOHO dan arsitektur **terpusat (WLC + LAP)** untuk enterprise.
* **Metode Keamanan:** Mengenali dua mode keamanan utama:
    * **WPA2-Personal (PSK):** Menggunakan satu kata sandi bersama, cocok untuk jaringan kecil.
    * **WPA2-Enterprise (802.1X):** Menggunakan kredensial unik (username/password) untuk setiap pengguna yang diautentikasi oleh server RADIUS.
* **Manajemen Lalu Lintas:** Jaringan enterprise memisahkan lalu lintas klien ke dalam **VLAN** yang berbeda untuk meningkatkan keamanan dan efisiensi jaringan.
* **Proses Autentikasi:** WPA2-Personal melakukan autentikasi langsung di router, sedangkan WPA2-Enterprise mendelegasikan prosesnya ke **server RADIUS** eksternal.
* **Skalabilitas & Manajemen:** Solusi berbasis WLC jauh lebih mudah dikelola dan diperluas (*scalable*) untuk melayani ratusan atau ribuan pengguna dibandingkan mengelola banyak router secara individual.
* **Verifikasi Jaringan:** Keterampilan dasar seperti menggunakan perintah `ipconfig` dan `ping` adalah fondasi penting untuk melakukan troubleshooting di kedua jenis arsitektur jaringan.