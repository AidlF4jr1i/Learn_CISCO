# 🛰️ Pengenalan BGP (Border Gateway Protocol)

**Border Gateway Protocol (BGP)** adalah sebuah *routing protocol* berbasis **EGP (Exterior Gateway Protocol)** yang digunakan untuk menghubungkan jaringan antar **Autonomous System (AS)** — sederhananya, antar organisasi atau perusahaan.

## Apa itu Autonomous System (AS)?
Sebuah **Autonomous System** adalah sekumpulan jaringan (network) yang dikelola oleh satu entitas administratif dengan kebijakan routing yang sama.

Contoh paling nyata dari komunikasi antar-AS adalah **Internet** itu sendiri.  
Internet merupakan kumpulan dari berbagai AS yang saling terhubung satu sama lain menggunakan BGP.

## Apa itu AS Number?
**AS Number (ASN)** adalah nomor unik yang digunakan untuk mengidentifikasi setiap Autonomous System di Internet.  
Bisa diibaratkan seperti *kartu nama digital* untuk sebuah jaringan.

## Bagaimana cara mendapatkan AS Number?
Untuk memperoleh ASN, kamu perlu mendaftarkannya melalui organisasi yang mengelola Internet Number Resources di setiap wilayah (*Regional Internet Registry / RIR*). Berikut daftarnya:

- **AFRINIC** – Afrika  
- **APNIC** – Asia Pasifik (termasuk Indonesia)  
- **ARIN** – Amerika Utara  
- **LACNIC** – Amerika Latin & Karibia  
- **RIPE NCC** – Eropa, Timur Tengah, dan sebagian Asia Tengah

---

# 🕓 Kapan Perlu Menggunakan BGP?

Tidak di semua kondisi jaringan kita memerlukan **BGP (Border Gateway Protocol)**.  
Sebagai contoh, ketika kita berada di lingkungan **rumahan** atau **kantor kecil**, apakah kita memerlukan BGP?  
Jawabannya **tidak** — karena untuk komunikasi internal, kita cukup menggunakan **IGP (Interior Gateway Protocol)** seperti **OSPF**, **EIGRP**, atau **IS-IS**, yang sudah mampu mengatur routing di dalam jaringan lokal.

Untuk memahami lebih jelas, mari kita bahas beberapa **skenario umum** penggunaan BGP.

---

## 🧩 Skenario 1
**Kondisi:**
- Mendapat 1 IP publik dari ISP  
- Menggunakan NAT/PAT agar client bisa mengakses internet  
- Router pelanggan memiliki *default route* ke ISP  

**Pertanyaan:**  
> Apakah perlu menggunakan BGP?

**Jawaban:**  
❌ **Tidak perlu!**  
Pada skenario ini, tujuan utama hanya agar client di dalam jaringan lokal dapat mengakses internet.  
Semua kebutuhan routing internal dapat ditangani dengan protokol IGP seperti **OSPF** atau **EIGRP**.  
Tidak ada kebutuhan untuk pertukaran routing antar-AS (Autonomous System), sehingga **BGP tidak diperlukan**.

---

## 🧩 Skenario 2
**Kondisi:**
- Mendapat 1 IP publik dari ISP  
- Memiliki **Mail Server** dan **Web Server** yang harus bisa diakses dari luar (internet)  
- Menggunakan **Destination NAT (DST-NAT)** pada router  

**Pertanyaan:**  
> Apakah perlu menggunakan BGP?

**Jawaban:**  
❌ **Tidak perlu.**  
Pada kasus ini, tujuan utamanya agar pengguna dari internet bisa mengakses Mail/Web Server di jaringan internal.  
Arah lalu lintas dari internet menuju server diatur menggunakan **DST-NAT** pada router.  
BGP tidak berperan di sini, karena routing publik sepenuhnya diatur oleh ISP, bukan oleh jaringan kita.

---

## 🧩 Skenario 3
**Kondisi:**
- Perusahaan menyewa beberapa IP publik dari ISP, misalnya blok **/29**  
- Setiap Web Server menggunakan IP publik langsung  
- ISP membuat **Static Route** ke jaringan server kita  
- ISP mengiklankan static route tersebut ke Internet melalui BGP  

**Pertanyaan:**  
> Apakah perlu menggunakan BGP?

**Jawaban:**  
❌ **Masih belum perlu.**  
Dalam skenario ini, meskipun kita memiliki beberapa IP publik, pengaturan rute ke internet tetap dilakukan **oleh ISP**.  
Kita cukup melakukan konfigurasi **static route** pada router agar server-server kita dapat mengakses dan diakses dari internet.  
Selama kita hanya menggunakan **satu ISP** dan tidak mengiklankan prefix sendiri, maka **BGP belum dibutuhkan**.

---

## 🧩 Skenario 4
**Kondisi:**
- IP publik **tidak berasal dari ISP1 atau ISP2**  
- Perusahaan **berlangganan IP publik sendiri** dari **IANA / APNIC / IDNIC**  
- Melakukan **peering BGP** dengan **dua ISP (ISP1 dan ISP2)**  
- Mengiklankan prefix IP publik ke kedua ISP tersebut  

**Pertanyaan:**  
> Apakah perlu menggunakan BGP?

**Jawaban:**  
✅ **Wajib menggunakan BGP!**  
Kenapa? Karena dalam skenario ini, kita ingin memiliki **redundansi** dan **failover** antar dua ISP menggunakan IP publik yang kita miliki sendiri.

Jika kita hanya menggunakan IP publik milik salah satu ISP, maka jaringan kita **hanya dikenal** melalui ISP tersebut.  
Ketika ISP itu mengalami gangguan, seluruh akses ke server kita akan ikut **down**.

Dengan memiliki **IP publik sendiri** dan **AS Number (Autonomous System Number)** dari IDNIC/APNIC, kita bisa:
- Mengiklankan prefix IP kita melalui **dua ISP sekaligus (multi-homed)**  
- Memastikan server tetap **dapat diakses** meskipun salah satu ISP mengalami gangguan  
- Membangun **redundansi, load balancing**, dan **failover** antar-ISP  

Inilah **fungsi utama BGP** — menghubungkan **AS kita dengan AS milik ISP**, sehingga jaringan kita menjadi bagian dari **rangkaian besar Internet**.

---

## 🧠 Catatan Penting

Banyak orang salah paham bahwa:

> “BGP adalah cara agar server lokal bisa mengakses internet.”

Padahal, yang **benar** adalah:

> **BGP adalah cara agar Internet dapat menjangkau server lokal kita.**

Inilah perbedaan mendasar antara **BGP** dengan **IGP**:
- **IGP** → mengatur routing di dalam jaringan internal (intra-domain)  
- **BGP** → mengatur routing antar jaringan besar di Internet (inter-domain)  

---

## 💡 Kesimpulan

Gunakan **BGP hanya ketika:**
1. Anda memiliki **lebih dari satu koneksi ISP (multi-homed)**  
2. Anda memiliki **AS Number dan IP publik sendiri**  
3. Anda ingin mengatur **redundansi, load balancing,** atau **control penuh terhadap routing eksternal**

Jika tidak memenuhi kondisi di atas, maka **IGP atau static route sudah cukup**.

---

✍️ **Penulis:** 
💬 _“BGP bukan tentang bagaimana kamu menuju Internet, tapi bagaimana Internet menuju kamu.”_


# 📢 Jenis-jenis BGP Advertisement

Sebelum membahas jenis-jenis *BGP Advertisement*, penting untuk memahami konteksnya.  
Per tahun **2024**, terdapat sekitar **970.000 prefix** yang diumumkan di Internet melalui BGP global routing table.  

Pertanyaannya:  
> Apakah sebuah router perlu menerima semua 970 ribu prefix tersebut?

Jawabannya tergantung pada kebutuhan dan skenario implementasi BGP.  
Berikut beberapa jenis *BGP Advertisement* beserta kasus penggunaannya.

---

## 📘 Studi Kasus

> Kita memiliki sebuah router milik **Customer (AS500)** yang terhubung ke dua ISP berbeda:  
> - **ISP1 (AS100)**  
> - **ISP2 (AS200)**  
>  
> Keduanya saling terkoneksi ke router customer melalui BGP.

---

## 1. 🟩 BGP Advertisement — *Default Route*

Pada metode ini, **ISP hanya mengirimkan satu prefix default route (0.0.0.0/0)** ke router customer.

### ✅ Kelebihan:
- Menghemat **resource router** karena hanya menyimpan satu route.  
- Konfigurasi sederhana.  
- Cocok untuk perusahaan kecil/menengah yang tidak membutuhkan *policy routing* kompleks.

### ⚠️ Kekurangan:
- Router hanya akan menggunakan **satu jalur (ISP)** sebagai akses utama ke Internet.  
- Jika ingin mengakses jaringan pelanggan lain di ISP kedua, lalu lintas tetap akan "memutar" melalui ISP pertama.  
- Tidak ada kendali granular terhadap jalur ke Internet.

### 💡 Contoh:
```
Customer(AS500)
   ├── ISP1 (AS100) → memberikan default route
   └── ISP2 (AS200) → memberikan default route
```
Router hanya akan memilih **satu** default route dari salah satu ISP.

---

## 2. 🟨 BGP Advertisement — *Partial Route*

Pada metode ini, ISP memberikan **sebagian prefix** dari Internet — biasanya prefix pelanggan mereka sendiri dan rute *default* ke Internet global.

### ✅ Kelebihan:
- Router dapat memilih jalur langsung ke pelanggan ISP tanpa memutar.  
- Resource router tetap efisien karena hanya menerima sebagian prefix.  
- Cocok untuk pelanggan dengan kebutuhan *redundancy* sederhana.

### ⚠️ Kekurangan:
- Router masih harus memilih salah satu ISP sebagai jalur utama untuk trafik global.  
- Keputusan jalur menuju Internet tetap bergantung pada *default route*, bukan seluruh tabel global.

### 💡 Contoh:
- Prefix pelanggan ISP1 (misalnya 103.10.10.0/24) akan di-*advertise* ke customer.  
- Untuk tujuan lain (misalnya ke Google), customer masih menggunakan default route dari ISP pilihan.

---

## 3. 🟥 BGP Advertisement — *Full Route*

Metode ini berarti setiap ISP akan mengirimkan **seluruh prefix Internet global** (±970.000 route) ke router customer.

### ✅ Kelebihan:
- Router memiliki **kendali penuh** dalam memilih jalur tercepat (*Best Path Selection*).  
- Dapat mengoptimalkan performa trafik antar-ISP.  
- Ideal untuk operator besar dan penyedia layanan (ISP / Data Center).

### ⚠️ Kekurangan:
- Membutuhkan **resource besar**: RAM, CPU, dan TCAM tinggi.  
- Hanya disarankan untuk **router kelas carrier-grade**.

### 💡 Contoh:
- Router menerima seluruh tabel Internet dari kedua ISP.  
- Dapat menentukan jalur optimal untuk setiap destinasi global secara independen.

---

# 🧭 Mengapa BGP Disebut *Path Vector Protocol*?

Secara terminologi:
- **Path** → menunjukkan *rute tujuan* (AS Path)  
- **Vector** → menunjukkan *arah/jalur* yang dilalui

Artinya, **BGP menentukan jalur terbaik berdasarkan urutan AS yang dilewati (AS Path)**, bukan berdasarkan topologi jaringan internal seperti pada protokol IGP (misalnya OSPF atau IS-IS).

### Perbandingan Singkat:
| Aspek | BGP (Path Vector) | OSPF/IS-IS (Link State) |
|-------|-------------------|--------------------------|
| Informasi disimpan | AS Path antar sistem | Topologi lengkap jaringan |
| Lingkup | Antar-AS (Inter-domain) | Dalam satu AS (Intra-domain) |
| Penentuan jalur | Berdasarkan kebijakan dan AS Path | Berdasarkan cost / metric |
| Loop prevention | Dengan daftar AS Path | Dengan LSDB dan SPF |

💡 Dalam BGP, router hanya mengetahui **daftar AS yang dilalui**, bukan seluruh struktur jaringan di tiap AS tersebut.  
Contoh jalur menuju **AS100**:
```
AS400 → AS300 → AS200 → AS100
```
BGP hanya menyimpan urutan ini, tanpa mengetahui topologi internal masing-masing AS.

---

# 🧩 BGP Route Selection

Beberapa hal penting tentang proses pemilihan rute (*Best Path Selection*) di BGP:

1. Secara default, **BGP tidak melakukan load balancing** antar-jalur, kecuali dikonfigurasi secara eksplisit (misalnya dengan *Multipath BGP*).  
2. BGP menggunakan **Attributes** untuk menentukan jalur terbaik.  
3. Sebagai protokol *Path Vector*, BGP hanya menilai jalur berdasarkan urutan AS, **tanpa memperhitungkan kapasitas atau kondisi link** (misal bandwidth, latency, dll).

### Contoh Kasus:
- Router A ingin mencapai Router D.  
- Terdapat dua jalur:
  - Jalur 1: A → B → C → D (10 Gbps)
  - Jalur 2: A → D (100 Mbps)

Meskipun jalur pertama memiliki kapasitas lebih besar, **BGP tetap memilih jalur kedua (A → D)** karena *AS Path*-nya lebih pendek.

### 🎯 Kesimpulan:
Sebagai network engineer, kita perlu memodifikasi *BGP Attributes* (seperti **Local Preference, MED, AS Path Prepending**, dll) agar router dapat memilih jalur sesuai kebutuhan operasional dan kebijakan jaringan.

> ✍️ *Dokumentasi ini masih dapat dikembangkan, misalnya dengan menambahkan contoh konfigurasi nyata BGP pada router Cisco, Juniper, atau Mikrotik untuk memperjelas konsep di atas.*

