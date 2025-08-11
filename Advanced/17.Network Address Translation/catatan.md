# Network Address Translation (NAT)

NAT (**Network Address Translation**) adalah salah satu metode yang digunakan untuk menerjemahkan IP Private menjadi IP Public, sehingga perangkat di jaringan lokal dapat mengakses layanan internet seperti Google, YouTube, dan lain-lain.

Secara umum, NAT memiliki dua fungsi utama:
1. **Menghemat penggunaan IP Public** – banyak perangkat di LAN dapat berbagi satu IP Public.
2. **Memberikan keamanan tambahan** – alamat IP asli perangkat di dalam jaringan tidak langsung terlihat dari luar.

Pada praktik kali ini, kita akan mempelajari dan mengonfigurasi **Dynamic NAT (Many-to-One)** dan **Static NAT (One-to-One)** menggunakan topologi yang sudah disiapkan.  

---

## 📌 1. Dynamic NAT (Many-to-One)

Dynamic NAT atau PAT (**Port Address Translation**) memungkinkan banyak IP Private diterjemahkan menjadi **satu** IP Public secara bersamaan.  
Di sini kita akan menggunakan topologi berikut:  
🔗 [Topologi Dynamic NAT](https://drive.google.com/open?id=1VnyfSnWxIAAwnj6syvxmq9UlxpkXTMu_&usp=drive_fs)

### **Device dan Ketentuan:**

1. **Server (Google Server)**
   - Analogi: Server Google yang akan diakses oleh komputer klien
   - IP: `172.16.10.2/24`
   - Gateway: `172.16.10.1`

2. **R1 (Router Google)**
   - IP: `172.16.10.1/24` (fa0/0)  
   - IP: `10.10.10.1/24` (fa0/1) → menuju Router ISP
   - Konfigurasi **BGP AS 1**

3. **R2 (Router ISP)**
   - IP: `10.10.10.2/24` (fa0/0)  
   - IP: `10.20.20.1/24` (fa0/1) → menuju Router Client
   - Konfigurasi **BGP AS 2**

4. **R3 (Router Client)**
   - IP: `10.20.20.2/24` (fa0/0) → menuju Router ISP
   - IP: `192.168.10.1/24` (fa0/1.10) → VLAN 10
   - IP: `192.168.20.1/24` (fa0/1.20) → VLAN 20
   - Menyediakan **DHCP Server** untuk klien

5. **Switch**
   - Menghubungkan antar VLAN dalam kantor
   - VLAN 10 dan VLAN 20

---

### **Konfigurasi BGP**

**R1 (Router Google)**:
```
router bgp 1
 neighbor 10.10.10.2 remote-as 2
 network 10.10.10.0 mask 255.255.255.0
 network 172.16.10.0 mask 255.255.255.0
```

**R2 (Router ISP)**:
```
router bgp 2
 neighbor 10.10.10.1 remote-as 1
 network 10.10.10.0 mask 255.255.255.0
 network 10.20.20.0 mask 255.255.255.0
```

---

### **Konfigurasi NAT Dynamic di R3 (Router Client)**

**Default Route**:
```
ip route 0.0.0.0 0.0.0.0 10.20.20.1
```

**Access List untuk NAT**:
```
access-list 1 permit 192.168.10.0 0.0.0.255
access-list 1 permit 192.168.20.0 0.0.0.255
```

**NAT Configuration**:
```
ip nat inside source list 1 interface fa0/0 overload
```

**Menentukan Interface NAT**:
```
interface fa0/0
 ip nat outside
exit
interface fa0/1.10
 ip nat inside
exit
interface fa0/1.20
 ip nat inside
```

💡 **Catatan:** Pastikan `ip nat inside` diterapkan pada interface yang mengarah ke jaringan lokal, dan `ip nat outside` pada interface yang mengarah ke jaringan publik/ISP.

---

## 📌 2. Static NAT (One-to-One)

Static NAT menerjemahkan **satu IP Private** menjadi **satu IP Public** secara permanen.  
Biasanya digunakan jika kita ingin **server lokal dapat diakses dari internet**.

🔗 [Topologi Static NAT](https://drive.google.com/open?id=1RHFhZBQTm8xBvDlsMtwbqbbDy8XLOCAg&usp=drive_fs)

### **Device dan Ketentuan:**

1. **Server Lokal**
   - IP: `192.168.30.2/24`
   - Gateway: `192.168.30.1`

2. **R3 (Router Client)**
   - VLAN 30 untuk server lokal:
     ```
     interface fa0/1.30
      encapsulation dot1q 30
      ip address 192.168.30.1 255.255.255.0
     exit
     ```
   - NAT Static:
     ```
     ip nat inside source static 192.168.30.2 10.20.20.3
     ```
   - Menentukan Interface NAT:
     ```
     interface fa0/1.30
      ip nat inside
     interface fa0/0
      ip nat outside
     ```

💡 **Penjelasan:**  
Dengan konfigurasi ini, setiap permintaan ke IP Public `10.20.20.3` akan langsung diteruskan ke server lokal `192.168.30.2`.

---

## 🎯 Pengujian
- Untuk **Dynamic NAT**, coba kirimkan paket dari komputer VLAN 10 atau VLAN 20 menuju server Google (`172.16.10.2`) dan amati di mode *Simulation*.  
  Anda akan melihat IP Private diterjemahkan menjadi IP Public milik Router Client.  

- Untuk **Static NAT**, coba akses IP Public `10.20.20.3` dari jaringan luar (misalnya di Router Google) dan pastikan dapat membuka web server lokal.

---

## 📚 Kesimpulan
- **Dynamic NAT** cocok untuk klien yang hanya mengakses internet (Many-to-One).  
- **Static NAT** cocok untuk server lokal yang harus diakses dari luar (One-to-One).  
- Pemahaman perbedaan ini penting agar konfigurasi NAT sesuai kebutuhan jaringan.
