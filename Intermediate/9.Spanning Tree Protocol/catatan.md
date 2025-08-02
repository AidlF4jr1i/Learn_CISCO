# 🧠 Spanning Tree Protocol (STP) - Ringkasan dan Simulasi Praktik

## Apa itu STP?
STP (Spanning Tree Protocol) adalah protokol pada switch yang berfungsi **mencegah terjadinya looping** dalam jaringan Layer 2 (Data Link Layer). STP bekerja dengan cara **memblokir** salah satu interface pada jalur redundan agar tidak terjadi ARP looping. 

Ketika link utama mati, STP akan otomatis **mengaktifkan link cadangan** untuk menggantikan jalur utama. Di perangkat **Cisco**, STP secara **default** sudah aktif.

---

## 🔁 Topologi STP (2 Switch)
**Link Topologi:** [Lihat Gambar Topologi](https://drive.google.com/open?id=1q0EAh2Fq7fGn81oA-YP1Z9sKMP8iYQaA&usp=drive_fs)

Topologi terdiri dari:
- 2 Switch
- 2 PC (masing-masing memiliki IP di subnet `192.168.1.0/24`)

### ⚠️ Pengujian Looping (STP OFF)
Untuk melakukan uji coba dan melihat efek looping bila STP dimatikan:
```bash
no spanning-tree vlan 1
```

❗**Catatan Penting:**  
> Jangan **pernah** menonaktifkan STP di jaringan nyata! Ini bisa menyebabkan **looping parah** yang membuat jaringan **down total** hanya dalam hitungan detik.

**Hasil simulasi looping:** [Lihat Animasi GIF](https://drive.google.com/open?id=1Ph3F38d9Icjw7YX2a6khagRzYHzyYloa&usp=drive_fs)

---

## 🔗 Topologi STP (3 Switch)
**Link Topologi:** [Lihat Gambar](https://drive.google.com/open?id=1b4biIVp1Tesa6MdeSMrXDlO5tqo5RXiy&usp=drive_fs)

Untuk memahami proses seleksi port oleh STP, kita menggunakan 3 Switch: SWA, SWB, dan SWC.

### 🧠 Konsep Dasar STP

1. **Root Bridge**  
   Switch yang menjadi pusat kendali STP. Semua port-nya adalah **Designated Port**. Pemilihan Root Bridge didasarkan pada:
   - **Priority** (default: 32768)
   - **MAC Address** (paling kecil akan menang)
   - Final Bridge ID = Priority + VLAN ID

   Contoh: VLAN 1 → Bridge ID = `32768 + 1 = 32769`

2. **Designated Port**  
   Port yang aktif untuk **mengirim data** ke segment tersebut. Biasanya milik Root Bridge.

3. **Root Port**  
   Port dengan jalur **tercepat** menuju Root Bridge (menggunakan **cost** sebagai acuan).

4. **Alternate Port**  
   Port cadangan yang akan **diblok** untuk mencegah looping.

### 🧪 Analisis Topologi

- **Root Bridge**: SWA (MAC: 0001.4258.52a2)
- **SWB**: Root Port = Fa0/2
- **SWC**: Root Port = Fa0/2
- Karena MAC SWC lebih rendah dari SWB, port Fa0/1 di SWC menjadi **Designated Port**, sedangkan Fa0/2 di SWB menjadi **Alternate (Blocked)**.

### 🔍 Verifikasi
```bash
show spanning-tree
do show spanning-tree
```

---

## 📊 Cost (Biaya Jalur)

Cost digunakan untuk memilih jalur tercepat ke Root Bridge.
- Cost berdasarkan **bandwidth interface** dan **jumlah hop**.

| Bandwidth | Cost  |
|-----------|-------|
| 10 Mb     | 100   |
| 100 Mb    | 19    |
| 1 Gb      | 4     |
| 10 Gb     | 2     |

Contoh:  
- Jalur SWA → SWC: Cost = 19  
- Jalur SWA → SWC → SWB: Cost = 19 + 19 = **38**

---

## 🛠️ Manipulasi Bandwidth

```bash
interface <nama_interface>
speed 10     # atau 100
```

❗Hanya dapat memilih dari nilai **diskrit**: `10`, `100`, `1000`, `10000`. Tidak bisa input sembarang angka seperti `49`, `279`, dll.

---

## 👑 Manipulasi Root Bridge

```bash
spanning-tree vlan <id_vlan> priority <value>
```

Value yang diperbolehkan:
```
0 4096 8192 12288 16384 20480 24576 28672 
32768 36864 40960 45056 49152 53248 57344 61440
```

Semakin **kecil nilai priority**, semakin besar kemungkinan Switch menjadi **Root Bridge**.

---

## 🔄 PVSTP (Per VLAN Spanning Tree Protocol)

Setiap VLAN punya STP **tersendiri**.

- Tambah VLAN baru (misal: VLAN 2 dan VLAN 3)
- Set port menjadi **trunk**:
```bash
switchport mode trunk
```

### 🔧 Manipulasi Root Bridge di PVSTP

Topologi: [Lihat Topologi PVSTP](https://drive.google.com/open?id=1fFZ3qYvm34v-VC9UpI3U4cQouKUDoNAE&usp=drive_fs)

Contoh konfigurasi:
```bash
# SWB
spanning-tree vlan 2 priority 20480

# SWA
interface fa0/4
switchport mode access
switchport access vlan 2

# SWC
interface fa0/3
switchport mode access
switchport access vlan 2
```

Dengan ini, **jalur cadangan** di VLAN 1 bisa menjadi **Root Port** di VLAN 2.

---

## 🔁 STP Port States

| State      | Fungsi                                  | Waktu   |
|------------|------------------------------------------|---------|
| Blocking   | Mencegah looping (Alternate Port)        | 20 s    |
| Listening  | Mendengar BPDU, tidak belajar MAC        | 15 s    |
| Learning   | Mulai belajar MAC, tidak forward packet  | 15 s    |
| Forwarding | Sudah bisa kirim/terima packet           | -       |

---

## ⚡ Fitur Tambahan

### 1. `portfast`
Untuk **interface menuju host/client langsung**, skip proses Listening & Learning:
```bash
interface <interface_ke_client>
spanning-tree portfast
```

### 2. `root primary`
Membuat Switch menjadi Root Bridge **secara otomatis** (priority = 24576):
```bash
spanning-tree vlan <id_vlan> root primary
```

### 3. Pemilihan Root Port bila nilai sama
Jika priority dan MAC sama, maka **angka interface lebih kecil** yang menang:
- Fa0/1 vs Fa0/2 → Fa0/1 yang jadi Root Port

---

🙏 **Terima kasih telah menyimak!**