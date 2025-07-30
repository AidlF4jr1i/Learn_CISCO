# 🔐 Pengenalan Telnet & SSH

**Telnet** dan **SSH** adalah dua protokol yang digunakan untuk meremote perangkat jaringan seperti **Router** atau **Switch** tanpa harus terhubung langsung secara fisik.

- **Telnet**: Tidak terenkripsi (tidak aman)
- **SSH (Secure Shell)**: Menggunakan enkripsi, jauh lebih aman dibanding Telnet

Sebagai permulaan, kita akan mencoba melakukan remote router via **Telnet** menggunakan topologi dari file `CASE_VLAN.pkt`.

---

## 🛠️ Konfigurasi Telnet pada Router

Langkah-langkah:

```bash
en
conf t
line vty 0 2                  # Maksimal 3 user (vty 0, 1, 2)
password telnet123           # Password Telnet
login                        # Memaksa user memasukkan password
transport input telnet       # Hanya mengizinkan Telnet
enable secret <password>     # Password untuk masuk Privileged Mode
```

---

## 👤 Telnet dengan Username & Password

Jika ingin agar user diminta **username dan password**, gunakan konfigurasi berikut:

```bash
username user1 secret password1  # Buat user login
line vty 0 2
login local                      # Memaksa masukkan username + password
```

📷 Referensi:  
[Konfigurasi Telnet di CPT](https://drive.google.com/open?id=1P2fLDvcvRKy9H-UcA43TT9L1XVrizxJX&usp=drive_fs)

> 🛡️ **NOTE:** Telnet tidak aman karena seluruh data termasuk password dikirim secara plain-text.  
> Di Cisco Packet Tracer, kamu bisa **simulasikan sniffing** dengan:
> - Masuk ke **Simulation Mode**
> - Checklist hanya protokol **Telnet**
> - Lihat traffic yang dikirim  
📷 Contoh gambar:  
[Simulasi Sniffing Telnet](https://drive.google.com/open?id=11CwurGdWJhGsJvCNc_aQ_c5BiEOnOoKK&usp=drive_fs)

---

# 🛡️ Konfigurasi SSH pada Router

SSH lebih aman daripada Telnet karena menggunakan enkripsi. Berikut langkah-langkah konfigurasi SSH:

```bash
en
conf t
hostname R1                                # Ganti hostname router
ip domain-name aidil.id                    # Tentukan domain
crypto key generate rsa                    # Generate RSA Key
# Masukkan ukuran key: 1024
username admin secret admin123             # Buat user login
line vty 0 2
transport input ssh                        # Hanya izinkan SSH
login local                                # Gunakan login dengan username/password
```

---

# 🔌 Konfigurasi IP Address pada Switch

Agar bisa diremote, setiap switch harus diberikan IP **melalui VLAN Interface**.

## Switch 1:

```bash
int vlan 10
ip address 192.168.10.30 255.255.255.0
ip default-gateway 192.168.10.1            # IP Gateway Router VLAN 10
```

## Switch 2:

```bash
int vlan 10
ip address 192.168.10.31 255.255.255.0
ip default-gateway 192.168.10.1
```

🔁 Tes konektivitas ke router:

```bash
do ping 192.168.10.1
```

Jika **tidak ada RTO**, berarti switch siap diremote.

---

# 🔐 Konfigurasi SSH pada Switch

Langkahnya hampir sama seperti di router. Pastikan switch bisa ping router, lalu lakukan konfigurasi berikut:

## Switch 1 (SW1):

```bash
en
conf t
hostname SW1
ip domain-name aidil.id
crypto key generate rsa
# Masukkan ukuran key: 1024
username admin secret admin123
line vty 0 4                              # Maksimal 5 user
transport input ssh
login local
```

## Switch 2 (SW2):

```bash
en
conf t
hostname SW2
ip domain-name aidil.id
crypto key generate rsa
# Masukkan ukuran key: 1024
username admin secret admin123
line vty 0 4
transport input ssh
login local
```

---

# 🖥️ Pengujian Remote via PC

Gunakan PC yang berada dalam jaringan VLAN 10, lalu buka terminal dan jalankan:

```bash
telnet 192.168.10.30       # Untuk mengakses SW1 via Telnet (jika masih aktif)
ssh -l admin 192.168.10.30 # Untuk akses SSH (lebih aman)
```

> Pastikan firewall dan koneksi VLAN berjalan dengan benar agar remote berhasil.

---

Semoga penjelasan ini membantumu memahami perbedaan dan cara konfigurasi **Telnet vs SSH**, serta bagaimana implementasi SSH yang aman untuk router dan switch.  
Tetap semangat! 🚀🧠