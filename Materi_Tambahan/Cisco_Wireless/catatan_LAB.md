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