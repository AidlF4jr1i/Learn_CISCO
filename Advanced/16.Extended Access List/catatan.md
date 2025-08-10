# Extended Access List: Kontrol Jaringan Lebih Presisi

Nah, setelah kita nyaman dengan Standard ACL, saatnya naik level ke **Extended Access List**! Kalau di Standard ACL kita cuma bisa menyaring paket berdasarkan "siapa pengirimnya" (Source Address), di Extended ACL kita punya kekuatan super untuk jadi lebih spesifik.

Dengan Extended ACL, kita bisa membuat aturan berdasarkan kombinasi dari:
1.  **Alamat IP Sumber** (*Source Address*)
2.  **Alamat IP Tujuan** (*Destination Address*)
3.  **Protokol** (misalnya `ip`, `tcp`, `udp`, `icmp`)
4.  **Nomor Port** (misalnya port `80` untuk HTTP, `443` untuk HTTPS, `21` untuk FTP)

Ini artinya, kita bisa membuat aturan yang sangat detail, seperti "Izinkan VLAN 10 mengakses web server, tapi hanya lewat HTTPS, dan blokir semua akses lainnya." Keren, kan?

---

## Memahami Sintaks Extended ACL

Struktur perintahnya sedikit lebih panjang, tapi logikanya sederhana.

```cisco
access-list <100-199> <permit|deny> <protokol> <sumber> <tujuan> [operator] [port]
```

-   **`<100-199>`**: Nomor ID untuk Extended ACL.
-   **`<protokol>`**: `ip` (semua jenis trafik IP), `tcp`, `udp`, atau `icmp`.
-   **`<sumber>`**: Alamat IP sumber (bisa network, host, atau `any`).
-   **`<tujuan>`**: Alamat IP tujuan (bisa network, host, atau `any`).
-   **`[operator] [port]`**: Bagian opsional untuk protokol TCP/UDP.
    -   Operator: `eq` (equal/sama dengan), `gt` (greater than), `lt` (less than).
    -   Port: Nomor port atau nama aliasnya (misal: `80` atau `www`).

---

## Studi Kasus 1: Akses Tersegmentasi ke Server

Kita akan tetap menggunakan topologi yang sama.
[Link Topologi](https://drive.google.com/open?id=16GPa4Rn_7NojdeA03cZxkZL5johI0F6i&usp=drive_fs)

**Skenario:**
Kita akan membuat aturan di **R1** untuk mengontrol akses dari semua VLAN ke beberapa server hipotetis di jaringan R2.
-   **Server 1**: `192.168.1.2`
-   **Server 2**: `192.168.1.3`
-   **Server 3**: `192.168.1.4`

**Aturan yang Diinginkan:**
1.  Blokir semua akses **HTTP (port 80)** ke semua server dari semua VLAN.
2.  Semua VLAN (`any`) boleh mengakses **Server 1**.
3.  Hanya **VLAN 20** yang boleh mengakses **Server 2**.
4.  Hanya **VLAN 30** yang boleh mengakses **Server 3**.
5.  Trafik lainnya diizinkan (misal: ping, dll).

#### Langkah Konfigurasi

Pertama, kita hapus dulu ACL yang sebelumnya menempel di R2 agar tidak terjadi konflik.
```cisco
R2(config)# interface fa0/0
R2(config-if)# no ip access-group PERMIT_VLAN30_Only out
```

Sekarang, kita buat ACL baru di **R1**. Kita akan menempatkannya di interface **Fa0/1** dengan arah **`out`**, karena kita ingin menyaring paket yang akan keluar dari R1 menuju R2.

```cisco
R1(config)# access-list 100 deny tcp any any eq 80
R1(config)# access-list 100 permit ip any host 192.168.1.2
R1(config)# access-list 100 permit ip 192.168.20.0 0.0.0.255 host 192.168.1.3
R1(config)# access-list 100 permit ip 192.168.30.0 0.0.0.255 host 192.168.1.4

R1(config)# interface fa0/1
R1(config-if)# ip access-group 100 out
```

**Penjelasan Aturan:**
-   **Baris 1**: Tolak (`deny`) paket `tcp` dari `any` (siapa saja) ke `any` (tujuan mana saja) jika port tujuannya adalah `80` (HTTP). Aturan ini kita letakkan paling atas karena ACL diproses secara berurutan.
-   **Baris 2**: Izinkan (`permit`) semua jenis trafik `ip` dari `any` (siapa saja) ke host spesifik `192.168.1.2` (Server 1).
-   **Baris 3**: Izinkan trafik `ip` dari network `192.168.20.0/24` (VLAN 20) ke `host 192.168.1.3` (Server 2).
-   **Baris 4**: Izinkan trafik `ip` dari network `192.168.30.0/24` (VLAN 30) ke `host 192.168.1.4` (Server 3).
-   **Implicit Deny**: Ingat, di akhir ACL selalu ada `deny ip any any` yang tersembunyi. Karena itu, akses VLAN 10 ke Server 2 dan 3 akan otomatis terblokir, sesuai keinginan kita.

Sekarang, tinggal lakukan uji coba. Pasti sudah berjalan sesuai skenario, muehehe.

---

## Studi Kasus 2: Akses Eksklusif HTTPS

Mari kita buat lebih menantang!

**Skenario:**
Kita akan mengganti aturan di R1 dengan yang baru.
-   **Nama ACL**: `AKSES_WEB_AMAN` (menggunakan Named ACL)

**Aturan yang Diinginkan:**
1.  **VLAN 10** hanya boleh mengakses **Server 1 (`192.168.1.2`)** melalui protokol **HTTPS (port 443)**.
2.  Akses lain dari **VLAN 10** ke **Server 1** (seperti ping/ICMP) harus diblokir.
3.  VLAN lain (**VLAN 20 & 30**) sama sekali tidak boleh mengakses **Server 1**.
4.  Semua VLAN harus tetap bisa mengakses **Server 2 (`192.168.1.3`)** dan **Server 3 (`192.168.1.4`)**.

#### Langkah Konfigurasi di R1

Kita akan membuat *Named Extended ACL* agar lebih mudah dibaca.

```cisco
R1(config)# ip access-list extended AKSES_WEB_AMAN

R1(config-ext-nacl)# permit tcp 192.168.10.0 0.0.0.255 host 192.168.1.2 eq 443
R1(config-ext-nacl)# deny ip any host 192.168.1.2
R1(config-ext-nacl)# permit ip any host 192.168.1.3
R1(config-ext-nacl)# permit ip any host 192.168.1.4

R1(config-ext-nacl)# exit
R1(config)# interface fa0/1
R1(config-if)# no ip access-group 100 out
R1(config-if)# ip access-group AKSES_WEB_AMAN out
```

**Penjelasan Logika Aturan:**
-   **Baris 1**: Izinkan (`permit`) paket `tcp` dari `VLAN 10` ke `Server 1` jika tujuannya adalah port `443` (HTTPS). Ini adalah aturan yang paling spesifik, jadi kita letakkan di paling atas.
-   **Baris 2**: Tolak (`deny`) semua jenis trafik `ip` dari `any` (siapa saja) ke `Server 1`. Karena aturan pertama sudah mengizinkan HTTPS dari VLAN 10, paket tersebut akan lolos duluan. Paket lain (seperti ping dari VLAN 10, atau semua paket dari VLAN 20/30) yang tujuannya ke Server 1 akan kena blokir di sini.
-   **Baris 3 & 4**: Izinkan (`permit`) semua jenis trafik `ip` dari `any` (siapa saja) ke `Server 2` dan `Server 3`. Ini memastikan semua VLAN tetap bisa mengakses kedua server ini.

**Saatnya Uji Coba:**
1.  Dari PC di VLAN 10, buka browser dan akses `https://192.168.1.2`. Seharusnya berhasil.
2.  Coba `ping 192.168.1.2` dari VLAN 10. Seharusnya gagal.
3.  Coba akses (ping atau http/https) ke `192.168.1.2` dari VLAN 20 atau 30. Semua pasti gagal.
4.  Coba `ping` ke Server 2 dan Server 3 dari semua VLAN. Semua seharusnya berhasil.

---

Sekian dari pembelajaran kita mengenai Access Control List. Dengan memahami Standard dan Extended ACL, kamu sudah punya bekal yang sangat kuat untuk merancang dan mengamankan jaringan menggunakan perangkat Cisco.

Semoga ilmunya bermanfaat dan membuatmu semakin penasaran untuk menjelajahi dunia jaringan lebih dalam lagi. Tetap semangat!