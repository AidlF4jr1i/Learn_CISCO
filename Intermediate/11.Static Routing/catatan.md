
# Konfigurasi Static Routing dan VLAN di Cisco Packet Tracer

## 📌 Pengantar

Routing merupakan cara untuk menghubungkan beberapa PC yang berada dalam subnet berbeda agar bisa saling berkomunikasi. Untuk mewujudkannya, kita membutuhkan perangkat seperti **Router** atau **Multi-Layer Switch**.

Salah satu metode routing adalah **Static Routing**, di mana kita **memasukkan informasi IP network secara manual** ke dalam setiap router yang terhubung.

---

## 🔁 Topologi 2 Router

📷 Gambar Topologi: [Klik di sini](https://drive.google.com/open?id=1-fq8Tij9YjnW9XNZLMGr2d90W4FVClRi&usp=drive_fs)

### 📐 Alokasi IP Address:

- **R1**:
  - Ke client: `192.168.1.1/24`
  - Ke R2: `10.10.10.1/24`
- **R2**:
  - Ke client: `192.168.2.1/24`
  - Ke R1: `10.10.10.2/24`
- IP PC client mengikuti subnet masing-masing.

### ⚙️ Konfigurasi

#### R1:

```shell
en
conf t
int fa0/0
 ip address 10.10.10.1 255.255.255.0
 no shutdown
exit
int fa0/1
 ip address 192.168.1.1 255.255.255.0
 no shutdown
exit
ip route 192.168.2.0 255.255.255.0 10.10.10.2
end
```

#### R2:

```shell
en
conf t
int fa0/0
 ip address 10.10.10.2 255.255.255.0
 no shutdown
exit
int fa0/1
 ip address 192.168.2.1 255.255.255.0
 no shutdown
exit
ip route 192.168.1.0 255.255.255.0 10.10.10.1
end
```

> ✅ Setelah konfigurasi, cobalah `ping` dari PC R1 ke PC R2, komunikasi seharusnya berhasil jika routing sudah benar.

---

## 🔁 Topologi 4 Router

📷 Gambar Topologi: [Klik di sini](https://drive.google.com/open?id=1pSZoqkN4TOuRBFbTyOC2-XeP1T0gK94M&usp=drive_fs)

### 📐 Alokasi IP Address:

**Router 1 - 4 memiliki struktur IP sebagai berikut:**

- **R1**:
  - Ke client: `192.168.1.1/24`
  - Ke R2: `10.10.10.1/24`

- **R2**:
  - Ke client: `192.168.2.1/24`
  - Ke R1: `10.10.10.2/24`
  - Ke R3: `10.20.20.1/24`

- **R3**:
  - Ke client: `192.168.3.1/24`
  - Ke R2: `10.20.20.2/24`
  - Ke R4: `10.30.30.1/24`

- **R4**:
  - Ke client: `192.168.4.1/24`
  - Ke R3: `10.30.30.2/24`

### ⚙️ Konfigurasi Lengkap:

**R1**:

```shell
en
conf t
int fa0/0
 ip address 10.10.10.1 255.255.255.0
 no shutdown
exit
int fa0/1
 ip address 192.168.1.1 255.255.255.0
 no shutdown
exit
ip route 192.168.2.0 255.255.255.0 10.10.10.2
ip route 10.20.20.0 255.255.255.0 10.10.10.2
ip route 10.30.30.0 255.255.255.0 10.10.10.2
ip route 192.168.3.0 255.255.255.0 10.10.10.2
ip route 192.168.4.0 255.255.255.0 10.10.10.2
end
```

**R2**:

```shell
en
conf t
int fa0/0
 ip address 10.10.10.2 255.255.255.0
 no shutdown
exit
int fa0/1
 ip address 192.168.2.1 255.255.255.0
 no shutdown
exit
int fa1/0
 ip address 10.20.20.1 255.255.255.0
 no shutdown
exit
ip route 192.168.1.0 255.255.255.0 10.10.10.1
ip route 10.30.30.0 255.255.255.0 10.20.20.2
ip route 192.168.3.0 255.255.255.0 10.20.20.2
ip route 192.168.4.0 255.255.255.0 10.20.20.2
end
```

**R3**:

```shell
en
conf t
int fa0/0
 ip address 10.20.20.2 255.255.255.0
 no shutdown
exit
int fa0/1
 ip address 192.168.3.1 255.255.255.0
 no shutdown
exit
int fa1/0
 ip address 10.30.30.1 255.255.255.0
 no shutdown
exit
ip route 192.168.2.0 255.255.255.0 10.20.20.1
ip route 192.168.1.0 255.255.255.0 10.20.20.1
ip route 10.10.10.0 255.255.255.0 10.20.20.1
ip route 192.168.4.0 255.255.255.0 10.30.30.2
end
```

**R4**:

```shell
en
conf t
int fa0/0
 ip address 10.30.30.2 255.255.255.0
 no shutdown
exit
int fa0/1
 ip address 192.168.4.1 255.255.255.0
 no shutdown
exit
ip route 192.168.3.0 255.255.255.0 10.30.30.1
ip route 192.168.2.0 255.255.255.0 10.30.30.1
ip route 192.168.1.0 255.255.255.0 10.30.30.1
ip route 10.10.10.0 255.255.255.0 10.30.30.1
ip route 10.20.20.0 255.255.255.0 10.30.30.1
end
```

> ⚠️ Konfigurasi ini sangat repetitif dan memerlukan ketelitian tinggi — kekuatan Static Routing ada pada kontrol manual, namun kelemahannya adalah skalabilitas rendah.

---

## 🌐 Konfigurasi VLAN pada Router 4

📷 Gambar Topologi: [Klik di sini](https://drive.google.com/open?id=18G8fqpnmHIhK1onydrc_uu0Stjlm87ih&usp=drive_fs)

### R4:

```shell
en
conf t
int fa1/0.40
 encapsulation dot1q 40
 ip address 192.168.40.1 255.255.255.0
exit
int fa1/0.50
 encapsulation dot1q 50
 ip address 192.168.50.1 255.255.255.0
exit

ip dhcp pool VLAN40
 network 192.168.40.0 255.255.255.0
 default-router 192.168.40.1
 dns-server 8.8.8.8

ip dhcp pool VLAN50
 network 192.168.50.0 255.255.255.0
 default-router 192.168.50.1
 dns-server 8.8.8.8
```

### Switch (Terhubung ke R4):

```shell
en
conf t
int fa0/5
 switchport mode trunk

vlan 40
exit
vlan 50
exit

do show vlan brief

int range fa0/1-2
 switchport mode access
 switchport access vlan 40

int range fa0/3-4
 switchport mode access
 switchport access vlan 50
end
```

### Tambahan Routing VLAN:

#### R3:

```shell
ip route 192.168.40.0 255.255.255.0 10.30.30.2
ip route 192.168.50.0 255.255.255.0 10.30.30.2
```

#### R2:

```shell
ip route 192.168.40.0 255.255.255.0 10.20.20.2
ip route 192.168.50.0 255.255.255.0 10.20.20.2
```

#### R1:

```shell
ip route 192.168.40.0 255.255.255.0 10.10.10.2
ip route 192.168.50.0 255.255.255.0 10.10.10.2
```

---

## ✅ Verifikasi & Pengujian

Lakukan `ping` antar PC dan Router. Pastikan semua perangkat dapat saling berkomunikasi. Anda bisa melihat demo hasilnya di video berikut:

🎥 [Klik untuk melihat video](https://drive.google.com/open?id=1YSb50yGbQMrVO3bL1wznwMJa1melTcIl&usp=drive_fs)

---

## 🎉 Penutup

Dengan konfigurasi ini, kamu telah memahami prinsip dasar **Static Routing**

Selamat mencoba, dan terima kasih! 🙌
