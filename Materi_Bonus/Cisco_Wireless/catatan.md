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

## Cara Kerja Jaringan Nirkabel
