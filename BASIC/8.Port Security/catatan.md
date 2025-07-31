# Port security

Port security adalah suatu metode untuk mengamankan sebuah port/interface yang dalam hal ini adalah port/interface CISCO Switch, kita ambil permisalan kasus dimana dalam sebuah jaringan yang terdiri dari 3 server dan 1 PC yang terhubung ke dalam 1 Switch, lalu ada hacker yang mencabut salah satu kabel server dan menyambungkannya ke komputer miliknya untuk mengambil semua info yg ada pada switch, nah fungsi port security adlaah untuk melakukan blok/ menshutdown akses terhadap sebuah interface yang terhubung ke device yang tidak terdaftar sehingga device tersebut tidak bisa melakukan apapun.


CASE port Security

pada Case ini kita akan menggunakan 3 server 1 Switch, dan 1 PC serta 1 laptop yang mana satu laptop akan kita jadikan sebagai pengujian port security(anggapannya seperti Hacker)


nah kita akan melakukan port security untuk interface fa0/1 yang terhubung ke server 1 dengan menjalankan command :

- int fa0/1
- switchport mode access
- switchport port-security 
- switchport port-security mac-address <mac address Server(kalau di CPT bisa dilihat di menu 'config' fast-ethernet)>
- switchport port-security maximum <angka maksimum komputer yang dapat terhubung ke satu interface>  
- switchport port-security violation <jenis action yang kita inginkan>, contoh "switchport port-security violation shutdown"

note: "port security memiliki beberapa action untuk violation mulai dari 'restrict', 'protect', dan 'shutdown'"


next kita akan coba lakukan pengujian dengan memindahkan port server 1 ke Laptop, lalu melakukan ping ke salah satu server atau PC, berikut hasilnya : https://drive.google.com/open?id=1vEzzhrFuyJu2iStEDV3jyj35Kq5JBch5&usp=drive_fs

# MAC Address Sticky

Mac-address Sticky adalah salah satu cara yang digunakan untuk menghindari kesalahan manusia(human error), ketika mengkonfigurasi port security terutama dibagian memasukkan mac-address, cara kerja mac-address sticky adalah setelah dikonfigurasi dia akan lgsng mencatat mac-address pada device yang terhubung ketika device tersebut melakukan sebuah komunikasi, kalau belum melakukan komunikasi apa-apa maka mac-address sticky tidak akan mencatat apa-apa, cara mengkonfigurasi nya pada case kita adalah sebagai berikut:

note: kita akan mengkonfigurasi di Switch 2:


- int fa0/2
- switchport mode access
- switchport port-security 
- switchport port-security mac-address sticky
- switchport port-security maximum <angka maksimum komputer yang dapat terhubung ke satu interface>  
- switchport port-security violation <jenis action yang kita inginkan>, contoh "switchport port-security violation shutdown"

- do sh port-security int <nama interface yang dikonfig port-security>, contoh: do sh port-security int fa0/2

# Violation Protect
berbeda dengan shutdown, Violation Protect bekerja dengan memblokir packet-packet yang bukan dari komputer/erver yang terdaftar sambil menjaga interface nya tetap-up sehingga ketika sudah kembali ke device yang terdaftar maka device tersebut dapat langsung berkomunikasi secara normal, selain itu pada protect ini dia tidak akan menambah "Security Violation Count" yang mana ini merupakan hitungan percobaan mengenai berapa kali penyusup berusaha mengirimkan packet. cara konfig nya sama seperti sebelumnya yang membedakan adalah di bagian command violation nya saja yakni harus di command "switchport port-security violation protect"

# Violation Restrict
Violation Restrict bekerja dengan cara yang mirip dengan vioaltion protect yakni dengan memblokir packet-packet yang bukan dari komputer/erver yang terdaftar sambil menjaga interface nya tetap-up sehingga ketika sudah kembali ke device yang terdaftar maka device tersebut dapat langsung berkomunikasi secara normal, selain itu pada restrict ini dia  akan menambah "Security Violation Count" yang mana ini merupakan hitungan percobaan mengenai berapa kali penyusup berusaha mengirimkan packet.cara konfig nya sama seperti sebelumnya yang membedakan adalah di bagian command violation nya saja yakni harus di command "switchport port-security violation restrict"