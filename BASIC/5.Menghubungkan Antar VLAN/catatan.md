# 🔁 Konfigurasi Inter VLAN Routing

**InterVLAN Routing** adalah metode yang memungkinkan perangkat dari VLAN berbeda untuk saling berkomunikasi.  
Karena VLAN secara default berada di domain broadcast yang berbeda, diperlukan **Router** atau **Multi Layer Switch (MLS)** agar perangkat antar VLAN dapat terhubung.

---

## 📌 Rencana IP untuk Masing-Masing VLAN

| VLAN ID | Nama Divisi     | Network IP           |
|---------|------------------|-----------------------|
| VLAN 1  | Native VLAN      | 192.168.1.0/24        |
| VLAN 10 | IT               | 192.168.10.0/24       |
| VLAN 20 | Marketing        | 192.168.20.0/24       |
| VLAN 30 | Engineering      | 192.168.30.0/24       |

---

## ⚙️ Konfigurasi Router - Static IP untuk InterVLAN

```bash
en
conf t
int fa0/0
ip address 192.168.1.1 255.255.255.0
no shutdown
```

> Konfigurasi di atas untuk VLAN 1 (Native VLAN)

### 🔧 Sub-Interface Configuration untuk VLAN 10-30

```bash
int fa0/0.10
encapsulation dot1q 10
ip address 192.168.10.1 255.255.255.0

int fa0/0.20
encapsulation dot1q 20
ip address 192.168.20.1 255.255.255.0

int fa0/0.30
encapsulation dot1q 30
ip address 192.168.30.1 255.255.255.0
```

> Sub-interface digunakan untuk menangani masing-masing VLAN melalui satu port fisik router (`fa0/0`).

---

# 📦 Konfigurasi DHCP Server di Router

Untuk mempermudah distribusi IP Address ke setiap PC dan menghindari konflik IP, kita bisa menggunakan **DHCP Pool**.

### 🔧 Contoh Konfigurasi DHCP:

```bash
en
conf t

ip dhcp pool VLAN10_IT
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
dns-server 8.8.8.8

ip dhcp pool VLAN20_MARKETING
network 192.168.20.0 255.255.255.0
default-router 192.168.20.1
dns-server 8.8.8.8

ip dhcp pool VLAN30_ENGINEERING
network 192.168.30.0 255.255.255.0
default-router 192.168.30.1
dns-server 8.8.8.8
```

> DHCP akan memberikan IP sesuai VLAN masing-masing jika topologi dan VLAN sudah benar.

---

# 🔀 Switch Layer 2 vs Layer 3 (MLS)

| Perangkat        | Kemampuan Routing | Fungsi          |
|------------------|-------------------|------------------|
| Switch Layer 2   | ❌ Tidak bisa     | Hanya antar device dalam satu VLAN |
| Multi Layer Switch (Layer 3) | ✅ Bisa | Routing antar VLAN + Switching |

> Multi Layer Switch (seperti Cisco 3560) mendukung fitur routing sekaligus switching.

📷 Topologi Referensi:  
[Klik untuk melihat](https://drive.google.com/open?id=1vQlnyfmLdCrV8QKlAYmFJ_jIPV7VgbmV&usp=drive_fs)

---

## 🧠 Konfigurasi IP SVI (Switch Virtual Interface) di MLS

```bash
en
conf t

int vlan 1
ip address 192.168.1.1 255.255.255.0

int vlan 10
ip address 192.168.10.1 255.255.255.0

int vlan 20
ip address 192.168.20.1 255.255.255.0

int vlan 30
ip address 192.168.30.1 255.255.255.0
```

### ⚠️ Catatan:
- Secara default MLS masih beroperasi pada Layer 2.
- Untuk mengaktifkan kemampuan routing (Layer 3):

```bash
ip routing
```

### 🔍 Verifikasi Routing:
```bash
do show ip route
```

Jika sudah aktif, akan muncul output seperti:

```
C    192.168.1.0/24 is directly connected, Vlan1
C    192.168.10.0/24 is directly connected, Vlan10
C    192.168.20.0/24 is directly connected, Vlan20
C    192.168.30.0/24 is directly connected, Vlan30
```

---

# 📦 DHCP Server di Multi Layer Switch

Konfigurasinya mirip dengan konfigurasi pada router. Pastikan `ip routing` sudah diaktifkan.

```bash
en
conf t

ip dhcp pool VLAN10_IT
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
dns-server 8.8.8.8

ip dhcp pool VLAN20_MARKETING
network 192.168.20.0 255.255.255.0
default-router 192.168.20.1
dns-server 8.8.8.8

ip dhcp pool VLAN30_ENGINEERING
network 192.168.30.0 255.255.255.0
default-router 192.168.30.1
dns-server 8.8.8.8
```

### 🔍 Verifikasi DHCP Pool:
```bash
do show ip dhcp pool
```

---

# 🧪 Latihan Soal VLAN & InterVLAN

### 📚 Case Study:
> Buatlah konfigurasi VLAN dan InterVLAN Routing menggunakan **Router** dan pastikan komunikasi antar VLAN berhasil.

### IP Ketentuan:
- **VLAN 10:** 192.168.10.0/24
- **VLAN 20:** 192.168.20.0/24
- **VLAN 30:** 192.168.30.0/24

### 🔍 File Uji Coba:
> Buka file `CASE_VLAN.pkt` pada direktori ini dan uji menggunakan Cisco Packet Tracer.

---

Semoga panduan ini membantu kamu memahami konsep **InterVLAN Routing**, baik dengan router maupun switch Layer 3.  
Terus berlatih dan semangat belajar jaringan! 🚀🧠