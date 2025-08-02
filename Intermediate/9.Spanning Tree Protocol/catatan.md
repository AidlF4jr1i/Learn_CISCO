# STP(Spanning Tree Protocol)

STP merupakan protokol yang berfungsi untuk melakukan block terhadap interface yang dijadikan cadangan pada suatu topologi jaringan untuk menghindari terjadinya looping arp packet, ketika link utama mati, maka bloking dari STP akan secara otomatis dimatikan dan link cadangan dapat menerima packet secara normal, by default di perangkat Cisco STP itu sudah otomatis aktif


# Topologi STP

topologi:https://drive.google.com/open?id=1q0EAh2Fq7fGn81oA-YP1Z9sKMP8iYQaA&usp=drive_fs

pada praktek kali ini kita akan menyimulasikan STP menggunakan 2 switch dan 2 PC seperti pada gambar, dengan masing-masing PC dikonfigurasi IP 192.168.1.0/24.

untuk keperluan pengujian dan melihat looping yang terjadi bila STP dimatikan pada ke-2 Switch kita akan matikan STP dengan command "no spanning-tree vlan <id vlan> atau dalam hal ini karena tidak ada vlan yang dikonfig maka base nya adalah vlan 1(native vlan)"

NOTE: "PADA JARINGAN REAL, JANGAN SAMA SEKALI BERPIKIRIAN UNTUK MENON AKTIFKAN STP, KARENA DAPAT MENYEBABKAN LOOPING DAN DOWN YANG SANGAT CEPAT PADA JARINGAN REAL DEVICE!!!"

Hasilnya dapat dilihat pada gif berikut:https://drive.google.com/open?id=1Ph3F38d9Icjw7YX2a6khagRzYHzyYloa&usp=drive_fs

# Topology STP 3 Switch

untuk mendalami cara kerja penentuan port mana yang akan di block dalam mode STP kita akan emnambahkan satu Switch lagi untuk mengupas lebih lanjut mengenai bagaimana penentuan port yang diblok itu berlangsung


dalam menentukan port yang akan di blok (dalam hal ini disebut dengan 'alternate port'),kita harus terlebih dahulu mengetahui mengenai :

1. Root Bridge --> Bridge disini merupakan istilah lain untuk Switch, Root Bridge adalah sebuah Switch yang berfungsi sebagai "Raja" nya dimana setiap interface nya dapat dipastikan merupakan port yang pasti akan UP atau disebut juga 'Designated Port', untuk menentukan Root bridge kita harus menentukan yang namanya 'Bridge ID', dimana Bridge ID didapatkan dari:
- priority(selalu sama yakni '32768' untuk defaultnya)
- mac-address(yang paling kecil, perlu diingat mac-address merupakan kumpulan angka dan hruf yang tercakup dalam hexadecimal)

note : "untuk priority lengkapnya itu Default + id Vlan nya, jadi kisal di switch kita memiliki vlan 1, maka priority nya menjadi 32768 + 1 = 32769"

2. Designated Port
merupakan port atau interface yang pasti akan UP apapun yang terjadi, dan biasanya merupakan kepunyaan dari si Root Bridge
3. Root Port
merupakan port atau interface yang memiliki jalur tercepat menuju root bridge
4. Alternate Port
merupakan port yang akan diisolasi (di blok) agar tidak terjadi Looping, 


kamu bisa melihat full topologi nya disini :https://drive.google.com/open?id=1b4biIVp1Tesa6MdeSMrXDlO5tqo5RXiy&usp=drive_fs


Analisis Topologi
Root Bridge: SW A terpilih sebagai Root Bridge. Ini karena SW A memiliki MAC Address terendah (0001.4258.52a2) dibandingkan SW B (00d0.bade.5633) dan SW C (000d.bda1.bd3c). Semua port aktif di Root Bridge akan menjadi Designated Port.

Root Port: Setiap Switch non-root (SW B dan SW C) akan memilih satu port dengan jalur terpendek  menuju Root Bridge.

SW B memilih Fa0/2 sebagai Root Port.

SW C memilih Fa0/2 sebagai Root Port.

Karena SW C memiliki Bridge ID (MAC Address) yang lebih rendah(000d.bda1.bd3c) daripada SW B(00d0.bade.5633), maka port di sisi SW C, yaitu Fa0/1, berhak menjadi Designated Port pada segmen tersebut.

Akibatnya, port di sisi lawannya, yaitu Fa0/2 pada SW B, harus mengalah dan masuk ke status blocking (disebut Alternate Port dalam terminologi RSTP) untuk mencegah terjadinya looping. 

terakhir kamu bisa memverifikasi hasil perhitungan dan perbandingan mu dengan  melihat STP pada masing-masing switch dengan command "sh spanning-tree" atau "do sh spanning-tree"

Tambahan:

penjelasan mengenai cost(salah satu info yang ditampilkan pada command sebelumnya):
- Cost adalah parameter yang digunakan untuk menentukan yang mana jalur tercepat/terpendek menuju sebuah switch atau dalam hal ini root bridge, penentuan cost itu berdasarkan:

- jumlah bandwidth di setiap interface
- berapa kali jalur yang dilewati untuk menuju sebuah interface(dalam case ini menuju Interface root bridge)

untuk menentukan cost dari  bandwidth kita memiliki perhitungan default seperti berikut:

- 10 Mb --> 100
- 100 Mb --> 19(ini merupakan coast default bila kita menggunakan Cisco Packet Tracer)
- 1 GB --> 4
- 10 GB --> 2

nah kembali lagi ke poin ke-2 mengenai "berapa kali jalur yang dilewati untuk menuju sebuah interface(dalam case ini menuju Interface root bridge)", kita dapat lihat pada Topology, misal pada interface fa0/2 SWA, bila Interface fa0/1 pada SWA dicabut  maka satu-satu nya jalan SWA untuk menuju SWC dan SWB adalah melalui Fa0/2 dengan perhitungan:

- menuju SWC --> 19 --> karena melewati 1 jalur saja
- menuju SwB --> 19 + 19 = 38 --> Karena melewati SWC dulu baru ke SWB 


# Manipulasi bandwidth

kalau kita ingin menguji apakah memang benar jalur itu dipilih berdasarkan Cost maka kita bisa melakukan manipulasi bandwidth untuk mengubah cost default sebuah interface kita bisa menjalankan command di global config:

- int <nama interface>
- speed <total bandwidth yang kita inginkan>, dalam hal ini karena kita menggunakan Switch 2960 di  CPT maka nilai bandiwdth yang bisa kita masukkan  adalah dari 10 atau 100

note : "yang bisa dimasukkan dalam paramter speed itu hanya nilai deskrit yang sudah pasti seperti 10,100,1000,dan 10000, kita tidak bisa memasukkan angka acak seperti 50, 49, 133, 279 dsb"


# Manipulasi Root Bridge

selain manipulais nilai pad bandwidth kita juga bisa melakukan manipulasi  pada root bridge dengan mengedit nilai dari priority nya, dengan command di global config:

- spanning-tree vlan <id vlan> priority <Value yang diinginkan>,

untuk value kita hanya bisa memilih nilai-nilai berikut:

Allowed values are:
  0     4096  8192  12288 16384 20480 24576 28672
  32768 36864 40960 45056 49152 53248 57344 61440

dan untuk mengatur root bridge kita bisa memilih value yang lebih kecil dari 32768 !


# Pengenalan Per VLAN Spanning Tree(PVSTP)

Sesuai namanya, PVSTP artinya setiap VLAN akan memiliki penjelasan mengenai STP mereka masing-masing, berbeda dengan sebelumnya dimana kita hanya memiliki vlan dfault(vlan 1), sehingga yang ditampilkan hanya informasi stp vlan 1 saja, untuk next pengujian kita akan menambah vlan baru yakni 

vlan 2, dan vlan 3 pada setiap switch, nah karena kita menambahkan vlan baru, maka di setiap interface pada masing-masing switch itu harus di setting mode trunk dulu dengan command "switchport mode trunk"

# Manipulasi Root Bridge PVSTP

Okeh sekarang kita akan melakukan manipulasi pada PVSTP, dimana pada vlan 2 yang sudah kita buat, akan kita setting agar melewati jalur cadangan, caranya cukup mudah, kita cukup membuat stp priority dari vlan 2 menjadi priority yang lebih kecil sehingga pada interface vlan 2 pada masing-masing switch akan mengikuti root bridge yang berbeda dari vlan 1, untuk lebih memahami silahkan lihat topologi yang digunakan ini : 'https://drive.google.com/open?id=1fFZ3qYvm34v-VC9UpI3U4cQouKUDoNAE&usp=drive_fs', disini saya akan melakukan perubahan priority vlan 2 pada SW B karena Alternate port untuk vlan 1 ada pada SW B, saya cukup jalankan: 

- "spanning-tree vlan 2 priority 20480" (cukup ini dan tunggu sampai prosesnya selesai), terakhir kita akan memasukkan interface fa0/4(SWA), dan Interface fa0/3(SWC) ke dalam vlan 2 dengan cara: 
SWA:
- int fa0/4
- switchport mode access
- switchport access vlan 2

SWB:
- int fa0/3
- switchport mode access
- switchport access vlan 2

selesai, dan dengan ini harusnya ketika PC Vlan 2 ingin berkomunikasi ke PC Vlan 2 di SW A, maka arp nya akan diarahkan menuju link cadangan pada konfigurasi stp vl 1 karena di vl 2 link cadangan tsbt sudah menjadi root port 


# STP Port States

Status port pada spanning tree ada 4

1. Blocking --> alternate port (waktu 20 s)
2. Listening --> memproses / mempelajari STP saat ini dan menentukan alternate port(waktu 15 s)
3. Learning --> maish sama yakni memproses / mempelajari STP saat ini dan menentukan alternate port namun sambil mempelajari mac-address nya juga(waktu 15 s)
4. Forward --> Link sudah bisa digunakan untuk mengirimkan data/packet

tambahan : 

1.portfast:

portfast adalah protokol yang digunakan dalam stp untuk menskip proses learning dan listening pada stp untuk interface yang mengarah ke single host/Client !, sehingga proses tersebut hanya terjadi anar interface switch saja, cara mengkonfigurasinya cukup mudah  yakni: 

- int <nama interface client / single host>
- spanning-tree portfast


2. spanning-tree root primary

spanning tree root primary adalah command yang bisa kita jalankan untuk mengubah priority dari sebuah interface vlan menjadi "24576" alias langsung mengubah sebuah interface vlan menjadi root bridge cara nya cukup simpel, kita hanya pelru menjalankan:

- spanning-tree vlan <id vlan> root primary

3. pada topologi 2 switch sebelumnya, sebagai pelengkap pengetahuan, dalam penentuan root port , bila priority dan mac-address nya sama, maka yang akan dibandingkan adalah besar angka dari interface nya, misal fa0/1 di SWB dan fa0/2 di SWB dengan root bridge nya adalah SWA, maka yang akan menjadi root port adalah fa0/1 karena angka interface nya paling kecil sehingga fa0/2 akan menjadi alternate port atau port yan diblokir smentara


Terimakasih telah menyimak!!