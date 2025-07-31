# Konfigurasi VLAN & VTP pada Jaringan Cisco

## 🔷 Apa itu VTP (VLAN Trunking Protocol)?

VTP (VLAN Trunking Protocol) adalah protokol Cisco yang digunakan untuk **mengelola dan menyebarkan informasi VLAN secara otomatis** antar switch dalam satu domain jaringan. Dengan VTP, kamu tidak perlu mengkonfigurasi VLAN satu per satu di setiap switch—cukup dikonfigurasi di satu switch (server), maka informasi VLAN akan tersebar ke semua switch client di domain yang sama.

### 🔑 Tujuan Penggunaan VTP:
- Mempermudah manajemen VLAN dalam jaringan skala besar
- Mengurangi human error
- Menghemat waktu konfigurasi

## 🔌 Mode-Mode dalam VTP

| Mode VTP | Fungsi |
|----------|--------|
| **Server** | Sumber utama informasi VLAN. Perubahan VLAN (tambah/hapus/ubah nama) hanya bisa dilakukan di sini. |
| **Client** | Menerima update VLAN dari Server. Tidak bisa mengubah konfigurasi VLAN. |
| **Transparent** | Tidak mengikuti VTP domain. Konfigurasi VLAN dilakukan secara lokal dan **tidak memengaruhi** maupun **terpengaruh oleh** Server/Client. Namun tetap meneruskan (forward) paket VTP ke switch lain. |

## ⚙️ Topologi Jaringan Praktik VTP

```
|SW1|fa0/1 ---> fa0/1|SW2|fa0/2 ---> fa0/1|SW3|fa0/2 ---> fa0/1|SW4|
```

> **Keterangan**:  
- SW1 dan SW4 → VTP Server  
- SW2 → VTP Transparent  
- SW3 → VTP Client

## 🌐 Konfigurasi Dasar Trunk

Sebelum mengatur VTP, pastikan semua port antar switch sudah berada dalam mode trunk:

```bash
switchport mode trunk
```

## ⚙️ Konfigurasi VTP di Setiap Switch

> Gunakan **domain** dan **password** yang sama agar VTP dapat berfungsi dengan baik.

### 🟢 SW1 (VTP Server)
```bash
vtp mode server
vtp domain aidil.id
vtp password 123
```

### 🟡 SW2 (VTP Transparent)
```bash
vtp mode transparent
vtp domain aidil.id
vtp password 123
```

### 🔵 SW3 (VTP Client)
```bash
vtp mode client
vtp domain aidil.id
vtp password 123
```

### 🟢 SW4 (VTP Server)
```bash
vtp mode server
vtp domain aidil.id
vtp password 123
```

## 📦 Pembuatan VLAN

### 🔧 Di VTP Server (SW1/SW4)
```bash
vlan 2
name A

vlan 3
name B

vlan 4
name C

vlan 5
name D
```

> VLAN yang dibuat di Server otomatis akan tersebar ke Client.

### 🔧 Di VTP Transparent (SW2)
```bash
vlan 10
name E

vlan 20
name F
```

> VLAN ini **hanya berlaku lokal di SW2** dan **tidak akan tersebar ke client/server lain**.

## 🔍 Mengecek Status VTP

Gunakan perintah berikut pada salah satu switch untuk melihat status VTP:
```bash
do show vtp status
```

## ⚠️ Penting! Configuration Revision Number

**Configuration Revision** adalah nomor yang menunjukkan versi konfigurasi VTP saat ini. Masalah serius bisa terjadi jika kamu menambahkan switch baru ke jaringan dengan **configuration revision lebih tinggi**.

### 🧨 Potensi Masalah:
Jika switch baru yang ditambahkan sebagai VTP Client memiliki revision number lebih tinggi, maka:
- Semua konfigurasi VLAN di VTP Server & Client bisa **tertimpa atau hilang**
- Potensi **kerusakan total jaringan** (downtime operasional)

### ✅ Solusi Aman Sebelum Menambahkan Switch Baru:
1. **Set switch baru ke mode Transparent** terlebih dahulu:
    ```bash
    vtp mode transparent
    ```
2. Setelah itu, barulah ubah ke mode Client (jika diperlukan):
    ```bash
    vtp mode client
    ```

Tujuan langkah ini adalah untuk **mereset configuration revision number** tanpa menghapus konfigurasi VLAN lokal.

## 🧪 CASE STUDY: Implementasi VTP + VLAN + InterVLAN + DHCP

### 🎯 Tujuan:
Buat konfigurasi jaringan dengan:
- **1 Router**
- **4 Switch**
    - 1 Server
    - 3 Client
- **8 VLAN (bebas ID & nama)**
- **InterVLAN Routing** di router
- **DHCP Server**
- **Testing komunikasi antar VLAN**

### 🔄 Proses yang perlu dilakukan:
- Konfigurasi VTP di semua switch
- Buat VLAN di switch Server
- Atur trunking & assign VLAN pada interface
- Setup sub-interface router untuk InterVLAN
- Konfigurasi DHCP untuk tiap VLAN
- Lakukan uji komunikasi antar VLAN

## 🧾 Contoh File Referensi

Lihat file `Praktek_VTP.pkt` untuk membandingkan hasil konfigurasi yang kamu buat.

## 🤝 Penutup

Dengan memahami konsep VTP dan mode-modennya, kamu bisa membangun dan memelihara jaringan switch VLAN yang besar dengan efisien dan aman. Selalu perhatikan penggunaan revision number untuk menghindari kesalahan fatal dalam jaringan.

**Semoga bermanfaat & selamat mencoba!** 🚀