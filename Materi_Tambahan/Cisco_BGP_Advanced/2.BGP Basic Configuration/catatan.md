# Basic Config BGP

## 📜 Poin Kunci BGP

Sebelum memulai, ada dua hal penting yang perlu diketahui saat konfigurasi BGP:

  * Kita harus mengkonfigurasi **neighbor (tetangga) secara manual** di kedua sisi router.
  * BGP menggunakan protokol transport **TCP pada port 179** untuk berkomunikasi.

-----

## 🔬 LAB 1: BGP Neighborship (Dasar)

Oke, langsung saja menuju LAB-nya. Berikut topologinya:

**Topology:** [🔗 Klik di sini untuk melihat topologi LAB 1](https://drive.google.com/open?id=1eYFCokQK6vB5YjiR50PWEEsdA7Sd5jFm&usp=drive_fs)

Pada LAB ini, kita akan mengkonfigurasikan BGP Neighborship sederhana (eBGP) dengan 2 router.

  * **R1** akan berada di **AS 100**.
  * **R2** akan berada di **AS 200**.
  * Keduanya terhubung langsung menggunakan jaringan **202.10.10.0/30**.

### R1 (AS 100)

```cisco
conf t
!
int lo1
 ip add 1.1.1.1 255.255.255.255 # --> IP Loopback untuk identitas router
exit
!
int e0/0 
 ip add 202.10.10.1 255.255.255.252 # --> IP Interface fisik ke R2
 no sh
!
router bgp 100
 neighbor 202.10.10.2 remote-as 200 # --> Menentukan IP neighbor & AS-nya
 neighbor 202.10.10.2 description R2-AS200 # --> Deskripsi (opsional, tapi best practice)
!
do sh run | s r b # --> Perintah untuk menunjukkan hanya command BGP yang dijalankan
do sh ip bgp sum # --> Perintah untuk menunjukkan status neighbor
```

### R2 (AS 200)

```cisco
conf t
!
int lo1
 ip add 2.2.2.2 255.255.255.255 # --> IP Loopback untuk identitas router
exit
!
int e0/0 
 ip add 202.10.10.2 255.255.255.252 # --> IP Interface fisik ke R1
 no sh
!
router bgp 200  
# (PERBAIKAN: AS harus 200, bukan 100)
!
 neighbor 202.10.10.1 remote-as 100 # --> Menentukan IP neighbor & AS-nya
 neighbor 202.10.10.1 description R1-AS100 
# (PERBAIKAN: Deskripsi di R2 harusnya menunjuk ke R1)
!
do sh run | s r b # --> Perintah untuk menunjukkan hanya command BGP yang dijalankan
do sh ip bgp sum # --> Perintah untuk menunjukkan status neighbor
```

### Validasi LAB 1

Perintah `do sh ip bgp sum` sangat penting. Jika konfigurasi berhasil, Anda akan melihat status *neighbor* **bukan lagi `Idle` atau `Active`**, melainkan angka yang menunjukkan jumlah *prefix* yang diterima (jika belum ada *prefix* yang di-advertise, akan tertulis `0`).

**Hasil akhir yang diharapkan:**  
📷 [🔗 Lihat hasil output BGP sum](https://drive.google.com/open?id=1mi9vLxoaZ7LDgUsYaOnoHSy2YjrdW0fy&usp=drive_fs)

> **Catatan:**
>
>   * Perintah `s r b` yang dijalankan merupakan singkatan dari `section router bgp`. Ini sangat berguna untuk memfilter konfigurasi yang panjang dan hanya menampilkan konfigurasi BGP saja.

### Ringkasan LAB 1

Pada LAB 1, kita berhasil membangun koneksi BGP *neighbor* dasar (eBGP) antara dua router (R1 dan R2) yang berada di *Autonomous System* (AS) yang berbeda, yaitu AS100 dan AS200. Koneksi ini dibuat langsung antar-interface fisik yang terhubung (e0/0) menggunakan alamat IP `202.10.10.1` dan `202.10.10.2`. Konfigurasi ini adalah fondasi paling sederhana untuk BGP, di mana kita secara manual menentukan *neighbor* dan AS-nya di kedua sisi.

-----

## 🚦 Status Neighbor BGP (BGP Neighbor State)

Masih menggunakan LAB sebelumnya, Anda pasti melihat kolom `State/PfxRcd` pada perintah `sh ip bgp sum`. `State` adalah parameter penting untuk melihat status *peering* BGP. Berikut adalah penjelasan detail mengenai urutan *state* tersebut:

  * **Idle**: *State* paling awal. Router sedang mencari *path* (rute) untuk menjangkau *neighbor* dan menunggu koneksi TCP dimulai.
  * **Connect**: Router telah menemukan *path* ke *neighbor* dan sedang menunggu proses **Three Way Handshake (TCP)** selesai. Ini terjadi karena BGP berjalan di atas TCP.
  * **Active**: Jika proses *Connect* gagal (misalnya, *Three Way Handshake* tidak terbalas), router akan kembali ke *state* ini. Router akan secara aktif mencoba kembali *Three Way Handshake* untuk membangun koneksi. Jika gagal berulang kali, *state* akan bolak-balik antara *Idle* dan *Active*.
  * **OpenSent**: *Three Way Handshake* TCP berhasil. Router telah mengirim **Open Message** (paket BGP pertama) dan sedang menunggu *Open Message* balasan dari *neighbor*.
  * **OpenConfirm**: Router telah menerima *Open Message* balasan dari *neighbor*. Sekarang router sedang menunggu pesan **Keepalive** pertama untuk memvalidasi sesi.
  * **Established**: Ini adalah tujuannya! *State* di mana koneksi BGP sudah berjalan normal. Kedua router sudah bertukar *Open Message* dan *Keepalive*, dan siap untuk bertukar informasi rute (paket *Update*).

> **Catatan:**
>
>   * Ada satu *command* yang sangat berguna untuk *troubleshooting* *state* ini di Cisco, yaitu `debug ip bgp all`. Perintah ini akan menampilkan semua proses BGP yang berjalan di *backend* ke terminal Anda secara *real-time*.
>   * Untuk mematikannya, gunakan `undebug all` atau `no debug ip bgp all`.

📷 [🔗 Lihat diagram urutan state BGP](https://drive.google.com/open?id=1AdRB0D-DCobevhPojzOgVjIfm-HpiUum&usp=drive_fs)

-----

## 📦 Jenis-Jenis Pesan BGP (BGP Message)

Setelah membahas *state*, kita akan mempelajari 'BGP Message'. Sebelumnya sempat disinggung sekilas mengenai *Open Message* dan *Keepalive*. Mari kita bedah keempat jenis pesan BGP:

  * **Open Message**: Digunakan untuk **membentuk koneksi awal** antar *neighbor* BGP.
  * **Update Message**: Digunakan untuk **bertukar informasi *routing***. Isinya bisa berupa rute baru yang ditambahkan, atau rute lama yang ditarik (dihapus).
  * **Keepalive Message**: Digunakan untuk **mengecek apakah *neighbor* masih UP atau Down**. Secara *default*, pesan ini dikirim setiap **60 detik**. Jika *neighbor* tidak merespon selama **180 detik** (*Hold Time*), koneksi BGP akan dianggap *Down*.
  * **Notification Message**: Pesan khusus yang dikirim untuk **memberi informasi bila terjadi Error** (misalnya, salah konfigurasi AS, *Hold Time* tidak cocok, dll.). Pesan ini akan langsung mematikan sesi BGP.

### Tambahan: Isi Paket BGP

Berikut adalah beberapa data penting di dalam jenis-jenis paket tersebut:

1.  **Open Message**

      * Ingat, paket ini hanya dikirim setelah koneksi TCP (Three Way Handshake) tercapai.
      * Informasi penting yang dibawa:
          * **Version**: Versi BGP yang digunakan (umumnya BGP-4).
          * **My AS**: Nomor AS dari router yang mengirim pesan.
          * **Hold Time**: Waktu (detik) yang disepakati untuk menentukan koneksi *Down* (default 180 detik).
          * **BGP Identifier**: *Router ID* BGP (biasanya IP *loopback* tertinggi atau IP *interface* tertinggi yang aktif).
          * **Optional Parameters**: Info tambahan seperti *support* untuk *multi-protocol*, *route refresh*, dll.

2.  **Update Message**

      * Dikirim setiap ada perubahan *routing*.
      * Informasi penting yang dibawa:
          * **NLRI (Network Layer Reachability Information)**: Data mengenai *prefix* (rute) baru yang ditambahkan/di-advertise.
          * **Withdrawn Routes**: Data mengenai *prefix* (rute) lama yang menghilang atau ditarik kembali.
          * **Path Attributes (PA)**: Ini adalah "jantung" BGP. Berisi semua atribut rute seperti AS\_PATH, NEXT\_HOP, LOCAL\_PREF, MED, dll.

3.  **Keepalive Message**

      * Berfungsi sebagai "detak jantung" sesi BGP. Dikirim setiap 60 detik (default) untuk menjaga sesi tetap *Established*.
      * Isi datanya sangat minim, hanya *header* BGP saja.

4.  **Notification Message**

      * Dikirim **hanya ketika ada kesalahan fatal**.
      * Data yang terkandung di dalamnya berupa kode *error* dan penjelasan singkat (misal: `Bad Peer AS`).
      * Setelah mengirim atau menerima pesan ini, sesi BGP akan langsung terputus.

-----

## 🚀 BGP MultiHop

Biasanya, eBGP (External BGP) mengharuskan router-router yang bertetangga (neighbor) terhubung secara fisik (direct). Namun, ada kalanya kita ingin membuat koneksi BGP ke router yang tidak terhubung langsung (terpisah oleh satu atau lebih hop).

Kasus penggunaan yang paling umum adalah peering menggunakan alamat Loopback. Peering via Loopback jauh lebih stabil dan menjadi best practice, karena interface Loopback tidak akan pernah down meskipun salah satu kabel fisik putus (selama masih ada rute alternatif).

Untuk melakukan ini, kita perlu konfigurasi tambahan yang disebut  **eBGP Multihop**.

### 🔬 LAB 2: BGP eBGP Multihop (via Loopback)

📘 [🔗 Lihat topologi LAB 2](https://drive.google.com/open?id=1ZX0az0JEWvKJ4LtTUSyG5lObvKvSzsa0&usp=drive_fs)

Berikut konfigurasi lengkapnya:

### R1 (AS 100)

```cisco
conf t
!
int lo0
 ip add 1.1.1.1 255.255.255.255 # --> IP Loopback
exit
!
int e0/0 
 ip add 202.10.10.1 255.255.255.252 # --> Link 1
 no sh
!
int e0/1
 ip add 202.10.10.5 255.255.255.252 # --> Link 2
 no sh
!
router bgp 100
 no neighbor 202.10.10.2 remote-as 200 --> menghapus konfig BGP di LAB sebelumnya
!
 neighbor 2.2.2.2 remote-as 200
 neighbor 2.2.2.2 description R2-AS200-Loopback
 neighbor 2.2.2.2 update-source lo0
 neighbor 2.2.2.2 ebgp-multihop 2
!
ip route 2.2.2.2 255.255.255.255 202.10.10.2
ip route 2.2.2.2 255.255.255.255 202.10.10.6
!
do sh ip bgp sum
do sh run | s r b
```

### R2 (AS 200)

```cisco
conf t
!
int lo0
 ip add 2.2.2.2 255.255.255.255
exit
!
int e0/0 
 ip add 202.10.10.2 255.255.255.252 # --> Link 1
 no sh
!
int e0/1
 ip add 202.10.10.6 255.255.255.252 # --> Link 2
 no sh
!
router bgp 200
 no neighbor 202.10.10.1 remote-as 100 --> menghapus konfig BGP di LAB sebelumnya
!
 neighbor 1.1.1.1 remote-as 100
 neighbor 1.1.1.1 description R1-AS100-Loopback
 neighbor 1.1.1.1 update-source lo0
 neighbor 1.1.1.1 ebgp-multihop 2
!
ip route 1.1.1.1 255.255.255.255 202.10.10.1
ip route 1.1.1.1 255.255.255.255 202.10.10.5
!
do sh ip bgp sum
do sh run | s r b
```

### Catatan Penting LAB 2

Pada LAB kali ini, ada 3 hal baru yang kita lakukan:

1.  **`neighbor ... update-source lo0`**

      * **Fungsi:** Perintah ini memaksa router untuk menggunakan alamat IP dari `interface lo0` sebagai **alamat sumber** (*source IP*) saat mengirim paket BGP. Tanpa ini, router akan menggunakan IP *interface* fisik (`e0/0` atau `e0/1`), dan R2 tidak akan menerima koneksi BGP dari `1.1.1.1`.

2.  **`neighbor ... ebgp-multihop 2`**

      * **Fungsi:** Secara *default*, paket eBGP memiliki **TTL (Time To Live) = 1**. Ini berarti paket hanya bisa "melompat" 1 kali dan harus diterima langsung oleh *neighbor*. Karena kita *peering* ke *loopback* (yang secara logis ada "di belakang" *interface* fisik), TTL 1 tidak cukup.
      * Perintah ini menaikkan TTL paket BGP menjadi 2 (atau lebih, sesuai angka yang kita masukkan, maks 255), sehingga paket bisa "melompat" dari *interface* fisik R1 ke *interface* fisik R2, lalu "masuk" ke *loopback* R2.

3.  **Dua Perintah `ip route`**

      * **Fungsi:** Ini adalah **static route** biasa. Kita menambahkannya agar R1 "tahu" cara menjangkau `2.2.2.2` (Loopback R2). Kita sengaja menambahkan dua rute (satu via `e0/0` dan satu via `e0/1`) agar jika salah satu kabel putus, koneksi BGP tetap terjalin melalui kabel cadangan. Inilah kehebatan *peering* via *loopback*\!


### Ringkasan LAB 2

Di LAB 2, kita meng-upgrade konfigurasi BGP kita untuk menggunakan *interface loopback* (lo0) sebagai sumber dan tujuan *peering*, bukan lagi IP *interface* fisik. Ini adalah praktik terbaik (*best practice*) karena *loopback* lebih stabil. Untuk membuatnya berfungsi, kita menambahkan dua *static route* di setiap router agar mereka tahu cara menjangkau *loopback* lawan melalui kedua *link* fisik (ini memberi kita *redundancy* / *failover*).

Selain itu, kita menggunakan dua perintah kunci: `neighbor ... update-source lo0` untuk memberi tahu BGP agar menggunakan IP *loopback*-nya sebagai sumber, dan `neighbor ... ebgp-multihop 2` untuk mengizinkan paket BGP melewati *hop* perantara (karena *loopback* tidak terhubung langsung) dengan menaikkan TTL-nya.

> **Catatan Akhir:**
> Bila ketika mengkonfigurasi BGP kita melakukan kesalahan seperti salah memasukkan nomor AS (misal, `router bgp 65001` padahal seharusnya `router bgp 100`), kita harus menghapus seluruh proses BGP terlebih dahulu. Gunakan perintah `no router bgp <nomor AS yang salah>` untuk menghapus total konfig BGP tersebut, baru masukkan kembali dengan `router bgp <nomor AS yang benar>`. Di Cisco IOS, *default*-nya hanya bisa memproses satu BGP AS dalam satu waktu.