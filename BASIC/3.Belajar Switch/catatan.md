# 🧠 Analisis Konsep Layer 2: MAC Address, ARP, dan Switch

Dokumen ini membahas secara mendalam tentang konsep-konsep penting pada **Layer 2 (Data Link Layer)** dalam jaringan komputer, termasuk **MAC Address**, **ARP (Address Resolution Protocol)**, dan cara kerja **Switch**.

---

## 1. 🔍 MAC Address (Media Access Control Address)

MAC Address adalah alamat fisik yang unik dan ditanamkan di dalam Network Interface Card (NIC) dari setiap perangkat jaringan, seperti PC, laptop, router, dan switch.  
MAC Address bekerja pada **Layer 2** dari model OSI.

### 📌 Fungsi Utama:
Sebagai **identitas unik** perangkat di dalam jaringan lokal (LAN). Komunikasi antar perangkat di jaringan lokal menggunakan MAC Address untuk mengirim data.

Untuk menghubungkan IP Address (Layer 3) dengan MAC Address (Layer 2), digunakan **Address Resolution Protocol (ARP)**.

### 📂 Struktur MAC Address:
- Total: **48 bit** (12 digit heksadesimal)
- Format: `XX:XX:XX:XX:XX:XX`

Dibagi menjadi dua bagian:
- **OUI (Organizationally Unique Identifier)** - 24 bit pertama (misalnya `00:1A:4F`)
- **NIC Specific** - 24 bit terakhir, unik untuk setiap perangkat (misalnya `F1:4C:C6`)

### 🛠 Cara Melihat MAC Address:
- **Windows**: Jalankan `ipconfig /all` di Command Prompt, lihat bagian `Physical Address`.
- **Cisco Switch/Router**: Gunakan perintah `show interfaces`.

📷 Screenshot:
- [Konfigurasi MAC Address di PC](https://drive.google.com/file/d/1dSRM9MV_6Ij9qgo0ZrwYd9CAkdZrHSFQ/view)
- [MAC Address di Switch Cisco](https://drive.google.com/file/d/1ObaIy4w3CR88UkHgrqX940z4oT-4U4TG/view)

---

## 2. 🔁 Proses Terbentuknya Tabel ARP

Tabel ARP menyimpan pemetaan antara IP Address dan MAC Address yang sudah diketahui. Berikut simulasi alur komunikasi:

### 🧪 Contoh:
**PC A (192.168.1.10)** ingin mengirimkan ping ke **PC B (192.168.1.11)**:

#### Langkah-langkah:
1. **Cek ARP Cache**  
   PC A memeriksa apakah IP 192.168.1.11 sudah ada di ARP cache (`arp -a`). Jika belum:

2. **Kirim ARP Request**  
   PC A mengirim pesan broadcast ke semua perangkat di LAN:  
   ```
   "Siapa pemilik IP 192.168.1.11? Ini saya, 192.168.1.10."
   ```
   MAC tujuan: `FF:FF:FF:FF:FF:FF`

3. **Perangkat Lain Mengabaikan**  
   Kecuali PC B, semua perangkat lain (PC C, D, E) akan mengabaikan pesan ini.

4. **ARP Reply oleh PC B**  
   PC B merespons langsung (unicast):  
   ```
   "Saya 192.168.1.11, MAC saya adalah [MAC PC B]"
   ```

5. **Update Tabel ARP**  
   PC A menyimpan MAC PC B dalam tabelnya dan baru kemudian mengirim ping (ICMP) dengan header yang berisi MAC tujuan dari PC B.

---

## 3. 🧪 Simulasi: 3 PC & 1 Switch

**Topologi:**
- PC0: `192.168.1.1`
- PC1: `192.168.1.2`
- PC2: `192.168.1.3`

📷 [Gambar Topologi](https://drive.google.com/file/d/1ysHySCMIldmxEFQZXgP359lvw8feAhPb/view)

Setelah ping antar-PC dilakukan, kita bisa melihat tabel MAC Switch dengan:
```
show mac-address-table
```

📷 [Hasil MAC Address Table](https://drive.google.com/file/d/1cmcAD_hjXtyPehVyCbiw5WOuJCHTvv0q/view)

---

## 4. ⚙️ Cara Switch Membangun Tabel MAC

Switch adalah perangkat pintar Layer 2. Berikut cara kerjanya:

### 📥 Proses Learning (Belajar)
Saat frame masuk, switch:
- Mencatat **MAC Address sumber** dan **port masuk**.
- Menyimpannya dalam **MAC Address Table**.

### 📤 Proses Forwarding (Penerusan)
- **MAC Tujuan Dikenal** → Switch langsung mengirim ke port terkait.
- **MAC Tujuan Tidak Dikenal** → Switch akan melakukan **flooding** (broadcast ke semua port kecuali asal).

### 🔄 Pembelajaran Lanjutan
Setelah menerima balasan dari perangkat tujuan, switch mencatat MAC dan port perangkat tersebut → next time tidak perlu flooding.

### 🔧 Reset ARP & MAC Table
- **PC**: `arp -d`
- **Switch**: `clear mac-address-table dynamic`

---

## 5. 🌐 Broadcast Domain

Broadcast Domain adalah area jaringan di mana semua perangkat menerima pesan broadcast, seperti ARP Request.

📌 Semua port pada switch **secara default berada dalam 1 broadcast domain**.  
Untuk memisahkan broadcast domain, kita perlu menggunakan **router** atau membuat **VLAN**.

---

Terima kasih dan Sekian dari section ini, semoga bermanfaat dan keep on track! 🚀💻
