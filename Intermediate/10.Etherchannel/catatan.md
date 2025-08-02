# 🔗 EtherChannel - Penggabungan Link untuk Bandwidth Maksimal

## Apa itu EtherChannel?

**EtherChannel** adalah teknik penggabungan beberapa interface fisik pada switch menjadi **satu interface logis (virtual)** yang disebut **Port-Channel**, untuk meningkatkan **bandwidth** dan **redundansi** tanpa terkena **blokir oleh STP**. Konsep ini serupa dengan **Bonding** atau **Link Aggregation** di perangkat lain seperti Mikrotik.

---

## 🔄 Jenis-Jenis EtherChannel

### 1. LACP (Link Aggregation Control Protocol)

- **Open Standard** (bisa digunakan di berbagai vendor)
- Gunakan command berikut untuk konfigurasi:

```bash
interface range fa0/1 - fa0/3
switchport mode trunk
channel-group <nomor_id> mode <active|passive>
```

📌 **Mode:**
- `active`: Mengajak tetangga untuk membentuk LACP
- `passive`: Menunggu ajakan dari tetangga

🧠 **Kombinasi Valid:**
- active - active ✅
- active - passive ✅
- passive - passive ❌ (tidak akan terbentuk)

💡 **Rekomendasi:** Gunakan **nomor ID yang sama** di kedua sisi switch.

### 🔍 Verifikasi:
```bash
do show etherchannel summary
```

Contoh output:
```
1     Po1(SU)           LACP   Fa0/1(P) Fa0/2(P) Fa0/3(P)
```

- `Po1`: Port-channel 1
- `S`: Switchport (Layer 2)
- `U`: In Use
- `(P)`: Port aktif dalam channel

---

### 2. PAgP (Port Aggregation Protocol)

- **Cisco Proprietary** (hanya untuk perangkat Cisco)
- Langkah konfigurasi mirip seperti LACP:

```bash
interface range fa0/1 - fa0/3
switchport mode trunk
channel-group <nomor_id> mode <desirable|auto>
```

📌 **Mode:**
- `desirable`: Mengajak tetangga untuk membentuk PAgP
- `auto`: Menunggu ajakan dari tetangga

🧠 **Kombinasi Valid:**
- desirable - desirable ✅
- auto - desirable ✅
- auto - auto ❌

### 🔍 Verifikasi:
```bash
do show etherchannel summary
```

Contoh output:
```
2     Po2(SU)           PAgP   Fa0/1(P) Fa0/2(P) Fa0/3(P)
```

---

### 3. EtherChannel Layer 3 (Routed Port)

- Digunakan jika ingin membuat EtherChannel yang berfungsi di **Layer 3**
- Berbasis IP Address
- Tidak menggunakan `switchport`, tapi **routing**

```bash
interface range fa0/1 - fa0/3
no switchport
channel-group <nomor_id> mode on

interface port-channel <nomor_id>
ip address <ip_address> <subnet_mask>
```

📌 Mode **satu-satunya** yang tersedia: `on`  
📌 Tidak memerlukan negosiasi (langsung force bundling)

📌 **Catatan Penting:**
- EtherChannel Layer 3 hanya membuat **Switch ↔ Switch** bisa berkomunikasi
- Jika ingin **PC ↔ PC** bisa komunikasi, **harus konfig VLAN & InterVLAN Routing** di kedua switch

### 🔍 Verifikasi:
```bash
do show etherchannel summary
```

Contoh output:
```
1     Po1(RU)           -   Fa0/1(P) Fa0/2(P) Fa0/3(P)
```

- `R`: Routed (Layer 3)
- `U`: In Use

---

## 📌 Catatan Umum EtherChannel

- ID yang bisa digunakan untuk `channel-group` adalah dari `1 - 6`
- Pastikan **mode dan ID sama di kedua switch** yang terhubung
- EtherChannel membantu **agregasi bandwidth** dan **failover link**

---

🙏 **Terima kasih telah menyimak! Semoga pembelajaran EtherChannel ini bermanfaat!**