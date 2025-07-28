saat ini semua vendor menggunakan network model yang sama yakni model TCP/IP yang terdiri dari 

1.Application Layer
2.Transport Layer
3.Network Layer
4.Data Link Layer
5.Physical Layer

A.Application Layer

merupakan layer pertama dalam model TCP/IP layer ini berfungsi sebagai Jembatan antara komputer
ke Internet/Web browser, cara kerja nya adalah komputer akan mengirimkan beberapa request ke sebuah situs
lalu web server dari situs tersebut akan memberikan balasan berupa http header yang berisi kode-kode dan juga data yang dipinta.
adapun beberapa http header code yang umum yakni: 

code 200 OK(menandakan bahwa web server mengerti dan memiliki data yang dipinta oleh komputer)
code 404 Not Found (menandakan bahwa web server tidak memiliki data yang diinginkan oleh komputer
misal komputer meminta request ke link web "www.wessite.com/about" dan ternyata link "about" tidak tersedia
di web server sehingga web server tidak bs memberikan data lanjutan )


Rincian Simulasi CPT :

1. siapkan satu buah Server dengan IP 192.168.1.1/24
2. satu buah PC/Laptop dengan IP 192.168.1.2/24
3. coba ping dari PC ke Server
4. ubah salah satu laman web yang tersedia di server dengan cara pergi ke menu "Service", cari "index.html", lalu edit sesuai dengan apa yang kamu inginkan
5. pergi ke sisi PC lalu akses menu "browser", masukkan ip server lalu klik 'Go', amati perubahan yang terjadi

B. Transport Layer
Transport Layer ini berfungsi sebagai pemberi Service ke Application Layer, service yang diberikan lebih ke sisi TCP nya yakni dikenal dengan nama "Error Recovery Mechanism"
cara kerjanya adalah web server akan memberikan data-data yang di request oleh komputer secara 'sequence' (satu per satu), bila di sequence tertentu terjadi galat seperti internet mati dll, maka web server akan mengirimkan ulang 'sequence' yang tertinggal ketika 
internet nya sudah kembali normal jadi bukan mengirimkan ulang semua data tapi hanya data yang terputus saja.

C.Network layer

pada lyer ini hanya ada satu Service yang digunakan yakni "IP" dimana "IP" ini memiliki 2 fungsi penting
yakni untuk "Routing" dan juga "Addressing", IP terdiri dari 2 Jenis yakni IPV4 dan IPV6, dengan yang paling umum digunakan adalah IPV4,

1.Addressing
Addressing merupakan fungsi/nama  lain dari "IP" itu sendiri yang mana bertugas untuk "memberikan nama" ke masing-masing komputer agar bisa saling berkomunikasi

contoh 

komputer 1(192.168.1.1)  ; Komputer 2(192.168.1.2), disini ketika Komputer 2 ingin berkomunikasi dengan komputer satu maka yang akan digunakan adalah IP "192.168.1.1" yang mana merupakan nama / address dari komputer tersebut

2. Routing
Routing merupakan fungsi utama yang ada pada layer ini dan dimiliki ole device "Router" prinsip atau cara kerja nya mirip seperti Kantor Pos, dimana ketika kita ingin mengirimkan suatu data ke suatu tempat maka router akan menentukan jalur tercepat berdasarkan routing yang di buat sehingga akses data dapat 
dilakukan dengan efisien

D. Data Link Layer
data Link layer berfungsi untuk mendefinisikan protokol yang akan digunakan pada Physical Layer, salah satu service yang ada pada layer ini adalah "FCS(Frame Check Sequence)" yang berfungsi sebagai error Detecetion , namun cara kerjanya sedikit berbeda dengan "Error Recovery Mechanism" yang ada pada "Transport Layer". dimana FCS itu hanya bisa mendeteksi adanya error tetapi tidak bisa melakukan recovery sehingga antara layer ini dengan Transport layer memiliki hubungan yang saling melengkapi dimana ketika layer Data link mendeteksi adanya error maka layer Transport akan memperbaiki error tersebut dengana mekanisme nya.

E. Physical Layer

layer ini merupakan tampilan luar yang terlihat dan bisa dipegang secara real oleh kita seperti Laptop/PC, Kabel LAN, Router , dan hardware-hardware lain yang membentuk sebuah jaringan Komputer.

Tambahan:

1.Encapsulation & Decapsulation

-Encapsulation adalah proses dimana komputer pengirim(sender) membungkus data yang ingin dikirimkan menggunakan beberapa header lalu diakhir akan diconvert ke dalam bentuk Biner

- Decapsulation adalah proses dimana komputer penerima(receiver) akan membuka "Bungkus" dari Biner yang diberikan oleh Komputer(sender), lalu menghapus semua header yang ada sampai tersisa hanya 'data' nya saja.

contoh simulasi proses :

#Encapsulation --> "Data" --> |TCP| DATA| --> |IP| |TCP| DATA| --> |Data Link(Header)| |IP| |TCP| |DATA| |Data Link(Tryler)| ---> Biner(1000101) -------> #Decapsulation 1000101 --> |Data Link(Header)| |IP| |TCP| |DATA| |Data Link(Tryler)| --> |IP| |TCP| DATA| ---> |TCP| DATA| --> "Data"


2. Name of TCP/IP Message

- Data --> Data
- |TCP| DATA| --> Segment
- |IP| |TCP| DATA| --> IP Packet
- |Data Link(Header)| |IP| |TCP| |DATA| |Data Link(Tryler)| --> Frame
- BINER(BIT)

3. TCP/IP vs OSI layer

TCP/IP Merupakan versi lebih ringkas dari OSI Layer dimana di OSI 3 Layer awal itu dimulai dari Application --> Presentation --> dan Session , sementara di TCP/IP itu dijadikan satu Application Layer saja sehingga membuat proses nya lebih cepat dan efisien, namun keunggulan dari OSI layer adalah karena OSI layer ini lebih mudah dipahami mekanisme nya dan cocok digunakn untuk pembelajaran awal. selain itu OSI Layer juga memiliki penamaan yang berbeda dengan TCP/IP berikut perbandingannya: 

Name of TCP/IP Message:

- Data 
- Transport --> Segment
- Network --> IP Packet
- Data Link --> Frame
- Physical(BINER(BIT))

Name of OSI Message:

- Application --> Layer7PDU
- Presentation --> Layer6PDU
- Session --> Layer5PDU
- Transport --> Layer4PDU
- Network --> Layer3PDU
- Data Link --> Layer2PDU
- Physical(BINER(BIT))