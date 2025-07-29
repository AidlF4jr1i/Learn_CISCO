# 🌐 Pengenalan VLAN (Virtual LAN)

**VLAN (Virtual Local Area Network)** adalah metode segmentasi jaringan secara logis untuk memecah domain broadcast menjadi beberapa bagian, sehingga memudahkan administrator jaringan dalam memanajemen layanan yang dibutuhkan oleh masing-masing divisi dalam suatu organisasi atau perusahaan.

📷 Gambar penerapan VLAN:  
[Klik untuk melihat](https://drive.google.com/open?id=1VpfcDE1_9tIxkR6rrhkB_f-AYlKjJO2B&usp=drive_fs)

---

# 🧪 Praktik VLAN di Cisco Packet Tracer (CPT)

Ketika kita membuat topologi jaringan dengan switch, secara default akan terdapat **VLAN 1**. VLAN ini merupakan bawaan pabrik dan tidak dapat dihapus, namun kita tetap bisa menambahkan VLAN baru sesuai kebutuhan.

> 📌 **Nomor VLAN berkisar dari 1 hingga 4095**

### 💻 Tahapan Konfigurasi VLAN via CLI:

```bash
vlan <nomor_vlan>                # Contoh: vlan 10
name <nama_vlan>                # Contoh: name Marketing
show vlan brief                 # Menampilkan semua VLAN yang ada
int <nama_interface>           # Contoh: int fa0/1
switchport mode access
switchport access vlan 10      # Memasukkan interface ke VLAN yang telah dibuat
```

### ❓ Pertanyaan Umum

**Q: Apakah komputer 1 bisa berkomunikasi dengan komputer 3 jika mereka berada di VLAN yang berbeda namun subnet IP-nya sama?**

**A:** Tidak bisa. Meskipun berada dalam subnet yang sama, jika dua perangkat berada di VLAN berbeda, maka mereka berada di broadcast domain yang berbeda dan tidak bisa saling berkomunikasi tanpa adanya perangkat Layer 3 (seperti router atau switch Layer 3).

---

# 🔀 Trunk pada VLAN

**Trunk** adalah jenis interface yang dapat membawa banyak VLAN secara bersamaan. Trunk dibutuhkan agar VLAN dengan ID yang sama tetapi berada di switch atau ruangan yang berbeda dapat berkomunikasi.

### ⚙️ Konfigurasi Trunk Antar Switch:

Hubungkan interface antar switch terlebih dahulu, kemudian lakukan konfigurasi:

```bash
int fa0/5
switchport mode trunk
do sh int trunk
```

Lakukan pada kedua switch yang terhubung.

---

# 🔐 Allowed VLAN di Trunk

Secara default, semua VLAN (ID 1–4095) diizinkan lewat di trunk. Namun, di Cisco Packet Tracer hanya mendukung ID hingga 1005. Untuk membatasi VLAN yang boleh lewat:

```bash
int fa0/5
switchport trunk allowed vlan 10,20
```

### ➕ Menambahkan VLAN Baru ke Trunk:
```bash
switchport trunk allowed vlan add 30,40
```

### ➖ Menghapus VLAN dari Trunk:
```bash
switchport trunk allowed vlan remove 30
```

> ⚠️ Perintah ini digunakan hanya pada fase awal pembuatan jaringan atau saat melakukan penyesuaian VLAN di kemudian hari.

---

# 🧱 Menambahkan Switch Baru (Switch Cisco 3560)

Saat menggunakan switch Cisco seri 3560, perlu menentukan protokol trunk secara eksplisit sebelum mengaktifkan trunk.

Cisco 3560 mendukung dua protokol trunk:
- **ISL**: Khusus untuk perangkat Cisco
- **802.1Q**: Protokol standar terbuka

### 🔧 Konfigurasi Trunk pada Switch 3560:

```bash
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan 10,20
```

> Rata-rata switch modern saat ini hanya mendukung **802.1Q**, tapi jika ditemukan switch yang mendukung lebih dari satu protokol, maka `encapsulation` harus didefinisikan terlebih dahulu.

---

# 🤝 Dynamic Trunking Protocol (DTP)

**DTP** adalah protokol Cisco yang memungkinkan negosiasi otomatis antar port switch untuk menentukan apakah koneksi menjadi trunk atau tidak.

> 📌 Hanya berfungsi antar perangkat Cisco.

### 🔍 Mengecek Mode:
```bash
show int fa0/16 switchport
```
Cek bagian `Administrative Mode`, biasanya default-nya adalah `Dynamic Auto`.

---

## 🔁 Hubungan Antar Mode DTP:

| Mode 1 | Mode 2 | Hasil |
|--------|--------|-------|
| Dynamic Auto | Dynamic Auto | Access |
| Dynamic Auto | Dynamic Desirable | Trunk |
| Dynamic Auto | Trunk / Access | Mengikuti mode tetangga |
| Dynamic Desirable | Dynamic Desirable | Trunk |
| Dynamic Desirable | Trunk | Trunk |
| Dynamic Desirable | Access | Access |
| Trunk | Access | ❌ Error (Looping & Limited Connectivity) |

### 🔧 Contoh Konfigurasi DTP:
```bash
switchport mode dynamic auto
```

---

# 🏷️ Apa itu Tagged dan Untagged?

- **Tagged**: Frame yang dikirim dari PC akan diberikan tanda ID VLAN oleh switch pengirim, contohnya: `|<ID VLAN>| |DATA|`
  - Biasanya terlihat sebagai format `dot1q`.

- **Untagged**: Saat frame masuk ke switch tujuan, **ID VLAN akan dihapus**, menyisakan hanya data (`|DATA|`) untuk dikirim ke PC tujuan.

---

# 🌈 Apa itu Native VLAN?

**Native VLAN** adalah VLAN yang tidak menggunakan tag ketika melewati trunk port. Default-nya adalah **VLAN 1**.

- Tidak diberikan tag karena sudah dianggap diketahui oleh switch yang terlibat.
- Tetap harus di-*allow* pada trunk agar dapat melewati.

---

## 🔄 Mengubah dan Mengembalikan Native VLAN:

### Mengubah:
```bash
switchport trunk native vlan 20
```
Sekarang VLAN 20 menjadi native, dan VLAN 1 diperlakukan seperti VLAN lainnya (menggunakan tag).

### Mengembalikan ke Default:
```bash
switchport trunk native vlan 1
```

---

Sekian penjelasan tentang VLAN, Trunking, dan protokol terkait. Semoga bermanfaat! 🚀💡