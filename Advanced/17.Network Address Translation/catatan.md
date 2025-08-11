# Network Address Translation (NAT): Gerbang Menuju Internet

Selamat datang di section selanjutnya! Kali ini kita akan membahas salah satu materi paling fundamental dan penting di dunia jaringan: **Network Address Translation** atau **NAT**.

Pernah bertanya-tanya bagaimana jutaan perangkat di rumah atau kantor kita yang menggunakan IP *private* (seperti `192.168.x.x`) bisa mengakses internet yang hanya mengenali IP *public*? Jawabannya adalah NAT!

**NAT adalah sebuah proses pada router yang bertugas menerjemahkan alamat IP private menjadi alamat IP public, dan sebaliknya.** Dengan begitu, perangkat-perangkat di jaringan lokal kita bisa "meminjam" IP public milik router untuk berkomunikasi dengan dunia luar, seperti mengakses Google, YouTube, dan layanan lainnya.

Ada dua jenis NAT yang akan kita pelajari:
1.  **Dynamic NAT (PAT)**: Menerjemahkan *banyak* IP private ke *satu* IP public. Ini yang paling umum digunakan.
2.  **Static NAT**: Menerjemahkan *satu* IP private ke *satu* IP public secara spesifik.

Biar nggak pusing, kita langsung praktik saja!

---

## Dynamic NAT (PAT - Port Address Translation)

Ini adalah jenis NAT yang paling sering kita temui, di mana banyak klien di jaringan lokal bisa mengakses internet secara bersamaan hanya dengan menggunakan satu alamat IP public. Metode ini juga sering disebut **NAT Overload**.

### Topologi Laboratorium

Kita akan menggunakan topologi berikut untuk simulasi.
[Link Topologi](https://drive.google.com/open?id=1VnyfSnWxIAAwnj6syvxmq9UlxpkXTMu_&usp=drive_fs)

**Peran Setiap Perangkat:**
-   **Server Google (`172.16.10.2/24`)**: Kita anggap ini adalah server Google yang akan kita akses.
-   **R1 (Router Google)**: Router milik Google, terhubung ke internet. Menggunakan BGP AS 1.
-   **R2 (Router ISP)**: Router milik penyedia layanan internet. Menggunakan BGP AS 2.
-   **R3 (Router Client)**: Router di kantor kita. Di sinilah kita akan mengkonfigurasi NAT.
-   **Switch & PCs**: Jaringan lokal kita dengan VLAN 10 dan 20.

### Konfigurasi

Kita asumsikan konfigurasi dasar (IP address, VLAN, DHCP) sudah selesai. Kita akan fokus pada routing (BGP) dan NAT-nya.

#### 1. Konfigurasi Routing (BGP & Default Route)

Agar semua router bisa saling bertukar informasi jaringan, kita gunakan BGP antara R1 dan R2. Lalu, R3 akan kita beri *default route* ke arah ISP.

**R1 (Router Google):**
```cisco
router bgp 1
 neighbor 10.10.10.2 remote-as 2
 network 172.16.10.0 mask 255.255.255.0
```

**R2 (Router ISP):**
```cisco
router bgp 2
 neighbor 10.10.10.1 remote-as 1
 network 10.20.20.0 mask 255.255.255.0
```

**R3 (Router Client):**
```cisco
! Default route ini memberitahu R3, "untuk ke tujuan manapun,
! kirim saja paketnya ke router ISP (10.20.20.1)"
ip route 0.0.0.0 0.0.0.0 10.20.20.1
```

#### 2. Konfigurasi NAT di R3

Ini dia bagian intinya!

**Langkah 1: Tentukan IP Private mana yang boleh di-NAT.**
Kita menggunakan Access List untuk menandai jaringan lokal kita (VLAN 10 dan 20).
```cisco
R3(config)# access-list 1 permit 192.168.10.0 0.0.0.255
R3(config)# access-list 1 permit 192.168.20.0 0.0.0.255
```

**Langkah 2: Buat Aturan NAT.**
Perintah ini artinya: "Terjemahkan semua IP sumber (`source`) yang ada di `list 1` ke alamat IP yang menempel di interface `fa0/0` ketika paket keluar. `overload` berarti banyak-ke-satu."
```cisco
R3(config)# ip nat inside source list 1 interface FastEthernet0/0 overload
```

**Langkah 3: Tentukan Interface "Dalam" dan "Luar".**
Kita harus memberitahu router, mana sisi jaringan lokal (dalam/inside) dan mana sisi internet (luar/outside).
```cisco
! Fa0/0 adalah interface yang mengarah ke ISP (internet)
R3(config)# interface fa0/0
R3(config-if)# ip nat outside
R3(config-if)# exit

! Fa0/1.10 adalah interface yang mengarah ke VLAN 10 (lokal)
R3(config)# interface fa0/1.10
R3(config-if)# ip nat inside
R3(config-if)# exit

! Fa0/1.20 adalah interface yang mengarah ke VLAN 20 (lokal)
R3(config)# interface fa0/1.20
R3(config-if)# ip nat inside
R3(config-if)# exit
```

Selesai! Sekarang coba kirim paket dari PC mana pun ke Server Google. Gunakan mode simulasi di Packet Tracer untuk melihat keajaibannya. Kamu akan lihat saat paket meninggalkan R3, IP sumbernya berubah dari IP private (`192.168.x.x`) menjadi IP public R3 (`10.20.20.2`).

[Gambar dari proses NAT di Packet Tracer](https://drive.google.com/open?id=1E0l695JREDgLTbmGf_bbnyPJrUWCmdY8&usp=drive_fs)

---

## Static NAT (One-to-One)

Bagaimana jika sebaliknya? Kita punya server di jaringan lokal (misal, web server perusahaan) dan ingin orang dari internet bisa mengaksesnya. Di sinilah **Static NAT** berperan.

Static NAT membuat pemetaan permanen 1-ke-1 antara IP private internal dengan IP public eksternal.

### Topologi Laboratorium

Kita akan menambahkan satu server lokal ke topologi kita.
[Link Topologi Static NAT](https://drive.google.com/open?id=1RHFhZBQTm8xBvDlsMtwbqbbDy8XLOCAg&usp=drive_fs)

**Penambahan:**
-   **Server Lokal**: IP `192.168.30.2/24`, berada di VLAN 30.
-   **Tujuan**: Server ini harus bisa diakses dari internet (misal, dari "Server Google") menggunakan alamat IP public `10.20.20.3`.

### Konfigurasi Static NAT di R3

Kita asumsikan VLAN 30 dan sub-interface `fa0/1.30` sudah dikonfigurasi di R3.

**Langkah 1: Buat Aturan Static NAT.**
Perintah ini sangat lugas: "Buat pemetaan NAT statis. Jika ada paket dari luar menuju IP public `10.20.20.3`, terjemahkan tujuannya ke IP private `192.168.30.2`."
```cisco
R3(config)# ip nat inside source static 192.168.30.2 10.20.20.3
```
*Catatan: Kita menggunakan IP `10.20.20.3` yang berada dalam satu subnet dengan IP utama router, seolah-olah kita punya cadangan IP public dari ISP.*

**Langkah 2: Definisikan Interface NAT.**
Jangan lupa menandai interface baru kita sebagai `ip nat inside`.
```cisco
R3(config)# interface fa0/1.30
R3(config-if)# ip nat inside
R3(config-if)# exit
```
Interface `fa0/0` sudah kita set sebagai `ip nat outside` sebelumnya, jadi tidak perlu diubah.

Sekarang, coba buka browser di "Server Google" dan akses alamat `http://10.20.20.3`. Harusnya kamu akan melihat halaman web dari server lokal kita! Ini membuktikan bahwa permintaan dari luar berhasil diterjemahkan dan diteruskan ke server internal.

[Gambar hasil akses web server via Static NAT](https://drive.google.com/open?id=1f0JMSP5X0Sv_nQJGsT33Laj0zZFSQ6w0&usp=drive_fs)

Itu dia, teman-teman! Kamu sudah berhasil mengkonfigurasi dua jenis NAT yang paling fundamental. Memahami NAT adalah kunci untuk menghubungkan jaringan lokal ke dunia yang lebih luas. Terus berlatih dan tetap semangat!
