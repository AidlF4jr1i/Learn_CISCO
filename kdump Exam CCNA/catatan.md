# 📚 Kumpulan Soal & Pembahasan Jaringan Cisco

Dokumen ini berisi kumpulan contoh soal beserta pembahasan teknis untuk membantu persiapan ujian atau evaluasi jaringan berbasis perangkat Cisco.  
Seluruh soal di sini **bukan** soal resmi dari Cisco atau platform terkait, melainkan disusun untuk menguji dan memperdalam pemahaman berdasarkan materi yang telah dipelajari sebelumnya.

> 💡 **Catatan:** Setiap pembahasan dilengkapi analisis teknis agar lebih mudah dipahami, baik oleh pemula maupun profesional jaringan.

---

## 1. Menentukan Alamat Network

**Pertanyaan:** Berapa IP Network untuk `192.168.1.100/27`?  
**Jawaban Benar:** ✅ `192.168.1.96`

**Analisis Teknis:**  
- Prefix `/27` berarti subnet mask `255.255.255.224`.  
- Ukuran blok subnet: `256 - 224 = 32`  
- Kelipatan network pada oktet keempat: 0, 32, 64, 96, 128, dst.  
- IP `192.168.1.100` berada di rentang network yang dimulai dari `192.168.1.96`.

---

## 2. Perangkat Penghubung Antar Network

**Pertanyaan:** Device apa yang digunakan untuk menghubungkan antar network?  
**Jawaban Benar:** ✅ `router`

**Analisis Teknis:**  
- **Router**: Layer 3 (Network Layer) → Meneruskan paket antar network berdasarkan IP.  
- **Switch**: Layer 2 (Data Link Layer) → Meneruskan frame di dalam LAN berdasarkan MAC.  
- **Hub**: Layer 1 (Physical Layer) → Mengulang sinyal ke semua port tanpa kecerdasan.

---

## 3. Konektivitas dalam Subnet yang Sama

**Pertanyaan:** PC1 `172.16.1.10/24` dan PC2 `172.16.1.200/24`. Bisa saling ping?  
**Jawaban Benar:** ✅ `bisa`

**Analisis Teknis:**  
- Prefix `/24` = `255.255.255.0` → 3 oktet pertama adalah network ID (`172.16.1`).  
- Kedua IP berada di network yang sama, sehingga dapat berkomunikasi langsung.

---

## 4. Kebutuhan Router untuk Komunikasi Intra-Subnet

**Pertanyaan:** PC `10.10.10.10/25` dan `10.10.10.50/25`. Perlukah router?  
**Jawaban Benar:** ✅ `tidak perlu`

**Analisis Teknis:**  
- Prefix `/25` (`255.255.255.128`) membagi jaringan menjadi dua subnet.  
- Rentang pertama: `10.10.10.1` – `10.10.10.126`.  
- Kedua IP berada di subnet pertama, sehingga tidak memerlukan router.

---

## 5. Menentukan Rentang Host Valid

**Pertanyaan:** IP range untuk `172.16.1.200/26`?  
**Jawaban Benar:** ✅ `172.16.1.193 - 172.16.1.254`

**Analisis Teknis:**  
- Prefix `/26` = blok 64 alamat.  
- Network: `172.16.1.192`  
- Broadcast: `172.16.1.255`  
- Host valid: `172.16.1.193` – `172.16.1.254`.

---

## 6. Elevasi Hak Akses

**Pertanyaan:** Perintah untuk masuk dari user mode ke privilege mode?  
**Jawaban Benar:** ✅ `enable`

**Analisis Teknis:**  
- **User EXEC Mode (`>`):** Perintah dasar & monitoring.  
- **Privileged EXEC Mode (`#`):** Konfigurasi & troubleshooting penuh.  
- Gunakan `enable` untuk berpindah dari user ke privileged mode.

---

## 7. Keamanan Kata Sandi

**Pertanyaan:** Mana yang lebih aman?  
**Jawaban Benar:** ✅ `enable secret cisco`

**Analisis Teknis:**  
- **enable secret:** Hash MD5 (tidak reversible).  
- **enable password:** Plain text atau enkripsi type 7 (mudah dibalik).  
- Jika keduanya ada, `enable secret` diprioritaskan.

---

## 8. Protokol Negosiasi EtherChannel

**Pertanyaan:** Protokol EtherChannel proprietary Cisco?  
**Jawaban Benar:** ✅ `pagp`

**Analisis Teknis:**  
- **PAgP:** Cisco proprietary link aggregation.  
- **LACP:** Standar IEEE 802.3ad, interoperable antar vendor.

---

## 9. Aktivasi Routing pada Multilayer Switch

**Pertanyaan:** Perintah untuk mengaktifkan fungsi routing pada MLS?  
**Jawaban Benar:** ✅ `ip routing`

**Analisis Teknis:**  
- MLS default Layer 2.  
- Gunakan `ip routing` untuk mengaktifkan kemampuan Layer 3 (inter-VLAN routing).

---

## 10. Mode Pelanggaran Port Security

**Pertanyaan:** Mana yang BUKAN mode violation?  
**Jawaban Benar:** ✅ `security`

**Analisis Teknis:**  
Mode violation yang valid:  
1. `shutdown` → port nonaktif.  
2. `restrict` → drop paket ilegal + log.  
3. `protect` → drop paket ilegal tanpa log.  
`security` hanyalah bagian perintah, bukan mode.

---

## 11. Identifikasi Standard Access List

**Pertanyaan:** ID apa yang bisa dipakai?  
**Jawaban Benar:** ✅ `80`

**Analisis Teknis:**  
- Standard ACL: 1–99 & 1300–1999.  
- Filter berdasarkan IP sumber.

---

## 12. Identifikasi Extended Access List

**Pertanyaan:** ID untuk Extended ACL?  
**Jawaban Benar:** ✅ `102`

**Analisis Teknis:**  
- Extended ACL: 100–199 & 2000–2699.  
- Bisa filter berdasarkan IP sumber, tujuan, protokol, port.

---

## 13. Konfigurasi NAT Overload (PAT)

**Pertanyaan:** Command NAT agar client bisa akses internet?  
**Jawaban Benar:** ✅  
```bash
ip nat inside source list 1 int fa0/0 overload
```

**Analisis Teknis:**  
- `ip nat inside source` → NAT untuk jaringan internal.  
- `list 1` → ACL yang menentukan IP privat.  
- `int fa0/0` → alamat publik dari interface tersebut.  
- `overload` → Mengaktifkan PAT.

---

## 14. Pencegahan Loop pada Layer 2

**Pertanyaan:** Protokol pencegah looping?  
**Jawaban Benar:** ✅ `stp`

**Analisis Teknis:**  
- **STP (Spanning Tree Protocol):** Mencegah loop di Layer 2 dengan memblokir jalur redundan.

---

## 15. Sinkronisasi Database VLAN

**Pertanyaan:** Protokol untuk membuat VLAN otomatis?  
**Jawaban Benar:** ✅ `vtp`

**Analisis Teknis:**  
- **VTP:** Cisco proprietary VLAN Trunking Protocol, sinkronisasi VLAN antar switch.

---

## 16. Menyimpan Konfigurasi Perangkat

**Pertanyaan:** Command untuk menyimpan konfigurasi?  
**Jawaban Benar:** ✅  
```bash
copy run start
```

**Analisis Teknis:**  
- `running-config` (RAM, hilang saat reboot).  
- `startup-config` (NVRAM, dimuat saat boot).  
- `copy run start` → menyimpan konfigurasi aktif ke startup.

---

## 17. Protokol Routing Antar Autonomous System

**Pertanyaan:** Routing Protocol antar AS?  
**Jawaban Benar:** ✅ `bgp`

**Analisis Teknis:**  
- **AS (Autonomous System):** Jaringan di bawah administrasi tunggal.  
- **IGP:** Routing dalam satu AS (contoh: OSPF, EIGRP).  
- **EGP:** Routing antar AS (contoh: BGP).

---

💻 **Penulis:** Disusun untuk pembelajaran jaringan berbasis Cisco secara praktis & teknis.