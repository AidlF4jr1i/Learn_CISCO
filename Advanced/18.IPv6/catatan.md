# Panduan Lengkap Konfigurasi IPv6 untuk CCNA

Halo, teman-teman! Selamat datang di panduan seru seputar **IPv6**. Dokumen ini dirancang khusus buat kamu yang lagi mendalami materi CCNA 200-301. Kita akan kupas tuntas mulai dari konsep dasar IPv6, cara menyingkat alamatnya yang super panjang, sampai konfigurasi routing statis dan dinamis (OSPF & EIGRP) di perangkat Cisco!

---

##  Mengenal IPv6

**IPv6** adalah versi terbaru dari Internet Protocol yang diciptakan untuk mengatasi masalah keterbatasan alamat pada IPv4. Kalau IPv4 jumlahnya "hanya" sekitar 4,3 miliar, IPv6 punya **340 undecillion** alamat (angka 340 diikuti 36 angka nol!). Jumlah yang luar biasa banyak ini memastikan kita nggak akan kehabisan alamat IP di masa depan.

Untuk perbandingan lebih detail antara IPv4 dan IPv6, kamu bisa lihat tabel di sini: [Perbandingan IPv4 vs IPv6](https://drive.google.com/open?id=1z9RmfXVrE1UY12he3N3KP-oNvp1z9BEG&usp=drive_fs)

---

## Penyederhanaan Alamat IPv6 (Kompresi)

Seperti yang kita tahu, alamat IPv6 itu panjang banget, terdiri dari 8 blok (hextet) yang dipisahkan oleh titik dua (`:`), di mana setiap blok berisi 4 karakter heksadesimal.

Contoh alamat lengkap: `2001:0db8:0000:0000:0000:ff00:0042:8329`

Panjang banget, kan? Nah, biar lebih simpel dan mudah dibaca, kita bisa menyingkatnya dengan dua aturan utama:

1.  **Hapus Angka Nol di Depan:** Pada setiap blok, kita bisa menghilangkan angka `0` yang ada di awal.
    * `0db8` menjadi `db8`
    * `0042` menjadi `42`
    * `0000` menjadi `0`

2.  **Gunakan Titik Dua Ganda (`::`):** Jika ada satu atau lebih blok yang isinya `0000` secara berurutan, kita bisa menggantinya dengan `::`. Aturan ini **hanya boleh digunakan sekali** dalam satu alamat IP.

Mari kita coba kompres beberapa contoh:

* **Contoh 1:** `2001:0db8:0000:0000:0000:ff00:0042:8329`
    * Ada tiga blok `0000` berurutan di tengah. Kita bisa singkat menjadi `::`.
    * Hasil kompresi: `2001:db8::ff00:42:8329`

* **Contoh 2:** `2607:f0d0:1002:0011:0000:0000:0000:0001`
    * Ada tiga blok `0000` berurutan. Kita singkat jadi `::`.
    * `0011` menjadi `11`, dan `0001` menjadi `1`.
    * Hasil kompresi: `2607:f0d0:1002:11::1`

Sekarang, gimana kalau ada dua kelompok blok nol yang terpisah? Misal: `2001:0db8:0000:0000:1111:0000:0000:0000`
Kita harus memilih salah satu grup untuk disingkat dengan `::`. Biasanya, kita memilih grup yang paling panjang.
* Opsi 1 (benar): `2001:db8:0:0:1111::` (Mempersingkat 3 blok nol di akhir)
* Opsi 2 (salah): `2001:db8::1111:0:0:0` (Mempersingkat 2 blok nol di awal)

### Mengembalikan Alamat IPv6 (Un-compress)

Proses kebalikannya juga gampang. Kita hanya perlu mengisi `::` dengan blok `0000` sampai total ada 8 blok.

* **Contoh 1:** `2001:db8::1`
    * Ada 3 blok yang terlihat (`2001`, `db8`, `1`).
    * Kita butuh 5 blok lagi untuk melengkapi.
    * Hasil un-compress: `2001:0db8:0000:0000:0000:0000:0000:0001`

* **Contoh 2:** `fe80::f2de:f1ff:fe3f:307e`
    * Ada 5 blok yang terlihat. Butuh 3 blok lagi.
    * Hasil un-compress: `fe80:0000:0000:0000:f2de:f1ff:fe3f:307e`

---

## Tipe-Tipe Alamat IPv6

Ada tiga jenis alamat IPv6 yang paling umum:

1.  **Global Unicast (Range: `2000::/3`)**
    * Ini setara dengan **IP Publik** di IPv4. Alamat ini unik secara global dan bisa di-routing di internet.
    * Ciri utamanya, alamatnya selalu diawali dengan angka `2` atau `3`.

2.  **Unique Local (Range: `FC00::/7`)**
    * Ini setara dengan **IP Private** di IPv4 (seperti `192.168.x.x`). Digunakan untuk jaringan lokal dan tidak bisa di-routing di internet.
    * Ciri utamanya, alamatnya selalu diawali dengan `FC` atau `FD`.

3.  **Link-Local (Range: `FE80::/10`)**
    * Alamat ini digunakan untuk komunikasi antar perangkat dalam satu segmen jaringan (satu link) yang sama. Setiap interface yang mengaktifkan IPv6 akan otomatis memiliki alamat ini.
    * Ciri utamanya, selalu diawali dengan `FE80`.

---

## Memahami Prefix IPv6

**Prefix** adalah bagian depan alamat IP yang berfungsi sebagai **Network ID** (identitas jaringan). Sisanya adalah **Host ID** (identitas perangkat). Prefix ditulis dalam format CIDR, contohnya `/64`.

* **IPv4:** Panjang 32 bit (prefix `/0` sampai `/32`)
* **IPv6:** Panjang 128 bit (prefix `/0` sampai `/128`)

Di IPv6, prefix yang paling umum digunakan untuk jaringan LAN adalah **/64**. Artinya, 64 bit pertama adalah alamat jaringan, dan 64 bit sisanya untuk host. Dengan sisa 64 bit, kita punya ruang host yang *sangat* besar, jadi nggak perlu khawatir kehabisan alamat untuk perangkat.

Agar dua perangkat bisa berkomunikasi langsung (tanpa router), mereka harus berada di jaringan yang sama, artinya **Network ID (prefix) mereka harus sama**.

Contoh:
* `2001:db8:1::1/64` dan `2001:db8:1::2/64` bisa berkomunikasi karena prefix `2001:db8:1::/64` mereka sama.

---

## Konfigurasi Dasar dan Topologi IPv6

Untuk latihan, kita akan menggunakan topologi seperti pada gambar berikut: [Topologi Jaringan IPv6](https://drive.google.com/open?id=1SJo0bOUso_8ZolDq4RyfLMGN7gS5vwGv&usp=drive_fs)

Berikut adalah alokasi IP untuk setiap router:

**Router R1:**
* `interface FastEthernet0/0`: `2001:db8:10::1/64` (menuju R2)
* `interface FastEthernet0/1`: `2001:db8:1::1/64` (menuju Client)

**Router R2:**
* `interface FastEthernet0/0`: `2001:db8:10::2/64` (menuju R1)
* `interface FastEthernet0/1`: `2001:db8:20::1/64` (menuju R3)
* `interface FastEthernet1/0`: `2001:db8:2::1/64` (menuju Client)

**Router R3:**
* `interface FastEthernet0/0`: `2001:db8:20::2/64` (menuju R2)
* `interface FastEthernet0/1`: `2001:db8:3::1/64` (menuju Client)

Gunakan perintah berikut untuk mengkonfigurasi alamat IP di setiap interface:
```cisco
! Masuk ke mode konfigurasi interface
interface <nama_interface>

! Terapkan alamat IPv6
ipv6 address <alamat_ipv6>/<prefix>

! Aktifkan interface
no shutdown
```

Setelah konfigurasi, kamu bisa cek dengan perintah ini:
```cisco
! Menampilkan ringkasan interface IPv6
show ipv6 interface brief

! Menampilkan tabel routing IPv6
show ipv6 route
```

---

## Konfigurasi Static Routing IPv6

Cara konfigurasi rute statis di IPv6 mirip dengan IPv4, tapi kita menunjuk ke **network prefix** tujuan, bukan network dengan subnet mask.

Format perintahnya: `ipv6 route <network_prefix_tujuan> <next_hop_ipv6_address>`

Penting! Jangan lupa aktifkan kemampuan routing IPv6 di setiap router dengan perintah `ipv6 unicast-routing`.

**Konfigurasi di R1:**
```cisco
ipv6 unicast-routing
ipv6 route 2001:db8:2::/64 2001:db8:10::2
ipv6 route 2001:db8:3::/64 2001:db8:10::2
ipv6 route 2001:db8:20::/64 2001:db8:10::2
```

**Konfigurasi di R2:**
```cisco
ipv6 unicast-routing
ipv6 route 2001:db8:1::/64 2001:db8:10::1
ipv6 route 2001:db8:3::/64 2001:db8:20::2
```

**Konfigurasi di R3:**
```cisco
ipv6 unicast-routing
ipv6 route 2001:db8:1::/64 2001:db8:20::1
ipv6 route 2001:db8:2::/64 2001:db8:20::1
ipv6 route 2001:db8:10::/64 2001:db8:20::1
```

---

## Konfigurasi Dynamic Routing: OSPFv3

Sekarang, kita coba konfigurasi routing dinamis menggunakan **OSPFv3** (OSPF untuk IPv6). Caranya sedikit berbeda dari OSPF untuk IPv4. Di sini, kita tidak menggunakan perintah `network`, melainkan mengaktifkan OSPF langsung di interfacenya.

Kita juga wajib mendefinisikan **Router ID** dalam format IPv4 untuk setiap router.

**Konfigurasi di R1:**
```cisco
ipv6 router ospf 1
 router-id 10.10.10.1
 exit
!
interface FastEthernet0/0
 ipv6 ospf 1 area 0
!
interface FastEthernet0/1
 ipv6 ospf 1 area 0
```

**Konfigurasi di R2:**
```cisco
ipv6 router ospf 2
 router-id 10.10.10.2
 exit
!
interface FastEthernet0/0
 ipv6 ospf 2 area 0
!
interface FastEthernet0/1
 ipv6 ospf 2 area 0
!
interface FastEthernet1/0
 ipv6 ospf 2 area 0
```

**Konfigurasi di R3:**
```cisco
ipv6 router ospf 3
 router-id 10.10.10.3
 exit
!
interface FastEthernet0/0
 ipv6 ospf 3 area 0
!
interface FastEthernet0/1
 ipv6 ospf 3 area 0
```
Untuk mengecek apakah OSPF sudah berjalan, gunakan `show ipv6 route ospf`.

---

## Konfigurasi Dynamic Routing: EIGRP for IPv6

Terakhir, kita akan coba **EIGRP for IPv6**. Pertama, pastikan kamu sudah menghapus konfigurasi OSPF sebelumnya dengan perintah `no ipv6 router ospf <process_id>` di setiap router.

**Hapus Konfigurasi OSPF:**
* **R1:** `no ipv6 router ospf 1`
* **R2:** `no ipv6 router ospf 2`
* **R3:** `no ipv6 router ospf 3`

Konfigurasi EIGRP for IPv6 mirip dengan OSPF, diaktifkan per-interface. Namun, di mode konfigurasi EIGRP, kita harus menjalankan perintah `no shutdown`. Pastikan juga **AS Number** (di contoh ini `10`) sama di semua router.

**Konfigurasi di R1:**
```cisco
ipv6 router eigrp 10
 eigrp router-id 10.10.10.1
 no shutdown
 exit
!
interface FastEthernet0/0
 ipv6 eigrp 10
!
interface FastEthernet0/1
 ipv6 eigrp 10
```

**Konfigurasi di R2:**
```cisco
ipv6 router eigrp 10
 eigrp router-id 10.10.10.2
 no shutdown
 exit
!
interface FastEthernet0/0
 ipv6 eigrp 10
!
interface FastEthernet0/1
 ipv6 eigrp 10
!
interface FastEthernet1/0
 ipv6 eigrp 10
```

**Konfigurasi di R3:**
```cisco
ipv6 router eigrp 10
 eigrp router-id 10.10.10.3
 no shutdown
 exit
!
interface FastEthernet0/0
 ipv6 eigrp 10
!
interface FastEthernet0/1
 ipv6 eigrp 10
```

---

Selesai! Kamu sudah berhasil memahami dan mengkonfigurasi berbagai aspek penting dari IPv6. Ini adalah pondasi yang kuat untuk karirmu di bidang jaringan. Teruslah belajar dan upgrade skill, mungkin selanjutnya bisa mendalami *wireless*, atau mencoba vendor lain seperti **MikroTik** dan **Juniper**.

Terima kasih sudah menyimak, semoga panduan ini bermanfaat ya!
