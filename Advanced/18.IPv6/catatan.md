# 📚 Belajar IPv6 – Dari Dasar sampai Routing

Selamat datang di ringkasan pembelajaran IPv6 ini!  
Di sini kita akan membahas IPv6 mulai dari pengenalan, jenis alamat, cara **compress/uncompress**, hingga konfigurasi routing statis dan dinamis (OSPF & EIGRP).  
Bahasannya **ringkas, jelas, namun tetap interaktif** ✨

---

## 🔍 1. Pengenalan IPv6

IPv6 adalah versi terbaru dari alamat IP yang dibuat untuk mengatasi kekurangan IPv4.  
Jika IPv4 memiliki kapasitas sekitar 4,3 miliar alamat, IPv6 mampu menyediakan **hingga 340 undecillion** alamat — jumlah yang luar biasa besar.  
Dengan begitu, perusahaan maupun individu **tidak akan kehabisan alamat IP**.

📊 Perbandingan IPv4 vs IPv6:  
[Lihat Tabel IPv4 vs IPv6](LINK_TABEL)

---

## 🧩 2. IPv6 Address Comprehension

IPv6 memiliki panjang **128-bit** yang ditulis dalam format **8 blok** heksadesimal (setiap blok = 4 karakter angka/huruf), dipisahkan oleh tanda `:`.

Contoh:  
```
2001:0db8:0000:0000:0000:ff00:0042:8329
```

### ✂️ Cara Memperpendek (Compress) IPv6
1. **Hapus nol di depan** setiap blok (maksimal 3 nol dihapus).
   - Contoh: `0db8` → `db8`, `0042` → `42`
2. Jika ada **urutan blok `0000` berturut-turut**, gantikan sekali saja dengan `::`
   - Contoh:  
     ```
     2001:0db8:0000:0000:0000:ff00:0000:0000
     ```
     Bisa menjadi:
     - `2001:db8::ff00:0:0` **atau**
     - `2001:db8:0:0:0:ff00::`

⚠️ Catatan: `::` hanya boleh dipakai **sekali** dalam satu alamat.

**Contoh Compress:**
1. `2001:0db8:0000:0000:0000:ff00:0042:8329` → `2001:db8::ff00:42:8329`  
2. `2607:f0d0:1002:0011:0000:0000:0000:0001` → `2607:f0d0:1002:11::1`

---

### 🔄 Cara Memperluas (Uncompress) IPv6
Balikkan proses compress:
1. Kembalikan `::` menjadi urutan blok `0000` hingga total blok = 8.
2. Tambahkan nol di depan blok jika kurang dari 4 digit.

**Contoh Uncompress:**
1. `2001:db8::1` → `2001:0db8:0000:0000:0000:0000:0000:0001`  
2. `fe80::f2de:f1ff:fe3f:307e` → `fe80:0000:0000:0000:f2de:f1ff:fe3f:307e`

---

## 🏷 3. Jenis Alamat IPv6

1. **Link Local** → `FE80::/10`  
   - Digunakan untuk komunikasi dalam satu link jaringan.
2. **Global Unicast** → `2000::/3`  
   - Setara dengan **IP Publik** di IPv4.
3. **Unique Local** → `FC00::/7`  
   - Setara dengan **IP Private** di IPv4.

💡 Keterangan:
- Jika IPv6 diawali `2` atau `3` → **Global Unicast**.
- Jika diawali `FC` atau `FD` → **Unique Local**.

---

## 📏 4. IPv6 Prefix

Prefix adalah bagian depan alamat IP yang menunjukkan **Network ID**.  
Ditulis dalam format **CIDR** `/angka` di mana angka adalah jumlah bit Network ID.

- IPv4: panjang alamat 32 bit (`/0` s/d `/32`)
- IPv6: panjang alamat 128 bit (`/0` s/d `/128`)

**Contoh IPv6 LAN /64:**  
- `2607:f0d0:1002:0011::1/64`  
- `2607:f0d0:1002:0011::2/64`  
Keduanya bisa berkomunikasi langsung (64 bit pertama sama).

---

## 🖥 5. Topology IPv6

[Lihat Topology IPv6](LINK_TOPOLOGY)

### Ketentuan IP:
**R1**
```
int fa0/0: 2001:db8:10::1/64
int fa0/1: 2001:db8:1::1/64
```

**R2**
```
int fa0/0: 2001:db8:10::2/64
int fa0/1: 2001:db8:20::1/64
int fa1/0: 2001:db8:2::1/64
```

**R3**
```
int fa0/0: 2001:db8:20::2/64
int fa0/1: 2001:db8:3::1/64
```

**Command Konfigurasi IP:**
```
int <nama interface>
ipv6 address <IPv6>/<prefix>
```

**Cek konfigurasi:**
```
sh ipv6 int br   // interface brief IPv6
sh ipv6 route    // tabel routing IPv6
```

---

## 📌 6. Static Route IPv6

Format:
```
ipv6 route <ipv6 network> <ipv6 next-hop>
```

**R1**
```
ipv6 route 2001:db8:20::/64 2001:db8:10::2
ipv6 route 2001:db8:2::/64 2001:db8:10::2
ipv6 route 2001:db8:3::/64 2001:db8:10::2
ipv6 unicast-routing
```

**R2**
```
ipv6 route 2001:db8:1::/64 2001:db8:10::1
ipv6 route 2001:db8:3::/64 2001:db8:20::2
ipv6 unicast-routing
```

**R3**
```
ipv6 route 2001:db8:10::/64 2001:db8:20::1
ipv6 route 2001:db8:1::/64 2001:db8:20::1
ipv6 route 2001:db8:2::/64 2001:db8:20::1
ipv6 unicast-routing
```

---

## 🚦 7. Dynamic Routing IPv6 – OSPF

IPv6 menggunakan **OSPFv3**. Bedanya:
- Tidak perlu advertise network, cukup aktifkan OSPF di interface.
- Wajib set **Router ID** dalam format IPv4.

**R1**
```
ipv6 router ospf 1
router-id 10.10.10.1
int fa0/0
 ipv6 ospf 1 area 0
int fa0/1
 ipv6 ospf 1 area 0
```

**R2**
```
ipv6 router ospf 2
router-id 10.10.10.2
int fa0/0
 ipv6 ospf 2 area 0
int fa0/1
 ipv6 ospf 2 area 0
int fa1/0
 ipv6 ospf 2 area 0
```

**R3**
```
ipv6 router ospf 3
router-id 10.10.10.3
int fa0/0
 ipv6 ospf 3 area 0
int fa0/1
 ipv6 ospf 3 area 0
```

**Cek Routing OSPF:**
```
do sh ipv6 route
```

---

## 🔄 8. Dynamic Routing IPv6 – EIGRP

Sebelum konfigurasi EIGRP, hapus OSPF:
```
no ipv6 router ospf <id>
```

**Konfigurasi EIGRP for IPv6:**
- ID EIGRP harus sama di semua router.
- Gunakan `no shutdown` di mode router.

**R1**
```
ipv6 router eigrp 10
eigrp router-id 10.10.10.1
no shutdown
int fa0/0
 ipv6 eigrp 10
int fa0/1
 ipv6 eigrp 10
```

**R2**
```
ipv6 router eigrp 10
eigrp router-id 10.10.10.2
no shutdown
int fa0/0
 ipv6 eigrp 10
int fa0/1
 ipv6 eigrp 10
int fa1/0
 ipv6 eigrp 10
```

**R3**
```
ipv6 router eigrp 10
eigrp router-id 10.10.10.3
no shutdown
int fa0/0
 ipv6 eigrp 10
int fa0/1
 ipv6 eigrp 10
```

---

## 🎯 Penutup

Dengan ini kita sudah mempelajari:
- Dasar IPv6 & jenis alamat
- Cara compress/uncompress
- Prefix & topologi
- Static routing IPv6
- Dynamic routing OSPF & EIGRP

Selamat! 🚀  
Lanjutkan dengan materi Wireless atau vendor lain seperti Mikrotik & Juniper.  
Terus upgrade skill networking kamu!