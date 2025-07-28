# Model Jaringan TCP/IP

Saat ini, semua vendor menggunakan model jaringan **TCP/IP** yang terdiri dari lima layer berikut:

1. **Application Layer**
2. **Transport Layer**
3. **Network Layer**
4. **Data Link Layer**
5. **Physical Layer**

---

## A. Application Layer

Layer pertama dalam model TCP/IP yang berfungsi sebagai jembatan antara komputer dengan layanan internet seperti web browser.

### Cara Kerja:
Komputer mengirimkan request ke situs web. Web server kemudian membalas dengan HTTP header yang berisi kode status dan data yang diminta.

### Contoh HTTP Header Code:
- **200 OK**: Web server mengerti dan memiliki data yang diminta oleh komputer.
- **404 Not Found**: Web server tidak memiliki data yang diminta. Misalnya, jika komputer meminta `www.website.com/about` tetapi halaman "about" tidak tersedia di server.

### Simulasi di Cisco Packet Tracer (CPT):
1. Siapkan sebuah server dengan IP: `192.168.1.1/24`
2. Siapkan satu PC/laptop dengan IP: `192.168.1.2/24`
3. Lakukan `ping` dari PC ke Server
4. Ubah konten halaman web pada server melalui menu **Services > HTTP > index.html**, lalu edit sesuai keinginan.
5. Dari PC, buka menu **Web Browser**, masukkan IP server, klik **Go**, dan amati perubahan.

---

## B. Transport Layer

Berfungsi memberikan layanan ke Application Layer, khususnya dalam pengiriman data yang andal menggunakan **TCP (Transmission Control Protocol)**.

### Fitur Utama:
- **Error Recovery Mechanism**: Data dikirim dalam bentuk urutan (sequence). Jika terjadi gangguan (misalnya koneksi internet terputus), hanya bagian data yang gagal dikirim ulang.

📷 Gambar simulasi: [Link Gambar](https://drive.google.com/open?id=1KaXyFPqLIU9APXhi6f4D1ZBnDi7ZF7bi&usp=drive_fs)

---

## C. Network Layer

Menyediakan dua layanan utama: **Addressing** dan **Routing**. Protokol utama yang digunakan adalah **IP (Internet Protocol)**, terdiri dari IPv4 dan IPv6.

### 1. Addressing:
Memberikan identitas unik (IP Address) ke setiap perangkat jaringan agar dapat saling berkomunikasi.

Contoh:
- Komputer 1: `192.168.1.1`
- Komputer 2: `192.168.1.2`

Komputer 2 dapat mengakses Komputer 1 menggunakan alamat `192.168.1.1`.

### 2. Routing:
Routing dilakukan oleh perangkat **Router** yang berfungsi menentukan jalur tercepat untuk pengiriman data, seperti sistem kantor pos yang mengarahkan surat.

📷 Gambar simulasi: [Link Gambar](https://drive.google.com/open?id=1km53Hs5ueG6-JAIsRk4ZuUgw3xmique2&usp=drive_fs)

---

## D. Data Link Layer

Berfungsi mendefinisikan protokol pada layer fisik. Salah satu layanannya adalah **FCS (Frame Check Sequence)**.

### Fitur:
- **FCS** berfungsi sebagai **Error Detection**.
- Tidak memperbaiki error, hanya mendeteksi. Untuk perbaikan, digunakan mekanisme dari Transport Layer.

---

## E. Physical Layer

Layer paling bawah dalam model TCP/IP. Merupakan elemen jaringan yang bisa dilihat dan disentuh secara fisik.

### Contoh:
- Laptop/PC
- Kabel LAN
- Router
- Perangkat keras lainnya

---

## Tambahan Materi

### 1. Encapsulation & Decapsulation

- **Encapsulation**: Proses membungkus data oleh komputer pengirim ke dalam beberapa header dan diubah menjadi bentuk biner.
- **Decapsulation**: Proses komputer penerima membuka dan membuang header sampai hanya menyisakan data utama.

#### Contoh Proses:

```
Encapsulation:
"Data" → |TCP|DATA| → |IP||TCP|DATA| → |Data Link Header||IP||TCP||DATA||Data Link Trailer| → Biner (1000101)

Decapsulation:
Biner (1000101) → |Data Link Header||IP||TCP||DATA||Data Link Trailer| → |IP||TCP|DATA| → |TCP|DATA| → "Data"
```

📷 Gambar simulasi: [Link Gambar](https://drive.google.com/open?id=1zIOwlwgf4CFfmGzov2S2my4RIfrTOppL&usp=drive_fs)

---

### 2. Nama Unit Data dalam TCP/IP

| Layer          | Nama Unit Data    |
|----------------|-------------------|
| Application    | Data              |
| Transport      | Segment           |
| Network        | Packet (IP Packet)|
| Data Link      | Frame             |
| Physical       | Bit (Biner)       |

---

### 3. TCP/IP vs OSI Model

- TCP/IP adalah versi ringkas dari OSI Layer.
- Pada OSI, tiga layer teratas adalah Application, Presentation, dan Session, sedangkan TCP/IP menggabungkannya menjadi satu layer: Application Layer.
- OSI lebih cocok untuk pembelajaran karena lebih detail dan mudah dipahami.

#### Perbandingan Unit Data TCP/IP vs OSI:

| Layer (TCP/IP)     | Unit Data         | Layer (OSI)       | Unit Data         |
|--------------------|-------------------|--------------------|-------------------|
| Application        | Data              | Application (L7)   | Layer7PDU         |
| Transport          | Segment           | Presentation (L6)  | Layer6PDU         |
| Network            | Packet            | Session (L5)       | Layer5PDU         |
| Data Link          | Frame             | Transport (L4)     | Layer4PDU         |
| Physical           | Bit               | Network (L3)       | Layer3PDU         |
|                    |                   | Data Link (L2)     | Layer2PDU         |
|                    |                   | Physical (L1)      | Bit (Biner)       |
