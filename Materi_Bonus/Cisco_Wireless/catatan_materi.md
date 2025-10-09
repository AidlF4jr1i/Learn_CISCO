# Jaringan Wireless

## Membedah Jaringan Nirkabel (Wireless)
Setelah sebelumnya kita banyak membahas konfigurasi jaringan berbasis kabel (LAN), sekarang saatnya kita mendalami mekanisme jaringan nirkabel (wireless). Kenapa ini penting? Karena teknologi wireless sudah menjadi bagian tak terpisahkan dari kehidupan sehari-hari, mulai dari perkantoran hingga di setiap rumah. Oleh karena itu, pemahaman mendalam tentang cara kerja dan konfigurasinya adalah sebuah keharusan bagi seorang network engineer.

---

## Wireless dan Spektrum Frekuensi Radio
Pada dasarnya, semua komunikasi nirkabel terjadi melalui spektrum elektromagnetik. Jaringan nirkabel yang paling sering kita gunakan, yaitu WLAN (Wi-Fi), beroperasi pada pita frekuensi **2.4 GHz** dan **5 GHz**. Pita frekuensi ini dikenal sebagai **ISM Band (Industrial, Scientific, and Medical Band)**. Penggunaan frekuensi ini diatur oleh lembaga pemerintah terkait di setiap negara untuk memastikan tidak terjadi interferensi. Di Indonesia, regulasi ini berada di bawah wewenang **Kementerian Komunikasi dan Informatika (Kominfo)**.

---

## Klasifikasi Jaringan Nirkabel Berdasarkan Jangkauan

### 1. WPAN (Wireless Personal-Area Network)
Jenis jaringan nirkabel dengan jangkauan paling pendek, biasanya hanya sekitar **5 hingga 10 meter**. WPAN diatur oleh standar **IEEE 802.15** dan beroperasi di frekuensi **2.4 GHz**. Teknologi ini ideal untuk menghubungkan perangkat pribadi dalam jarak dekat.

**Contoh Teknologi:** Bluetooth, Zigbee.

---

### 2. WLAN (Wireless Local-Area Network)
WLAN adalah yang paling umum kita kenal sebagai **Wi-Fi**. Jangkauannya bisa mencapai **100 meter** di area terbuka dan diatur oleh standar **IEEE 802.11**. Frekuensi yang digunakan adalah **2.4 GHz** dan **5 GHz** (bahkan sudah merambah ke 6 GHz).

**Contoh Penggunaan:** Wi-Fi di rumah (IndiHome, CBN), kantor, kafe, sekolah.

---

### 3. WMAN (Wireless Metropolitan-Area Network)
Versi skala besar dari WLAN, mencakup satu kota atau area metropolitan. Menggunakan **frekuensi berlisensi (berbayar)** untuk koneksi stabil.

**Contoh:** Infrastruktur **menara BTS** operator seluler.

---

### 4. WWAN (Wireless Wide-Area Network)
Memiliki cakupan paling luas hingga antar negara atau benua. Mengandalkan **menara seluler** atau **satelit**.

**Contoh Teknologi:** 4G LTE, 5G, koneksi ATM, dan internet satelit.

---

## Teknologi Jaringan Nirkabel Populer

### 1. Bluetooth
Bluetooth adalah standar WPAN untuk pertukaran data jarak dekat. Membentuk **piconet** untuk menghubungkan hingga delapan perangkat.

**Contoh:** Headset audio, speaker portable, transfer file antar ponsel.

---

### 2. Bluetooth Low Energy (BLE)
Varian Bluetooth untuk perangkat dengan **daya sangat rendah**. Cocok untuk **IoT, sensor, smartwatch, dan fitness tracker.**

---

### 3. Bluetooth BR/EDR (Basic Rate/Enhanced Data Rate)
Mode Bluetooth klasik dengan kecepatan hingga **3 Mbps**. Ideal untuk streaming musik atau audio nirkabel.

---

### 4. WiMAX (Worldwide Interoperability for Microwave Access)
Teknologi **WMAN** berbasis standar **IEEE 802.16**. Menyediakan akses broadband nirkabel jarak jauh.

**Contoh Penggunaan:** Koneksi point-to-point dan backhaul jaringan.

---

### 5. Seluler
Teknologi **WWAN** paling dominan seperti **4G LTE dan 5G**. Menyediakan koneksi data untuk perangkat bergerak.

---

### 6. Satelit Broadband
Layanan internet melalui **satelit orbit bumi**, cocok untuk daerah terpencil dan maritim.

---

## Standar Wi-Fi: Evolusi IEEE 802.11

| IEEE Standard | Radio Frequency | Deskripsi |
| :------------ | :-------------- | :---------- |
| 802.11 | 2.4 GHz | Kecepatan hingga 2 Mbps |
| 802.11a | 5 GHz | Kecepatan hingga 54 Mbps, tidak kompatibel dengan 802.11b/g |
| 802.11b | 2.4 GHz | Kecepatan hingga 11 Mbps, jangkauan lebih baik |
| 802.11g | 2.4 GHz | Kecepatan hingga 54 Mbps, kompatibel dengan 802.11b |
| 802.11n | 2.4 & 5 GHz | Kecepatan 150–600 Mbps, menggunakan teknologi MIMO |
| 802.11ac | 5 GHz | Kecepatan 450 Mbps–1.3 Gbps, dikenal sebagai Wi-Fi 5 |
| 802.11ax | 2.4 & 5 GHz | Dikenal sebagai Wi-Fi 6, fokus pada efisiensi perangkat padat |

---

## Organisasi di Balik Standar Nirkabel

### 1. ITU (International Telecommunication Union)
Badan PBB yang mengatur **telekomunikasi global**, termasuk alokasi **spektrum radio** dan **orbit satelit**.

### 2. IEEE (Institute of Electrical and Electronics Engineers)
Organisasi profesional yang mengembangkan standar seperti **IEEE 802.3 (Ethernet)** dan **IEEE 802.11 (Wi-Fi)**.

### 3. Wi-Fi Alliance
Konsorsium global pemegang merek dagang “Wi-Fi”, yang mengeluarkan sertifikasi **Wi-Fi CERTIFIED** untuk menjamin interoperabilitas perangkat.

---

## Komponen Penting Jaringan Nirkabel

### 1. NIC (Network Interface Card)
Komponen yang memungkinkan perangkat terhubung ke jaringan. Mengubah data digital menjadi sinyal radio dan sebaliknya.

**Contoh:**
- NIC Internal: Chip Wi-Fi tertanam di laptop atau ponsel.
- USB Wireless Adapter: Tambahan Wi-Fi eksternal.
- PCIe Wireless Card: Dipasang di PC desktop.

---

### 2. Wireless Home Router
Perangkat multifungsi yang menggabungkan:
- **Router:** Mengatur lalu lintas data dan IP.
- **Switch:** Menghubungkan perangkat via kabel.
- **Access Point:** Memancarkan sinyal Wi-Fi.

**Contoh:** TP-Link Archer, Linksys Velop, Netgear Nighthawk.

---

### 3. Access Point (AP)
Membentuk jaringan WLAN, menjadi jembatan antara jaringan kabel dan nirkabel.

**Jenis AP:**
- **Standalone:** Dikonfigurasi manual satu per satu.
- **Controller-Based (LAP):** Dikelola oleh **Wireless LAN Controller (WLC)** secara terpusat.

---

## Jenis-Jenis Antena

### 1. Omnidirectional
Memancarkan sinyal ke segala arah 360°. Cocok untuk area cakupan luas.

**Contoh:** Antena stik (dipole) pada router rumah.

### 2. Directional
Memancarkan sinyal fokus ke satu arah, seperti lampu senter.

**Contoh:** Antena Yagi, Parabolic/Dish.

### 3. Multiple Input Multiple Output (MIMO)
Teknologi dengan banyak antena untuk meningkatkan kecepatan dan keandalan transmisi. Digunakan sejak **Wi-Fi 802.11n**.

---
## Cara Kerja Wireless

### 1. Mode Operasi Jaringan Wi-Fi

Pada jaringan Wi-Fi, ada tiga mode operasi utama yang perlu diketahui.

#### ● Mode Ad-Hoc
Mode ini memungkinkan dua atau lebih perangkat klien terhubung secara langsung satu sama lain tanpa memerlukan perangkat perantara seperti Access Point (AP). Komunikasi ini bersifat *peer-to-peer (P2P)*, di mana setiap perangkat memiliki peran yang setara.  
Bayangkan Anda dan teman Anda ingin bertukar file langsung dari laptop masing-masing tanpa ada koneksi internet atau router — inilah fungsi mode Ad-Hoc.

#### ● Tethering (Mobile Hotspot)
Ini adalah variasi dari mode Ad-Hoc, di mana perangkat utamanya adalah ponsel. Saat fitur tethering (hotspot) diaktifkan, ponsel Anda berubah fungsi menjadi semacam Access Point portabel.

- **Cara Kerja:** Ponsel terhubung ke internet melalui data seluler (ISP). Kemudian, fitur hotspot akan mengaktifkan DHCP Server mini di ponsel untuk membagikan alamat IP privat ke perangkat lain yang terhubung (laptop, tablet, dll.).  
- **Proses NAT:** Ketika perangkat-perangkat tersebut mengakses internet, ponsel akan melakukan proses *NAT (Network Address Translation)*. Artinya, semua permintaan dari IP privat akan “diterjemahkan” dan diwakilkan oleh satu IP publik milik ponsel.  
- **Konsumsi Daya:** Proses ini (menjadi AP, menjalankan DHCP, dan melakukan NAT terus-menerus) membutuhkan banyak daya, sehingga baterai ponsel bisa cepat habis saat tethering aktif.

#### ● Mode Infrastruktur AP
Ini adalah mode yang paling umum digunakan. Mode ini digunakan untuk menghubungkan klien ke sebuah jaringan yang lebih besar (seperti internet) menggunakan Access Point (AP) sebagai perantara. Semua komunikasi dari klien harus melewati AP terlebih dahulu.

---

### 2. Arsitektur pada Mode Infrastruktur AP: BSS dan ESS

Dalam mode infrastruktur AP, ada dua arsitektur dasar.

#### ● BSS (Basic Service Set)
BSS terdiri dari satu Access Point (AP) tunggal yang melayani semua klien nirkabel di area jangkauannya. AP ini bertindak sebagai gerbang bagi semua klien untuk berkomunikasi satu sama lain atau terhubung ke jaringan kabel (LAN). Dalam skenario ini, klien dari BSS yang berbeda (yang dilayani oleh AP yang berbeda dan tidak terhubung) tidak dapat berkomunikasi secara langsung.

#### ● ESS (Extended Service Set)
ESS adalah gabungan dari dua atau lebih BSS yang dihubungkan melalui sebuah sistem kabel yang disebut *Distribution System (DS)*, yang biasanya adalah jaringan LAN (misalnya, terhubung ke switch yang sama).

- **Tujuan:** Memperluas area jangkauan nirkabel. Semua AP dalam satu ESS akan menggunakan SSID (nama jaringan Wi-Fi) yang sama.  
- **Roaming:** Ini memungkinkan klien berpindah tempat dari jangkauan satu AP ke AP lain tanpa koneksi terputus. Proses ini disebut *roaming*.

---

### 3. Mekanisme Akses Media Nirkabel

Karena media transmisi nirkabel digunakan bersama-sama, diperlukan sebuah aturan agar tidak terjadi tabrakan data.

#### ● CA (Collision Avoidance)
Ini adalah prinsip dasar di mana jaringan nirkabel berusaha untuk menghindari tabrakan data (*Collision Avoidance*), berbeda dengan jaringan kabel (Ethernet) yang mendeteksi tabrakan setelah terjadi (*Collision Detection*).  
Analogi sederhananya seperti menggunakan walkie-talkie:

- Anda tidak bisa berbicara (mengirim) dan mendengar (menerima) secara bersamaan.
- Sebelum berbicara, Anda harus mendengarkan terlebih dahulu untuk memastikan tidak ada orang lain yang sedang berbicara. Jika ada, Anda harus menunggu giliran.

#### ● CSMA/CA (Carrier Sense Multiple Access with Collision Avoidance)
Ini adalah protokol atau metode spesifik yang menerapkan prinsip CA. Cara kerjanya:

1. **Listen Before Talk:** Sebelum mengirim data, perangkat akan mendengarkan media transmisi (*Carrier Sense*) untuk memeriksa apakah ada perangkat lain yang sedang mengirim data.  
2. **Tunggu & Kirim:** Jika media sedang kosong, perangkat akan menunggu sesaat (dalam rentang waktu acak yang sangat singkat) sebelum mulai mengirimkan data.  
3. **RTS/CTS (Opsional):** Untuk mengatasi masalah *hidden node*, ada mekanisme opsional:  
   - Perangkat pengirim mengirim pesan singkat *RTS (Request to Send)* ke AP.  
   - Jika AP siap, ia membalas dengan *CTS (Clear to Send)* yang bisa didengar oleh semua klien di jangkauannya.  
   - Setelah menerima CTS, perangkat mengirimkan paket DATA utama.  
   - AP mengirimkan *ACK (Acknowledgement)* untuk mengonfirmasi bahwa data diterima dengan baik.

---

### 4. Bagaimana Klien Mengenal Access Point (AP)?

Ada dua cara bagi perangkat untuk menemukan jaringan Wi-Fi yang tersedia.

#### ● Passive Scanning (Memindai Pasif)
Dalam mode ini, klien hanya “mendengarkan”. Access Point secara berkala mengirimkan sinyal yang disebut *Beacon Frame*. Sinyal ini seperti iklan yang berisi informasi tentang AP, seperti SSID, kecepatan, dan metode keamanan. Klien mengumpulkan semua *beacon frame* ini untuk menampilkan daftar jaringan Wi-Fi yang tersedia.

#### ● Active Scanning (Memindai Aktif)
Dalam mode ini, klien proaktif mencari jaringan.

1. Klien mengirimkan *Probe Request* ke semua channel (“AP mana saja yang ada di sini?”).  
2. Semua AP yang menerima permintaan membalas dengan *Probe Response* yang isinya mirip dengan *Beacon Frame*.  
3. Setelah balasan diterima, koneksi dapat dibangun antara klien dan AP yang dipilih.

---

### 5. Protokol CAPWAP (Manajemen AP Terpusat)

Di lingkungan besar seperti kantor atau kampus, banyak AP perlu dikelola secara terpusat. Di sinilah protokol CAPWAP berperan.

**CAPWAP (Control and Provisioning of Wireless Access Points)** adalah protokol standar yang memungkinkan *Lightweight Access Point (LAP)* berkomunikasi dengan *Wireless LAN Controller (WLC)*. WLC bertindak sebagai “otak” yang mengelola konfigurasi, keamanan, dan operasi semua AP yang terhubung.

#### A. DTLS (Datagram Transport Layer Security)
Mekanisme keamanan dalam CAPWAP. DTLS mengenkripsi lalu lintas kontrol dan manajemen antara WLC dan AP. Ini memastikan hanya AP sah yang bisa bergabung ke WLC dan mencegah WLC palsu mengambil alih AP.

#### B. FlexConnect (Contoh Teknologi Spesifik)
*FlexConnect* (istilah Cisco, vendor lain memiliki nama berbeda) digunakan untuk mengelola AP yang berada di lokasi terpencil dan terhubung ke WLC pusat melalui jaringan WAN.

- **Connected Mode:** Saat koneksi WAN aktif, AP bekerja normal. Traffic manajemen dikirim ke WLC, sementara traffic data dari klien bisa langsung keluar ke jaringan lokal agar tidak membebani link WAN.  
- **Standalone Mode:** Jika koneksi WAN ke WLC terputus, AP beralih ke mode mandiri. Dalam mode ini, AP tetap bisa melayani klien yang sudah terhubung atau baru dengan otentikasi lokal (jika dikonfigurasi). AP mengambil alih sebagian fungsi WLC hingga koneksi pulih.

---


## Manajemen Kanal