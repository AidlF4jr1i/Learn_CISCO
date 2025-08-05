# Memahami BGP (Border Gateway Protocol): Fondasi Internet

Selamat datang di panduan pengenalan **Border Gateway Protocol (BGP)**! BGP adalah protokol yang menjadi tulang punggung internet global. Berbeda dengan protokol routing internal seperti OSPF atau EIGRP yang bekerja di dalam satu jaringan perusahaan, BGP dirancang khusus untuk menghubungkan jaringan-jaringan besar yang berbeda di seluruh dunia, yang dikenal sebagai **Autonomous System (AS)**.

Dalam panduan ini, kita akan mengupas konsep dasar BGP, mulai dari perbedaannya dengan protokol lain hingga melakukan konfigurasi praktis pada topologi router. Mari kita mulai!

---

## 🌐 Konsep Dasar: IGP vs EGP

Sebelum menyelami BGP, penting untuk memahami dua kategori utama protokol routing:

1.  **Interior Gateway Protocol (IGP)**: Protokol ini digunakan untuk bertukar informasi routing *di dalam* sebuah Autonomous System (AS). Bayangkan sebuah perusahaan besar dengan ratusan router; IGP memastikan semua router di dalam perusahaan tersebut dapat saling berkomunikasi secara efisien.
    * **Contoh Populer**: OSPF, EIGRP (Cisco), IS-IS.

2.  **Exterior Gateway Protocol (EGP)**: Protokol ini bertugas menghubungkan satu AS dengan AS lainnya. Inilah yang membentuk internet seperti yang kita kenal, memungkinkan jaringan milik Telkomsel terhubung dengan jaringan Google, dan seterusnya.
    * **Satu-satunya Protokol**: Saat ini, satu-satunya protokol EGP yang digunakan secara masif adalah **BGP**.

> **Penasaran?** Anda bisa melihat bagaimana BGP bekerja di dunia nyata! Kunjungi situs seperti [bgp.he.net](https://bgp.he.net) dan anda akan langsung melihat nama ISP yang anda gunakan. Anda akan melihat nomor AS milik ISP tersebut dan AS-AS lain yang terhubung dengannya.

---

## 🔬 Lab 1: Konfigurasi BGP Dasar dengan 2 Router

Mari kita mulai dengan konfigurasi paling sederhana untuk memahami cara kerja BGP.

**Topologi:**
* 2 Router (R1 dan R2) yang terhubung langsung.
* Masing-masing router memiliki jaringan lokal (misalnya, terhubung ke PC).
* [Lihat diagram topologi di sini](https://drive.google.com/open?id=1dOjqTSSpn-X6EHePr-9BZ-fkx-P_rEk9&usp=drive_fs)

### 📐 Alokasi IP Address

| Router | Interface         | Alamat IP        | Keterangan          |
| :----- | :---------------- | :--------------- | :------------------ |
| **R1** | Ke Client (Lokal) | `192.168.1.1/24` | Jaringan Internal 1 |
|        | Ke R2             | `10.10.10.1/24`  | Koneksi Antar-AS    |
| **R2** | Ke Client (Lokal) | `192.168.2.1/24` | Jaringan Internal 2 |
|        | Ke R1             | `10.10.10.2/24`  | Koneksi Antar-AS    |

> 📌 **Catatan:** Pastikan IP Address pada PC klien berada dalam subnet yang sama dengan interface router yang terhubung.

### ⚙️ Langkah Konfigurasi BGP

Kunci dari BGP adalah kita harus secara manual mendefinisikan *tetangga* (neighbor) dan nomor AS-nya. Ini berbeda dengan IGP yang biasanya menemukan tetangganya secara otomatis.

**Konfigurasi pada R1 (AS 1):**
```cisco
enable
configure terminal

! Masuk ke mode konfigurasi BGP dengan nomor AS 1
router bgp 1

! Mendefinisikan R2 (10.10.10.2) sebagai tetangga di AS 2
neighbor 10.10.10.2 remote-as 2

! Mengiklankan (advertise) jaringan yang ingin dibagikan ke R2
network 192.168.1.0 mask 255.255.255.0
network 10.10.10.0 mask 255.255.255.0

end
```

**Konfigurasi pada R2 (AS 2):**
```cisco
enable
configure terminal

! Masuk ke mode konfigurasi BGP dengan nomor AS 2
router bgp 2

! Mendefinisikan R1 (10.10.10.1) sebagai tetangga di AS 1
neighbor 10.10.10.1 remote-as 1

! Mengiklankan (advertise) jaringan yang ingin dibagikan ke R1
network 192.168.2.0 mask 255.255.255.0
network 10.10.10.0 mask 255.255.255.0

end
```

### ✅ Verifikasi

Untuk memeriksa apakah hubungan BGP sudah terbentuk (Established), gunakan perintah `show ip bgp summary`.

```bash
R2# show ip bgp summary
BGP router identifier 192.168.2.1, local AS number 2
BGP table version is 5, main routing table version 6
...
Neighbor        V    AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.10.10.1      4     1       5       2        5    0    0 00:00:35        4
```

Perhatikan kolom `State/PfxRcd`. Jika Anda melihat angka (selain 0 atau "Idle"), itu berarti koneksi berhasil dan R2 telah menerima 4 prefix jaringan dari R1. Sekarang, coba lakukan `ping` dari PC di jaringan R1 ke PC di jaringan R2. Seharusnya sudah berhasil!

---

## 🧪 Lab 2: BGP dengan 3 Router dan VLAN

Sekarang, mari kita buat topologi lebih kompleks dengan menambahkan satu router lagi dan mengenalkan VLAN.

**Topologi Baru:**
* R1 terhubung ke R2.
* R2 terhubung ke R3.
* R3 memiliki beberapa VLAN untuk jaringan lokalnya.
* [Lihat diagram topologi baru di sini](https://drive.google.com/open?id=14DSBWbiIn2DUJ2_vQ2rCTVf1qhDjR2tc&usp=drive_fs)

### 📐 Alokasi IP Address & AS

| Router | AS # | Interface         | Alamat IP          | Keterangan              |
| :----- | :--- | :---------------- | :----------------- | :---------------------- |
| **R1** | 1    | Ke R2             | `10.10.10.1/24`    | -                       |
| **R2** | 2    | Ke R1             | `10.10.10.2/24`    | -                       |
|        |      | Ke R3             | `10.20.20.1/24`    | -                       |
| **R3** | 3    | Ke R2             | `10.20.20.2/24`    | -                       |
|        |      | VLAN 2 (Lokal)    | `192.168.20.1/24`  | Sub-interface fa1/0.2   |
|        |      | VLAN 3 (Lokal)    | `192.168.30.1/24`  | Sub-interface fa1/0.3   |
|        |      | Jaringan fisik    | `192.168.3.1/24`   | -                       |

### ⚙️ Langkah Konfigurasi Tambahan

Kita hanya perlu menambahkan konfigurasi pada R2 dan R3.

**Tambahan Konfigurasi pada R2 (AS 2):**
```cisco
configure terminal
router bgp 2

! Menambahkan R3 (10.20.20.2) sebagai tetangga baru di AS 3
neighbor 10.20.20.2 remote-as 3

! Mengiklankan jaringan penghubung ke R3
network 10.20.20.0 mask 255.255.255.0

end
```

**Konfigurasi Penuh pada R3 (AS 3):**
```cisco
enable
configure terminal

! Konfigurasi Sub-interface untuk VLAN
interface fastEthernet 1/0
 no shutdown
exit

interface fastEthernet 1/0.2
 encapsulation dot1Q 2
 ip address 192.168.20.1 255.255.255.0
exit

interface fastEthernet 1/0.3
 encapsulation dot1Q 3
 ip address 192.168.30.1 255.255.255.0
exit

! Konfigurasi BGP
router bgp 3
 neighbor 10.20.20.1 remote-as 2

 ! Mengiklankan semua jaringan yang terhubung di R3
 network 192.168.3.0 mask 255.255.255.0
 network 192.168.20.0 mask 255.255.255.0
 network 192.168.30.0 mask 255.255.255.0
 network 10.20.20.0 mask 255.255.255.0
end
```

**Konfigurasi pada Switch yang terhubung ke R3:**
```cisco
enable
configure terminal

! Konfigurasi trunk port ke router
interface fastEthernet 0/3
 switchport mode trunk
exit

! Membuat VLAN
vlan 2
exit
vlan 3
exit

! Menetapkan port ke VLAN
interface fastEthernet 0/1
 switchport mode access
 switchport access vlan 2
exit

interface fastEthernet 0/2
 switchport mode access
 switchport access vlan 3
exit
```

Setelah semua konfigurasi selesai, lakukan tes ping antar PC yang berada di AS yang berbeda (misal, dari PC di jaringan R1 ke PC di VLAN 2 pada R3). Jika semua berjalan lancar, komunikasi lintas AS dan VLAN sudah berhasil!

---

## 📦 Bonus: `network` vs. `redistribute connected`

Di BGP, ada dua cara umum untuk mengiklankan jaringan: perintah `network` yang sudah kita gunakan, dan `redistribute connected`. Apa bedanya?

### 🎯 Perbandingan Fungsi

| Perintah                 | Cara Kerja                                                                 | Analogi                                                                 |
| :----------------------- | :------------------------------------------------------------------------- | :---------------------------------------------------------------------- |
| **`network`** | Mengiklankan **hanya** prefix jaringan yang Anda tulis secara manual.      | Anda memilih dokumen spesifik (misal: "Laporan Keuangan Q1") untuk dibagikan. |
| **`redistribute connected`** | Secara **otomatis** mengiklankan **semua** jaringan yang statusnya `connected` (terhubung langsung) di router. | Anda membagikan semua dokumen yang ada di meja kerja Anda, tanpa terkecuali. |

### ⚠️ Kapan Sebaiknya Digunakan?

| Skenario                               | Gunakan `network` | Gunakan `redistribute connected` |
| :------------------------------------- | :---------------: | :------------------------------: |
| **Jaringan Produksi / Perusahaan** |         ✅        |        ❌ (Sangat Berisiko)        |
| **Lab / Ujian Sertifikasi (CCNA)** |         ✅        |       ✅ (Untuk kemudahan)       |
| **Ingin kontrol penuh & keamanan** |         ✅        |                ❌                |
| **Banyak interface & ingin otomatis** |         ❌        |    ✅ (Wajib dengan filter!)     |

### 🛡️ Praktik Terbaik (Best Practice)

Di lingkungan produksi, menggunakan `redistribute connected` tanpa filter adalah **kesalahan fatal** karena bisa membocorkan jaringan internal yang seharusnya tidak terlihat oleh publik.

Jika terpaksa menggunakannya, **WAJIB** pasang filter menggunakan `route-map` untuk menyaring jaringan mana saja yang boleh diiklankan.

**Contoh Konfigurasi Aman:**
```cisco
! Hanya izinkan network 192.168.100.0/24 yang di-redistribute
router bgp 3
 redistribute connected route-map IZINKAN-JARINGAN-INI

! Buat route-map untuk memfilter
route-map IZINKAN-JARINGAN-INI permit 10
 match ip address prefix-list HANYA-PREFIX-INI

! Definisikan prefix yang diizinkan
ip prefix-list HANYA-PREFIX-INI seq 5 permit 192.168.100.0/24
```

> 🔖 **Kesimpulan**:
> * Gunakan `network` untuk kontrol yang ketat dan keamanan maksimal.
> * Gunakan `redistribute connected` hanya di lingkungan lab atau jika Anda benar-benar tahu apa yang Anda lakukan dan **selalu gunakan filter**.

---

Materi ini adalah pengenalan dasar BGP yang sudah lebih dari cukup untuk level CCNA. Konsep BGP yang lebih dalam seperti *path attributes*, *route reflectors*, dan *confederations* akan dibahas di level sertifikasi yang lebih tinggi.

Selamat belajar, dan sampai jumpa di bab selanjutnya!