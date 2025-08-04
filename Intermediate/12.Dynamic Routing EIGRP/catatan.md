
# 📡 Dynamic Routing dan EIGRP (Enhanced Interior Gateway Routing Protocol)

## 💡 Pengantar

Seperti yang sudah dirasakan pada konfigurasi **Static Routing**, terdapat masalah **skalabilitas** jika jaringan terus berkembang. Bayangkan jika ada 10 router baru yang ditambahkan, maka kita harus memperbarui informasi routing di **setiap router** secara manual — ini sangat tidak efisien.

Solusinya adalah dengan menggunakan **Dynamic Routing**! Dengan dynamic routing, **informasi jaringan baru akan disebarkan otomatis** oleh router yang baru terhubung ke tetangganya, sehingga tidak perlu input manual di semua router.

---

## 🚀 Mengenal EIGRP (Enhanced Interior Gateway Routing Protocol)

EIGRP adalah salah satu dynamic routing protocol yang **khusus digunakan di perangkat Cisco** (proprietary). EIGRP menggunakan **algoritma DUAL (Diffusing Update Algorithm)** yang membuat routing lebih cepat dan stabil.

> 🔍 **Fun Fact:**
> - EIGRP menggunakan huruf **D** dalam routing table (bukan "E") karena huruf "E" sudah digunakan oleh protokol lain yaitu EGP.

### 🧠 Konsep Dasar EIGRP:

- **Router ID** harus **sama antar router yang saling terhubung** dalam domain EIGRP.
- Gunakan perintah `network` untuk menentukan subnet mana saja yang ingin diumumkan ke tetangga.
- Gunakan `do show ip eigrp neighbor` untuk memverifikasi status EIGRP.

---

## 🧪 Praktik: EIGRP dengan 2 Router

📷 Gambar Topologi: [Klik di sini](https://drive.google.com/open?id=1pED9Q69DcNrB-KYoPm1BVBjBIgSZX_AX&usp=drive_fs)

### 📐 IP Address:

- **R1**:
  - Ke client: `192.168.1.1/24`
  - Ke R2: `10.10.10.1/24`
- **R2**:
  - Ke client: `192.168.2.1/24`
  - Ke R1: `10.10.10.2/24`

> 📌 IP PC mengikuti subnet masing-masing router.

### ⚙️ Konfigurasi EIGRP

**R1**:

```shell
router eigrp 11
network 192.168.1.0
network 10.10.10.0
```

**R2**:

```shell
router eigrp 11
network 192.168.2.0
network 10.10.10.0
```

✅ Setelah konfigurasi, coba lakukan **ping antar PC client**. Jika konfigurasi EIGRP berhasil, komunikasi akan langsung terjalin tanpa perlu route manual!

---

## 🌐 Topologi 4 Router dengan EIGRP

### 📐 IP Address:

- **R1**:
  - Client: `192.168.1.1/24`
  - Ke R2: `10.10.10.1/24`
- **R2**:
  - Client: `192.168.2.1/24`
  - Ke R1: `10.10.10.2/24`
  - Ke R3: `10.20.20.1/24`
- **R3**:
  - Client: `192.168.3.1/24`
  - Ke R2: `10.20.20.2/24`
  - Ke R4: `10.30.30.1/24`
- **R4**:
  - Client: `192.168.4.1/24`
  - Ke R3: `10.30.30.2/24`

> 📌 IP PC tetap mengikuti subnet masing-masing router.

### ⚙️ Konfigurasi EIGRP Lanjutan

**R2**:

```shell
router eigrp 11
network 10.20.20.0
```

**R3**:

```shell
router eigrp 11
network 10.20.20.0
network 10.30.30.0
network 192.168.3.0
```

**R4**:

```shell
router eigrp 11
network 10.30.30.0
network 192.168.4.0
```

### 🔍 Verifikasi

Gunakan perintah berikut untuk mengecek apakah route EIGRP sudah muncul:

```shell
do show ip route
```

> Hasil route EIGRP akan muncul dengan awalan `D`.

---

## 🌈 Menambahkan VLAN pada Jaringan EIGRP

Selanjutnya, kita akan menambahkan **VLAN 5** dan **VLAN 6** ke dalam jaringan dan menghubungkannya ke **Router 4** melalui switch.

📷 Gambar Topologi: [Klik di sini](https://drive.google.com/open?id=1T5CKrwxBBPKR1IMUkYnuag_IfnSK6dxv&usp=drive_fs)

### 📐 IP VLAN

- **VLAN 5**: `192.168.5.1/24`
- **VLAN 6**: `192.168.6.1/24`

### ⚙️ Konfigurasi R4:

```shell
en
conf t
int fa1/0.5
 encapsulation dot1q 5
 ip address 192.168.5.1 255.255.255.0

ip dhcp pool VLAN5
 network 192.168.5.0 255.255.255.0
 default-router 192.168.5.1
 dns-server 8.8.8.8
exit

int fa1/0.6
 encapsulation dot1q 6
 ip address 192.168.6.1 255.255.255.0

ip dhcp pool VLAN6
 network 192.168.6.0 255.255.255.0
 default-router 192.168.6.1
 dns-server 8.8.8.8
exit
```

### ⚙️ Konfigurasi Switch:

```shell
en
conf t
int fa0/3
 switchport mode trunk

vlan 5
exit
vlan 6
exit

do show vlan brief

int fa0/1
 switchport mode access
 switchport access vlan 5

int fa0/2
 switchport mode access
 switchport access vlan 6
end
```

### 📡 Tambahan EIGRP untuk VLAN:

```shell
router eigrp 11
network 192.168.5.0
network 192.168.6.0
```

---

## ✅ Penutup

Dengan menggunakan **EIGRP**, proses routing menjadi lebih otomatis dan efisien. Kita tidak perlu menginput rute satu per satu setiap kali menambahkan router baru. Selain itu, integrasi VLAN juga memberikan fleksibilitas dalam membagi segmen jaringan.

Semangat terus belajar jaringan! 🔥💻🌐
