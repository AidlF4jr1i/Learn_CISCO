# 📡 Port Security & MAC Address Sticky on Cisco Switch

## 🔐 Apa Itu Port Security?

**Port Security** adalah metode untuk mengamankan port/interface di switch Cisco dari perangkat asing yang tidak dikenal. Misalnya:

> Dalam sebuah jaringan terdapat 3 server dan 1 PC yang terhubung ke sebuah switch. Jika seorang hacker mencabut kabel dari salah satu server dan menyambungkannya ke laptopnya, ia bisa mencoba mengakses informasi jaringan. Nah, *Port Security* mencegah hal ini dengan melakukan pemblokiran (block) atau *shutdown* pada port yang terdeteksi digunakan oleh perangkat tidak dikenal.

---

## ⚙️ Studi Kasus Port Security

### **📦 Topologi:**
- 3 Server
- 1 Switch
- 1 PC
- 1 Laptop (Disimulasikan sebagai Hacker)

### **🎯 Tujuan:**
Mengamankan interface `fa0/1` yang terhubung ke Server 1, agar hanya MAC Address server tersebut yang diizinkan.

### **🧪 Langkah Konfigurasi Port Security pada Interface `fa0/1`:**

```bash
Switch> enable
Switch# configure terminal
Switch(config)# interface fa0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport port-security 
Switch(config-if)# switchport port-security mac-address <MAC address Server>
Switch(config-if)# switchport port-security maximum <jumlah device yang diizinkan>
Switch(config-if)# switchport port-security violation shutdown
```

> 🔎 **Catatan:**
>
> Untuk melihat MAC Address di Cisco Packet Tracer:
> - Buka device
> - Pilih tab `Config` → `FastEthernet` → Lihat `MAC Address`

> ✅ *Violation Mode* yang tersedia:
> - `shutdown`: Mematikan interface jika terjadi pelanggaran
> - `restrict`: Memblokir perangkat asing dan mencatat pelanggaran
> - `protect`: Memblokir perangkat asing TANPA mencatat pelanggaran

---

## 🔬 Uji Coba Pelanggaran

Setelah konfigurasi selesai, coba cabut kabel dari Server 1 dan sambungkan ke Laptop (hacker). Kemudian lakukan perintah `ping` ke salah satu server atau PC lain.

🔗 [Contoh hasil uji coba (foto)](https://drive.google.com/open?id=1vEzzhrFuyJu2iStEDV3jyj35Kq5JBch5&usp=drive_fs)

---

## 🧠 Apa Itu MAC Address Sticky?

**MAC Address Sticky** adalah fitur yang secara otomatis menyimpan MAC Address dari device yang pertama kali terkoneksi dan mengirim traffic ke port switch.

> Jadi kamu tidak perlu input manual MAC Address!

Namun, MAC address baru hanya akan direkam jika device tersebut aktif mengirimkan paket.

---

## 🧪 Konfigurasi MAC Address Sticky (Contoh: interface `fa0/2` di Switch 2):

```bash
Switch> enable
Switch# configure terminal
Switch(config)# interface fa0/2
Switch(config-if)# switchport mode access
Switch(config-if)# switchport port-security 
Switch(config-if)# switchport port-security mac-address sticky
Switch(config-if)# switchport port-security maximum <jumlah device>
Switch(config-if)# switchport port-security violation shutdown
```

### 👀 Cek hasil konfigurasi sticky:
```bash
Switch# do show port-security interface fa0/2
```

---

## ⚠️ Mode Pelanggaran: Perbedaan Protect, Restrict, dan Shutdown

### 🔐 1. Violation Mode: **Protect**
- **Memblokir** semua paket dari perangkat asing
- Interface **tetap aktif (up)**
- Tidak menambah **Security Violation Count**

```bash
Switch(config-if)# switchport port-security violation protect
```

---

### 🚨 2. Violation Mode: **Restrict**
- Sama seperti *protect*, tetapi:
- **Menambah** `Security Violation Count`
- Cocok jika kamu ingin memonitor percobaan intrusi

```bash
Switch(config-if)# switchport port-security violation restrict
```

---

### 💣 3. Violation Mode: **Shutdown**
- Interface langsung dinonaktifkan (shutdown) saat pelanggaran terjadi
- Harus di-*reset* secara manual (atau menggunakan auto recovery)

```bash
Switch(config-if)# switchport port-security violation shutdown
```

---

## 📘 Kesimpulan

| Mode | Interface | Packet Asing | Hitung Violation | Keterangan |
|------|-----------|--------------|------------------|------------|
| Shutdown | Down | ❌ Blok | ✅ Ya | Aman, tapi ekstrem |
| Restrict | Up | ❌ Blok | ✅ Ya | Aman dan tercatat |
| Protect | Up | ❌ Blok | ❌ Tidak | Aman, tidak tercatat |

---

Semoga dokumentasi ini membantumu memahami dan menerapkan **Port Security** & **MAC Sticky** di Cisco Switch secara **efektif dan aman** 🔐✨

🔨🤖🔧 Kalau ada yang masih bingung, tinggal tanya aja ya!