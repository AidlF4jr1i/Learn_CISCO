# OSPF
Selamat datang di panduan konfigurasi **OSPF (Open Shortest Path First)**! 

OSPF adalah protokol **Link-State** yang berstandar terbuka (**Open Standard**), artinya dapat digunakan di perangkat router dari berbagai vendor, tidak seperti **EIGRP** yang merupakan proprietary Cisco. Tujuan utama OSPF adalah agar setiap router dalam jaringan dapat "mempelajari" topologi jaringan secara mandiri dan dinamis, sehingga ketika ada penambahan jaringan baru atau perubahan, semua router akan secara otomatis memperbarui tabel routing mereka.

Dalam panduan ini, kita akan membahas:
1.  Perbedaan mendasar OSPF dan EIGRP.
2.  Praktik konfigurasi OSPF dasar dengan 2 router.
3.  Studi kasus lanjutan dengan 4 router, termasuk integrasi VLAN.
4.  Implementasi OSPF **Multi-Area** untuk skalabilitas jaringan.

Mari kita mulai!

---

## OSPF vs. EIGRP: Perbedaan Utama

Meskipun OSPF dan EIGRP memiliki fungsi yang sama—yaitu sebagai protokol *routing* dinamis—ada perbedaan fundamental yang wajib diketahui:

-   **EIGRP (Enhanced Interior Gateway Routing Protocol):**
    -   **Proprietary Cisco:** Hanya bisa digunakan pada perangkat Cisco.
    -   **Hybrid Protocol:** Menggabungkan karakteristik dari *Distance Vector* dan *Link-State*.
    -   **Konvergensi Cepat:** Dikenal sangat cepat dalam menemukan rute alternatif jika terjadi kegagalan jaringan.
    -   **Konfigurasi:** Menggunakan **Autonomous System (AS) Number** yang harus sama di semua router dalam satu domain *routing*.

-   **OSPF (Open Shortest Path First):**
    -   **Open Standard:** Kompatibel dengan semua vendor router (MikroTik, Juniper, dll.).
    -   **Link-State Protocol:** Setiap router memiliki "peta" lengkap dari topologi jaringan.
    -   **Skalabilitas Tinggi:** Sangat andal untuk jaringan skala besar berkat penggunaan konsep **Area**.
    -   **Konfigurasi:** Menggunakan **Process ID** yang tidak harus sama antar router dan konsep **Area** untuk segmentasi jaringan.

---

## Praktik Dasar: OSPF dengan 2 Router

Kita akan mulai dengan skenario dasar untuk memahami cara kerja OSPF.

**Topologi:**
-   2 Router (R1 dan R2) yang terhubung langsung.
-   Masing-masing router terhubung ke jaringan klien (PC).
-   [Lihat topologi di sini](https://drive.google.com/open?id=1-fq8Tij9YjnW9XNZLMGr2d90W4FVClRi&usp=drive_fs)

### 📐 Alokasi IP Address

-   **R1**:
    -   Ke Client: `192.168.1.1/24`
    -   Ke R2: `10.10.10.1/24`
-   **R2**:
    -   Ke Client: `192.168.2.1/24`
    -   Ke R1: `10.10.10.2/24`

> 📌 **Catatan:** IP Address pada PC klien harus disesuaikan agar berada dalam satu subnet dengan interface router yang terhubung.

### ⚙️ Sintaks Dasar Konfigurasi OSPF

```shell
enable
configure terminal
router ospf <process-id>
network <network-address> <wildcard-mask> area <area-id>
```

### ⚙️ Konfigurasi pada Router

**Router 1 (R1):**
```shell
enable
configure terminal
!
router ospf 1
 network 10.10.10.0 0.0.0.255 area 0
 network 192.168.1.0 0.0.0.255 area 0
!
end
```

**Router 2 (R2):**
```shell
enable
configure terminal
!
router ospf 2
 network 10.10.10.0 0.0.0.255 area 0
 network 192.168.2.0 0.0.0.255 area 0
!
end
```

### 🧠 Poin Penting (Wajib Tahu!)

1.  **Process ID:** Angka `1` dan `2` pada `router ospf <process-id>` adalah nomor proses yang bersifat **lokal** di setiap router. Angka ini **tidak harus sama** antar router, berbeda dengan AS Number pada EIGRP.
2.  **Wildcard Mask:** Ini adalah kebalikan dari subnet mask. Cara menghitungnya adalah dengan mengurangi `255.255.255.255` dengan subnet mask yang digunakan.
    -   Contoh: Untuk prefix `/24` (Subnet Mask `255.255.255.0`), perhitungannya:
        `255.255.255.255 - 255.255.255.0` = `0.0.0.255`.
3.  **Area:** `area 0` adalah area utama atau **backbone area**. Untuk jaringan sederhana, kita menggunakan area 0. Untuk jaringan yang lebih kompleks, area 0 wajib ada sebagai pusat yang menghubungkan area-area lainnya.

Setelah konfigurasi selesai, coba lakukan `ping` dari PC di jaringan R1 ke PC di jaringan R2. Jika berhasil, OSPF sudah berjalan dengan benar!

---

## Studi Kasus Lanjutan: 4 Router + VLAN

Sekarang, kita akan memperluas jaringan dan menambahkan kompleksitas dengan VLAN.

**Topologi:**
-   4 Router yang terhubung secara berurutan.
-   Router 4 akan terhubung ke sebuah switch yang mengelola dua VLAN.
-   [Lihat topologi di sini](https://drive.google.com/open?id=1ZAlLES4Y_1UK_N185Jdt5i6PUx-Y5b-F&usp=drive_fs)

### 📐 Alokasi IP Address

-   **R1**:
    -   Client: `192.168.1.1/24`
    -   Ke R2: `10.10.10.1/24`
-   **R2**:
    -   Client: `192.168.2.1/24`
    -   Ke R1: `10.10.10.2/24`
    -   Ke R3: `10.20.20.1/24`
-   **R3**:
    -   Client: `192.168.3.1/24`
    -   Ke R2: `10.20.20.2/24`
    -   Ke R4: `10.30.30.1/24`
-   **R4**:
    -   Client: `192.168.4.1/24`
    -   Ke R3: `10.30.30.2/24`
-   **VLAN**:
    -   VLAN 5: `192.168.5.1/24`
    -   VLAN 6: `192.168.6.1/24`

### ⚙️ Konfigurasi OSPF Lanjutan

*Asumsi: Konfigurasi dasar pada R1 dan R2 sudah dilakukan.*

**Router 2 (R2):**
```shell
router ospf 2
 network 10.20.20.0 0.0.0.255 area 0
```

**Router 3 (R3):**
```shell
router ospf 3
 network 10.20.20.0 0.0.0.255 area 0
 network 10.30.30.0 0.0.0.255 area 0
 network 192.168.3.0 0.0.0.255 area 0
```

**Router 4 (R4):**
```shell
router ospf 4
 network 10.30.30.0 0.0.0.255 area 0
 network 192.168.4.0 0.0.0.255 area 0
```

### ⚙️ Konfigurasi R4 (VLAN & DHCP)

Router 4 akan bertindak sebagai "Router-on-a-Stick" untuk melayani *routing* antar-VLAN dan menyediakan IP melalui DHCP.

```shell
! Konfigurasi Sub-Interface untuk VLAN
interface FastEthernet1/0.5
 encapsulation dot1q 5
 ip address 192.168.5.1 255.255.255.0
!
interface FastEthernet1/0.6
 encapsulation dot1q 6
 ip address 192.168.6.1 255.255.255.0
!
! Konfigurasi DHCP Server untuk setiap VLAN
ip dhcp pool VLAN5
 network 192.168.5.0 255.255.255.0
 default-router 192.168.5.1
 dns-server 8.8.8.8
!
ip dhcp pool VLAN6
 network 192.168.6.0 255.255.255.0
 default-router 192.168.6.1
 dns-server 8.8.8.8
```

### ⚙️ Konfigurasi Switch

```shell
enable
configure terminal
!
vlan 5
name IT
exit
vlan 6
name HR
exit
!
! Konfigurasi Trunk Port ke Router
interface FastEthernet0/5
 switchport mode trunk
!
! Alokasi Access Port untuk VLAN
interface range FastEthernet0/1-2
 switchport mode access
 switchport access vlan 5
!
interface range FastEthernet0/3-4
 switchport mode access
 switchport access vlan 6
!
end
```

### 📡 Iklankan Jaringan VLAN ke OSPF (di R4)

Agar seluruh jaringan tahu tentang VLAN 5 dan 6, kita harus mengiklankannya di OSPF R4.

```shell
router ospf 4
 network 192.168.5.0 0.0.0.255 area 0
 network 192.168.6.0 0.0.0.255 area 0
```

---

## Konfigurasi OSPF Multi-Area

Salah satu keunggulan utama OSPF adalah kemampuannya untuk membagi jaringan besar menjadi beberapa **area**. Ini berguna untuk mengurangi beban kerja CPU pada router, membatasi penyebaran informasi *link-state*, dan membuat jaringan lebih terorganisir.

Aturan utamanya adalah: **semua area harus terhubung langsung ke `area 0` (backbone).**

Router yang menghubungkan `area 0` dengan area lain (seperti R3 dalam kasus kita nanti) disebut **Area Border Router (ABR)**. Tugasnya adalah meringkas dan meneruskan informasi rute antar-area.

> 📌 **Catatan Penting: ABR vs. ASBR**
>
> -   **ABR (Area Border Router):** Menghubungkan area OSPF internal (misal: Area 1 ke Area 0).
> -   **ASBR (Autonomous System Boundary Router):** Menghubungkan domain OSPF dengan jaringan eksternal yang menggunakan protokol *routing* berbeda (misal: OSPF ke EIGRP atau BGP).
>
> Dalam skenario kita, R3 akan menjadi **ABR**.

### 🎯 Skenario Modifikasi

Kita akan memodifikasi topologi 4 router sebelumnya:
-   Jaringan yang dikelola R3 dan R4 akan masuk ke **Area 1**.
-   R3 akan menjadi **ABR** yang menghubungkan Area 1 ke Area 0.

### ⚙️ Perubahan Konfigurasi

**Router 3 (R3):**
```shell
enable
configure terminal
router ospf 3
 ! Hapus network yang akan dipindah ke area 1
 no network 10.30.30.0 0.0.0.255 area 0
 no network 192.168.3.0 0.0.0.255 area 0
 !
 ! Tambahkan kembali network tersebut ke area 1
 network 10.30.30.0 0.0.0.255 area 1
 network 192.168.3.0 0.0.0.255 area 1
end
```

**Router 4 (R4):**
```shell
enable
configure terminal
 ! Hapus konfigurasi OSPF lama
 no router ospf 4
 !
 ! Buat konfigurasi OSPF baru dengan semua network di area 1
 router ospf 4
  network 10.30.30.0 0.0.0.255 area 1
  network 192.168.4.0 0.0.0.255 area 1
  network 192.168.5.0 0.0.0.255 area 1
  network 192.168.6.0 0.0.0.255 area 1
end
```

### ✅ Verifikasi dan Troubleshooting

Gunakan perintah `show ip route` pada R2 dan R4 untuk melihat perubahannya.

**Tabel Routing R2 (Berada di Area 0):**
```
O IA  10.30.30.0/24 [110/2] via 10.20.20.2, 00:05:02, FastEthernet1/0
O IA  192.168.3.0/24 [110/2] via 10.20.20.2, 00:02:02, FastEthernet1/0
O IA  192.168.4.0/24 [110/3] via 10.20.20.2, 00:00:45, FastEthernet1/0
O IA  192.168.5.0/24 [110/3] via 10.20.20.2, 00:00:35, FastEthernet1/0
O IA  192.168.6.0/24 [110/3] via 10.20.20.2, 00:00:25, FastEthernet1/0
```
-   Perhatikan kode `O IA`. `IA` singkatan dari **Inter-Area**, yang membuktikan bahwa rute tersebut dipelajari dari area OSPF yang berbeda (Area 1) melalui ABR (R3).

**Tabel Routing R4 (Berada di Area 1):**
```
O IA  10.10.10.0/24 [110/3] via 10.30.30.1, 00:00:28, FastEthernet0/0
O IA  10.20.20.0/24 [110/2] via 10.30.30.1, 00:00:28, FastEthernet0/0
O IA  192.168.1.0/24 [110/4] via 10.30.30.1, 00:00:28, FastEthernet0/0
O IA  192.168.2.0/24 [110/3] via 10.30.30.1, 00:00:28, FastEthernet0/0
O     192.168.3.0/24 [110/2] via 10.30.30.1, 00:00:28, FastEthernet0/0
```
-   R4 juga melihat jaringan dari Area 0 sebagai rute `O IA`. Rute ke `192.168.3.0/24` (jaringan klien R3) dilihat sebagai rute intra-area biasa (`O`) karena keduanya berada di Area 1.

**Jika perubahan belum muncul:**
1.  **Reset Proses OSPF:** Lakukan pada semua router untuk mempercepat pembaruan.
    ```shell
    clear ip ospf process
    ```
    Ketik `yes` untuk konfirmasi.
2.  **Simpan & Reload:** Jika cara pertama tidak berhasil, simpan konfigurasi dan restart router.
    ```shell
    write memory
    reload
    ```

---

Selamat! Anda telah berhasil mengonfigurasi OSPF dari skenario dasar hingga implementasi multi-area yang lebih kompleks. Sekarang, coba lakukan `ping` antar PC yang berada di area berbeda untuk memastikan seluruh jaringan dapat berkomunikasi.

Teruslah berlatih dan tetap semangat!