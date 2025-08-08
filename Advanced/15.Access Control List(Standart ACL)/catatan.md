# Mengenal Access Control List (ACL) di Cisco

Halo, teman-teman! Kali ini kita akan menyelami salah satu fitur keamanan paling fundamental di perangkat Cisco, yaitu **Access Control List** atau yang sering kita sebut **ACL**.

Secara sederhana, **ACL adalah sekumpulan aturan (rules) yang kita buat untuk menyaring paket data yang lalu-lalang di jaringan.** Tujuannya jelas: memblokir akses yang tidak sah dan mengizinkan koneksi yang memang kita kehendaki. Kalau kamu pernah bermain dengan Mikrotik, fitur ini sangat mirip dengan *Firewall*. Di Cisco, ACL menjadi garda terdepan untuk mengamankan jaringan kita.

Ada dua jenis ACL yang paling umum digunakan:
- **Standard ACL**: Penyaringan paket berdasarkan alamat IP sumber (*source address*) saja.
- **Extended ACL**: Penyaringan yang lebih canggih, bisa berdasarkan IP sumber, IP tujuan, protokol (TCP/UDP), hingga nomor port.

Yuk, kita bedah satu per satu!

---

## Standard ACL

**Standard ACL** adalah jenis ACL yang paling dasar. Aturan yang dibuat hanya melihat "siapa" yang mengirim paket (alamat IP sumber) tanpa peduli "mau ke mana" paket itu (tujuan) atau "mau ngapain" (port/protokol).

### Cara Konfigurasi Standard ACL

Berikut adalah langkah-langkah dasar untuk membuat dan menerapkan Standard ACL:

1.  **Masuk ke Global Configuration Mode:**
    ```cisco
    en
    conf t
    ```

2.  **Buat Aturan ACL:**
    Gunakan nomor identifikasi (ID) dari **1 hingga 99** untuk Standard ACL.
    ```cisco
    access-list <nomor_id_1-99> <permit | deny | remark> <source_address> <wildcard_mask>
    ```
    -   **`permit`**: Mengizinkan paket.
    -   **`deny`**: Memblokir paket.
    -   **`remark`**: Memberikan komentar/catatan pada rule (tidak dieksekusi, hanya untuk dokumentasi).

    Untuk *source address*, kita punya beberapa pilihan:
    -   **`A.B.C.D`**: IP network tertentu (misal: `192.168.10.0`).
    -   **`host A.B.C.D`**: Satu alamat IP spesifik (misal: `host 192.168.10.5`).
    -   **`any`**: Mewakili semua alamat IP sumber.

3.  **Izinkan Trafik Lainnya (PENTING!):**
    Secara *default*, di akhir setiap ACL ada aturan tersembunyi yaitu `deny any` (tolak semua). Jadi, jika kita hanya membuat aturan `deny`, semua trafik lain akan ikut terblokir! Untuk mengatasinya, kita harus secara eksplisit mengizinkan trafik lainnya.
    ```cisco
    access-list <nomor_id_yang_sama> permit any
    ```

4.  **Terapkan ACL pada Interface:**
    Setelah aturan dibuat, kita harus menempelkannya ke sebuah interface (port fisik atau virtual).
    ```cisco
    interface <nama_interface>
    ip access-group <nomor_id_yang_dibuat> <in | out>
    ```
    -   **`in`**: Menerapkan ACL pada paket yang **masuk** ke interface tersebut.
    -   **`out`**: Menerapkan ACL pada paket yang **keluar** dari interface tersebut.

5.  **Verifikasi Konfigurasi:**
    ```cisco
    do show access-lists
    ```

### Studi Kasus: Blokir Akses dari VLAN 20

Mari kita praktikkan dengan topologi berikut:
[Link Topologi](https://drive.google.com/open?id=16GPa4Rn_7NojdeA03cZxkZL5johI0F6i&usp=drive_fs)

**Skenario:** Kita akan memblokir seluruh perangkat di **VLAN 20** (`192.168.20.0/24`) agar tidak bisa mengakses server di jaringan R2. VLAN lain (10 dan 30) harus tetap bisa berkomunikasi.

**Konfigurasi Awal (Asumsi sudah dilakukan):**
* **R1**: Konfigurasi IP Address, Inter-VLAN routing, DHCP Server untuk setiap VLAN, dan static route ke jaringan server.
* **R2**: Konfigurasi IP Address dan static route ke semua jaringan VLAN di R1.
* **Switch**: Konfigurasi VLAN dan Trunking.

---

#### Konfigurasi Lengkap

<details>
<summary>Klik untuk melihat Konfigurasi Awal R1, Switch, dan R2</summary>

**R1:**
```cisco
ip dhcp pool VLAN10
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8
!
ip dhcp pool VLAN20
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 dns-server 8.8.8.8
!
ip dhcp pool VLAN30
 network 192.168.30.0 255.255.255.0
 default-router 192.168.30.1
 dns-server 8.8.8.8
!
interface FastEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
!
interface FastEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
!
interface FastEthernet0/0.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0
!
interface FastEthernet0/1
 ip address 10.10.10.1 255.255.255.0
!
ip route 192.168.1.0 255.255.254.0 10.10.10.2
```

**Switch:**
```cisco
interface FastEthernet0/1
 switchport mode trunk
!
interface FastEthernet0/2
 switchport access vlan 10
 switchport mode access
!
interface FastEthernet0/4
 switchport access vlan 20
 switchport mode access
!
interface FastEthernet0/6
 switchport access vlan 30
 switchport mode access
!
```

**R2:**
```cisco
interface FastEthernet0/0
 ip address 192.168.1.1 255.255.254.0
!
interface FastEthernet0/1
 ip address 10.10.10.2 255.255.255.0
!
ip route 192.168.10.0 255.255.255.0 10.10.10.1
ip route 192.168.20.0 255.255.255.0 10.10.10.1
ip route 192.168.30.0 255.255.255.0 10.10.10.1
```
</details>

---

#### Langkah-langkah Konfigurasi ACL di R2

Kita akan menempatkan ACL di **R2** pada interface **Fa0/0** dengan arah **`out`**. Kenapa? Karena kita ingin menyaring paket *tepat sebelum* paket tersebut sampai ke server.

1.  **Buat aturan untuk memblokir VLAN 20:**
    ```cisco
    R2(config)# access-list 1 deny 192.168.20.0 0.0.0.255
    ```
    *Wildcard mask `0.0.0.255`* berarti kita mencocokkan 3 oktet pertama (`192.168.20`) secara presisi, dan tidak peduli dengan oktet terakhir.

2.  **Buat aturan untuk mengizinkan semua trafik lainnya:**
    ```cisco
    R2(config)# access-list 1 permit any
    ```

3.  **Terapkan ACL ke interface Fa0/0:**
    ```cisco
    R2(config)# interface fa0/0
    R2(config-if)# ip access-group 1 out
    ```

#### Hasil Percobaan

Sekarang, coba lakukan `ping` dari PC di VLAN 20 ke Server. Pasti gagal! Lalu coba `ping` dari VLAN 10 atau 30, pasti berhasil.

Untuk melihat buktinya di router, gunakan perintah:
```cisco
R2# show access-lists
```

Outputnya akan terlihat seperti ini:
```
Standard IP access list 1
    10 deny   192.168.20.0, wildcard bits 0.0.0.255 (4 match(es))
    20 permit any (8 match(es))
```
-   `(4 match(es))` pada baris `deny` menunjukkan ada 4 paket dari VLAN 20 yang berhasil ditangkap dan diblokir.
-   `(8 match(es))` pada baris `permit` menunjukkan ada 8 paket dari jaringan lain (VLAN 10 & 30) yang diizinkan lewat.

---

## Mengelola ACL dengan Sequence Number

Perhatikan angka `10` dan `20` pada output `show access-lists` di atas. Itu adalah **Sequence Number**, atau nomor urutan. Cisco memproses ACL dari nomor urut terkecil ke terbesar. Secara default, setiap aturan baru akan ditambahkan di urutan paling akhir.

Bagaimana jika kita mau menyisipkan aturan baru di tengah-tengah? Atau menghapus satu aturan spesifik tanpa menghapus semuanya? Di sinilah kita perlu masuk ke mode konfigurasi ACL spesifik.

**Cara Menghapus Satu Aturan Spesifik:**
Jangan pernah gunakan `no access-list <id> ...` di global config, karena itu akan menghapus **semua** aturan dalam ACL tersebut! Gunakan cara ini:
```cisco
R2(config)# ip access-list standard 1
R2(config-std-nacl)# no <nomor_urut_yang_mau_dihapus>
```

**Cara Menambahkan Aturan dengan Nomor Urut Tertentu:**
Misalnya, kita mau menambahkan aturan baru untuk memblokir satu PC dari VLAN 10 (IP: `192.168.10.2`) dan menempatkannya di antara aturan `deny` dan `permit` yang sudah ada (misal, urutan 15).

```cisco
R2(config)# ip access-list standard 1
R2(config-std-nacl)# 15 deny host 192.168.10.2
```

Sekarang, coba cek lagi dengan `show access-lists`. Aturan baru tersebut sudah tersisip dengan rapi!

---

## Named ACL: Memberi Nama pada ACL

Menggunakan nomor ID (seperti `1`, `10`, `50`) memang bisa, tapi di dunia nyata, kita akan kesulitan mengingat fungsi dari `access-list 57` itu untuk apa. Solusinya adalah **Named ACL**, yaitu memberikan nama yang deskriptif pada ACL kita.

### Studi Kasus: Hanya Izinkan VLAN 30

**Skenario:** Kita akan membuat aturan baru yang *hanya* mengizinkan trafik dari **VLAN 30** ke server. Aturan lama (ACL nomor 1) akan kita nonaktifkan dari interface, tapi tidak kita hapus.

1.  **Cabut ACL lama dari interface Fa0/0:**
    ```cisco
    R2(config)# interface fa0/0
    R2(config-if)# no ip access-group 1 out
    ```

2.  **Buat Named ACL baru:**
    ```cisco
    R2(config)# ip access-list standard PERMIT_VLAN30_ONLY
    ```
    Perhatikan *prompt*-nya berubah menjadi `Router(config-std-nacl)#`.

3.  **Masukkan aturannya:**
    ```cisco
    R2(config-std-nacl)# permit 192.168.30.0 0.0.0.255
    ```
    Kita tidak perlu menambahkan `deny any` di sini, karena kita memang ingin memblokir semua trafik lain secara *default*.

4.  **Terapkan Named ACL baru ke interface:**
    ```cisco
    R2(config-std-nacl)# exit
    R2(config)# interface fa0/0
    R2(config-if)# ip access-group PERMIT_VLAN30_ONLY out
    ```

Sekarang, hanya PC dari VLAN 30 yang bisa `ping` ke server. Coba lakukan tes dan lihat `show access-lists`. Kamu akan melihat *counter match* pada ACL `PERMIT_VLAN30_ONLY` akan bertambah.

```
Standard IP access list PERMIT_VLAN30_ONLY
    10 permit 192.168.30.0, wildcard bits 0.0.0.255 (4 match(es))
```

Okeh! Sekarang kamu sudah berhasil menguasai dasar-dasar Standard ACL di Cisco. Konsep ini adalah fondasi penting untuk keamanan jaringan. Tetap semangat dan mari kita lanjut ke materi berikutnya!
