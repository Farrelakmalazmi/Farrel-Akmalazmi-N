[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/99wpTe72)
| Name           | NRP        | Kelas     |
| ---            | ---        | ----------|
| Daru Fadhilah Nahdi | 5025221282 | Jaringan Komputer (B) |


## Find your topology here!

- Link: https://drive.google.com/drive/folders/1ECQD6-cQkg0DzyflG-jSxJZaGaxg0KSU?usp=sharing

- Topology distribution for groups: https://docs.google.com/spreadsheets/d/1QKEZjixTStNbdXznOalJoJS0UQ6ed23o51pP8t8eAIM/edit?gid=1757558734#gid=1757558734

## Put your topology config image here!

![Topologi main](Screenshot/Topologi.png)

*Note : Prefix IP adalah 192.205*

<br>

## Soal 1

> Topologi terdiri dari node Wortel yang berupa DNS Master*. Selain itu, terdapat pula node Pokcoy sebagai DNS Slave*, yang bertugas sebagai cadangan dari node Wortel.
> <br> </br>
> Selanjutnya terdapat node Tomat dan Taoge yang bekerja sebagai Client*, tiga buah Web Server* yaitu Bayam, Buncis, dan Brokoli, serta Mayur sebagai Router*. Buatlah topologi sesuai dengan pembagian topologi [di sini](https://docs.google.com/spreadsheets/d/1QKEZjixTStNbdXznOalJoJS0UQ6ed23o51pP8t8eAIM/edit?usp=sharing) dan konfigurasi topologi [di sini](https://drive.google.com/drive/folders/1ECQD6-cQkg0DzyflG-jSxJZaGaxg0KSU?usp=sharing). Pastikan bahwa setiap node dapat terhubung ke Internet.

> _The topology consists of a Wortel node which is a DNS Master*. In addition, there is also a Pokcoy node as a DNS Slave*, which serves as a backup for the Wortel node._
> <br> </br>
> _Furthermore, there are Tomat and Taoge nodes that work as Client*, three Web Servers*, namely Bayam, Buncis, and Brokoli, then finally Mayur as Router*. Make a topology according to the topology division [here](https://docs.google.com/spreadsheets/d/1QKEZjixTStNbdXznOalJoJS0UQ6ed23o51pP8t8eAIM/edit?usp=sharing) and the topology configuration [here](https://drive.google.com/drive/folders/1ECQD6-cQkg0DzyflG-jSxJZaGaxg0KSU?usp=sharing). Make sure that each node can connect to the Internet._

**Answer:**

- Screenshot

  ![dns's inet](Screenshot/1a.png)
  ![client inet](Screenshot/1b.png)
  ![webserver inet](Screenshot/1c.png)

- Explanation

  Hal ini adalah setup pertama agar setiap node bisa mengakses internet. Cara Agar hal ini berhasil adalah pertama untuk setup routernya (Node Mayur). Untuk melakukannya kita akan run `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.205.0.0/16`. Dari situ kita bisa melihat IP DNS di /etc/resolv.conf yang merupakan 192.168.122.1. Selanjutnya adalah konfigurasi network tiap node. Untuk router confignya adalah sebagai berikut.

  ```
  auto eth0
  iface eth0 inet dhcp

  auto eth1
  iface eth1 inet static
  	address 192.205.1.1
  	netmask 255.255.255.0

  auto eth2
  iface eth2 inet static
  	address 192.205.2.1
  	netmask 255.255.255.0
  ```

  Selanjutnya untuk node lainnya bisa di konfigurasi sebagai berikut:

  ```
  auto eth0
  iface eth0 inet static
  	address [IP Address]
  	netmask 255.255.255.0
  	gateway [Gateway Connection]
  ```

  Konfigurasi IP dan gateway sesuai yang diperlukan. gateway berdasarkan tersambung ke eth mana dari router dan ip address di konfigurasi sendiri. Berikut adalah list IP Address dari tiap node:

  ```
  eth1:
  IP WortelDNSMaster  : 192.205.1.2 
  IP PokcoyDNSSlave   : 192.205.1.3 
  IP TaugeClient      : 192.205.1.4 

  eth2:
  IP TomatClient      : 192.205.2.2 
  IP BuncisWebServer  : 192.205.2.3 
  IP BrokoliWebServer : 192.205.2.4 
  IP BayamWebServer   : 192.205.2.5

  Contoh Konfigurasi network:
  // WortelDNSMaster
  auto eth0
  iface eth0 inet static
    address 192.205.1.2
    netmask 255.255.255.0
    gateway 192.205.1.1
  ```

  gateway eth1 akan connect ke 192.205.1.1 dan eth2 akan ke 192.205.2.1. Selanjutnya pada masing-masing node kita akan tambahkan `nameserver 192.168.122.1` pada file /etc/resolv.conf.

<br>

## Soal 2

> Tambahkan konfigurasi untuk domain bayam.yyy.com yang mengarah ke IP node Bayam di DNS Master. Dengan cara yang sama, buat konfigurasi domain brokoli.yyy.com yang mengarah ke IP node Brokoli dan domain buncis.yyy.com yang mengarah ke IP node Buncis. Simpan semua konfigurasi dalam folder Jarkom. Selama pengerjaan soal, ubah yyy menjadi kode kelompok masing-masing (contoh: A02).
> <br> </br>
> Jangan lupa update konfigurasi kedua client agar dapat berkomunikasi dengan semua domain tersebut.


> _Add a configuration for bayam.yyy.com domain that points to the Bayam node IP in the DNS Master. In the same way, create a brokoli.yyy.com domain configuration that points to the Brokoli node IP and a buncis.yyy.com domain that points to the Buncis node IP. Save all configurations in a folder called Jarkom. For this practicum, substitute yyy with the code of each group (ex: A02).
> <br> </br> 
> Don't forget to update the configuration of both clients so that they can communicate with the domains._

**Answer:**

- Screenshot

  ![Hasil ping domain](Screenshot/2a.png)

- Explanation

  Untuk Melakukan ini kita akan membuat DNS Record di wortel sesuai jumlah domain yang akan kita buat. Caranya adalah kita pertama akan buat sebuah folder bernama jarkom di direktori /etc/bind. Direktori ini akan menyimpan semua dns record kita. Lalu untuk membuat dns record nya kita butuh kerangkanya, maka kita akan copy dengan `cp /etc/bind/db.local /etc/bind/jarkom/bayam.B24.com`. Lakukan ini 2 kali lagi untuk buncis dan brokoli. Lalu masing-masing DNS record akan terisi seperti ini:
  
  - bayam.B24.com
  ```
  ;
  ; BIND data file for local loopback interface
  ;
  $TTL    604800
  @       IN      SOA     bayam.B24.com. root.bayam.B24.com. (
                      2024100201         ; Serial
                          604800         ; Refresh
                           86400         ; Retry
                         2419200         ; Expire
                          604800 )       ; Negative Cache TTL
  ;
  @       IN      NS      bayam.B24.com.
  @       IN      A       192.205.2.5     ;IP BayamWebServer
  @       IN      AAAA    ::1
  ```
  - buncis.B24.com
  ```
  ;
  ; BIND data file for local loopback interface
  ;
  $TTL    604800
  @       IN      SOA     buncis.B24.com. root.buncis.B24.com. (
                      2024100301         ; Serial
                          604800         ; Refresh
                            86400         ; Retry
                          2419200         ; Expire
                          604800 )       ; Negative Cache TTL
  ;
  @       IN      NS      buncis.B24.com.
  @       IN      A       192.205.2.3
  @       IN      AAAA    ::1
  ```
  - brokoli.B24.com
  ```
  ;
  ; BIND data file for local loopback interface
  ;
  $TTL    604800
  @       IN      SOA     brokoli.B24.com. root.brokoli.B24.com. (
                      2024100201         ; Serial
                          604800         ; Refresh
                            86400         ; Retry
                          2419200         ; Expire
                          604800 )       ; Negative Cache TTL
  ;
  @       IN      NS      brokoli.B24.com.
  @       IN      A       192.205.2.4     ;IP BrokoliWebServer
  @       IN      AAAA    ::1
  ```

  Pada tiap record tersebut, record ns-nya kita ganti menjadi domain yang kita inginkan dan record A nya kita tunjuk ke IP sesuai masing-masing dan record SOA kita sesuaikan dengan domainnya juga. Setelah itu kita akan edit /etc/bind/named.conf/local untuk konfigurasi ketiga domain tersebut. Isi dari file tersebut adalah sebagai berikut:
  ```
  zone "bayam.B24.com" {
        type master;
        file "/etc/bind/jarkom/bayam.B24.com";
  };

  zone "brokoli.B24.com" {
          type master;
          file "/etc/bind/jarkom/brokoli.B24.com";
  };

  zone "buncis.B24.com" {
          type master;
          file "/etc/bind/jarkom/buncis.B24.com";
  };
  ```

  Bikin 3 zona sesuai yang diingingkan dan arahkan filenya ke directory masing-masing. Lalu untuk cek apakah pembuatan domainnya berhasil kita akan menambahkan ip worteldnsmaster ke nameserver client. Jadi tambahkan `nameserver 192.205.1.2` ke dalam /etc/resolv.conf.


<br>

## Soal 3

> Tambahkan domain alias berupa www.bayam.yyy.com pada alamat bayam.yyy.com dan www.brokoli.yyy.com pada alamat brokoli.yyy.com.

> _Add a domain alias in the form of www.bayam.yyy.com to the bayam.yyy.com address and www.brokoli.yyy.com to the brokoli.yyy.com address._

**Answer:**

- Screenshot

  ![ping www domain](Screenshot/3a.png)

- Explanation

  Untuk menambahkan fitur alias tersebut caranya adalah dengan mengedit dns record kedua domain tersebut. Lalu kita akan tambahkan record CNAME dengan www agar bisa alias. Berikut isi kedua domain dns record file

  - bayam
  ```
  ;
  ; BIND data file for local loopback interface
  ;
  $TTL    43200
  @       IN      SOA     bayam.B24.com. root.bayam.B24.com. (
                      2024100201         ; Serial
                          604800         ; Refresh
                           86400         ; Retry
                         2419200         ; Expire
                          604800 )       ; Negative Cache TTL
  ;
  @       IN      NS      bayam.B24.com.
  @       IN      A       192.205.2.5     ;IP BayamWebServer
  www     IN      CNAME   bayam.B24.com.
  @       IN      AAAA    ::1
  ```

  - brokoli
  ```
  ;
  ; BIND data file for local loopback interface
  ;
  $TTL    604800
  @       IN      SOA     buncis.B24.com. root.buncis.B24.com. (
                      2024100301         ; Serial
                          604800         ; Refresh
                            86400         ; Retry
                          2419200         ; Expire
                          604800 )       ; Negative Cache TTL
  ;
  @       IN      NS      buncis.B24.com.
  @       IN      A       192.205.2.3
  www     IN      CNAME   buncis.B24.com.
  @       IN      AAAA    ::1
  ```

<br>

## Soal 4

> Tambahkan record reverse domain untuk domain brokoli.yyy.com dan buncis.yyy.com.

> _Add a reverse domain record for brokoli.yyy.com and buncis.yyy.com domains._

**Answer:**

- Screenshot

  ![cek benar](Screenshot/4a.png)

- Explanation

  Untuk melakukan hal ini kita akan membuat dns record dan modif named.conf.local. Buat DNS zone file baru di folder jarkom dengan nama 2.205.192.in-addr.arpa. Karena 3 byte pertama ip address brokoli dan buncis sama reverse domain akan di konfigurasi di file yang sama. Isi dari 2.205.192.in-addr.arpa adalah sebagai berikut:
  ```
  ;
  ; BIND data file for local loopback interface
  ;
  $TTL    604800
  @       IN      SOA     ns.B24.com. root.B24.com. (
                      2024100201         ; Serial
                          604800         ; Refresh
                            86400         ; Retry
                          2419200         ; Expire
                          604800 )       ; Negative Cache TTL
  ;
  2.205.192.in-addr.arpa. IN      NS      ns.B24.com.
  4                       IN      PTR     brokoli.B24.com.  
  3                       IN      PTR     buncis.B24.com.   
  ```

  Disini ada 2 record PTR untuk kedua ip. 4 dan 3 adalah byte terakhir dari IP masing masing webserver. Setelah itu modif named.conf.local agar ada zona ini.

  - Tambahan named.conf.local
  ```
  zone "2.205.192.in-addr.arpa" {
      type master;
      file "/etc/bind/jarkom/2.205.192.in-addr.arpa";
  };
  ```

<br>

## Soal 5

> Ubah record SOA dari domain bayam.yyy.com sesuai dengan ketentuan berikut:
> - Lama waktu server slave menunggu untuk mengecek salinan baru server master adalah sebesar 2 jam.
> - Field yang mengatur revisi file zona ini diubah menjadi tanggal awal praktikum (format YYYYMMDD) kemudian diikuti dengan nomor kelompok (contoh untuk kelompok A02 maka nomornya 02).
> - Lamanya waktu server harus menunggu untuk meminta pembaruan lagi dari nameserver master yang tidak responsif sebesar 30 menit.
> - Lama waktu nama domain di-cache secara lokal sebelum kadaluarsa dan kembali ke nameserver otoritatif untuk informasi terbaru sebesar 12 jam.
> - Jika server slave tidak mendapatkan respons dari server master dalam waktu 2 minggu, server tersebut harus berhenti merespons kueri untuk zona tersebut.

> _Change the SOA record of the bayam.yyy.com domain according to the following conditions:_
> - The length of time the slave server waits to check for a new revision of the master server is 2 hours.
> - The field that regulates the revision of this zone file is changed to the start date of the practicum (YYYYMMDD format) then followed by the group number (ex: for A02 the group number would be 02).
> - The length of time the server has to wait to request another update from an unresponsive master nameserver is 30 minutes.
> - The length of time a domain name is cached locally before it expires and returns to an authoritative nameserver for up-to-date information is 12 hours.
> - If the slave server does not get a response from the master server within 2 weeks, it must stop responding to queries for that zone.

**Answer:**

- Screenshot

  ![bayam updated](Screenshot/5a.png)

- Explanation

  Untuk melakukan hal ini kita akan modif record zone dns dari bayam. Record yang harus kita atur adalah SOA. Pertama-tama SOA ini menerima second maka harus kita convert dari soal menjadi second.

  - *Lama waktu server slave menunggu untuk mengecek salinan baru server master adalah sebesar 2 jam.* > 2 Jam = 7200 second, diubah yang Refresh
  - *Field yang mengatur revisi file zona ini diubah menjadi tanggal awal praktikum (format YYYYMMDD) kemudian diikuti dengan nomor kelompok.* > Ubah Serial Number sesuainya
  - *Lamanya waktu server harus menunggu untuk meminta pembaruan lagi dari nameserver master yang tidak responsif sebesar 30 menit.* > 30 menit = 1800 second, uvah yang retry
  - *Lama waktu nama domain di-cache secara lokal sebelum kadaluarsa dan kembali ke nameserver otoritatif untuk informasi terbaru sebesar 12 jam.* > 12 Jam = 43200 second ganti ttl yang diatas SOA
  - *Jika server slave tidak mendapatkan respons dari server master dalam waktu 2 minggu, server tersebut harus berhenti merespons kueri untuk zona tersebut.* 2 minggu = 1209600 second, ganti Expire
  
<br>

## Soal 6

> Untuk menangani request yang berlebih dari client ke ketiga alamat yang tadi dibuat, konfigurasikan node Pokcoy sebagai DNS Slave yang bekerja untuk DNS Master Wortel.

> _To handle excess requests from the client to the three addresses created, configure the Pokcoy node as the DNS Slave that works for Wortel DNS Master._

**Answer:**

- Screenshot

  Untuk test pastikan master sedang mati

  ![alt text](Screenshot/6a.png)
  ![alt text](Screenshot/6b.png)

- Explanation

  Untuk Melakukan hal ini kita harus memodifikasi named.conf.local agar ketiga zona tersebut agar juga notifikasi dan allow transfer ke pokcoydnsslave (192.205.1.3). Berikut adalah isi dari named.conf.local:

  - named.conf.local (WortelDNSMaster)
  ```
  //
  // Do any local configuration here
  //

  // Consider adding the 1918 zones here, if they are not used in your
  // organization
  //include "/etc/bind/zones.rfc1918";

  zone "bayam.B24.com" {
          type master;
          notify yes;
          also-notify { 192.205.1.3; };
          allow-transfer { 192.205.1.3; };
          file "/etc/bind/jarkom/bayam.B24.com";
  };

  zone "brokoli.B24.com" {
          type master;
          notify yes;
          also-notify { 192.205.1.3; };
          allow-transfer { 192.205.1.3; };
          file "/etc/bind/jarkom/brokoli.B24.com";
  };

  zone "buncis.B24.com" {
          type master;
          notify yes;
          also-notify { 192.205.1.3; }; 
          allow-transfer { 192.205.1.3; }; 
          file "/etc/bind/jarkom/buncis.B24.com";
  };

  zone "2.205.192.in-addr.arpa" {
      type master;
      file "/etc/bind/jarkom/2.205.192.in-addr.arpa";
  };
  ```

  Selanjutnya kita akan konfigurasi /etc/bind/named.conf.local yang ada di PokcoyDNSSlave. Kita akan menambahkan ketiga zona dengan tipe mereka "slave" yang menunjuk master nya ke IP WortelDNSMaster.

  - named.conf.local (PokcoyDNSSlave)
  ```
  //
  // Do any local configuration here
  //

  // Consider adding the 1918 zones here, if they are not used in your
  // organization
  //include "/etc/bind/zones.rfc1918";

  zone "buncis.B24.com" {
      type slave;
      masters { 192.205.1.2; };
      file "/var/lib/bind/buncis.B24.com";
  };

  zone "brokoli.B24.com" {
      type slave;
      masters { 192.205.1.2; };
      file "/var/lib/bind/brokoli.B24.com";
  };

  zone "bayam.B24.com" {
      type slave;
      masters { 192.205.1.2; };
      file "/var/lib/bind/bayam.B24.com";
  };
  ```

  Selanjutnya kita harus menambahkan nameserver dengan IP PokcoyDNSSlave kedalam client di resolv.conf.

<br>

## Soal 7

> Karena membutuhkan tempat untuk menyimpan resep brokoli, buatlah subdomain berupa vitamin.brokoli.yyy.com dengan alias www.vitamin.brokoli.yyy.com dengan mendelegasikannya dari Wortel ke Pokcoy dengan alamat IP menuju Brokoli yang diatur di folder Vitamin.

> _Since we need a place to store Brokoli recipes, create a subdomain in the form of vitamin.brokoli.yyy.com with an alias of www.vitamin.brokoli.yyy.com by delegating it from Wortel to Pokcoy with an ip to the Brokoli node in a folder called Vitamin._

**Answer:**

- Screenshot

  ![alt text](Screenshot/7a.png)

- Explanation

  Untuk melakukan hal ini, kita pertama-tama harus mendelegasikan domain brokolinya terlebih dahulu. Kita akan mendelegasikan ke nameserver PokcoyDNSSlave (192.205.1.3). Berarti harus modif record brokoli

  - brokoli.B24.com
  ```
  ;
  ; BIND data file for local loopback interface
  ;
  $TTL    604800
  @       IN      SOA     brokoli.B24.com. root.brokoli.B24.com. (
                      2024100201         ; Serial
                          604800         ; Refresh
                            86400         ; Retry
                          2419200         ; Expire
                          604800 )       ; Negative Cache TTL
  ;
  @       IN      NS      brokoli.B24.com.
  @       IN      A       192.205.2.4     ;IP BrokoliWebServer
  www     IN      CNAME   brokoli.B24.com.
  ns1     IN      A       192.205.1.3
  vitamin IN      NS      ns1
  @       IN      AAAA    ::1
  ```

  Selanjutnya pada kedua server dns kita akan modif config option `/etc/bind/named.conf.options` dan memberinya `allow-query{any;};` dan kita akan command `dnssec-validation auto`. Lalu selanjutnya adalah konfigurasi di PokcoyDNSSlave. Pada named.conf.local kita akan tambahkan zona untuk vitamin.brokoli.B24.com.

  - Tambahan vitamin.brokoli.B24.com
  ```
  zone "vitamin.brokoli.B24.com"{
          type master;
          file "/etc/bind/Vitamin/vitamin.brokoli.B24.com";
  };
  ```
  Setelah itu kita akan buat record DNS zonanya di "/etc/bind/Vitamin" dengan nama vitamin.brokoli.B24.com.

  - vitamin.brokoli.B24.com
  ```
  ;
  ; BIND data file for local loopback interface
  ;
  $TTL    604800
  @       IN      SOA     vitamin.brokoli.B24.com. root.vitamin.brokoli.B24.com. (
                      2024100301         ; Serial
                          604800         ; Refresh
                            86400         ; Retry
                          2419200         ; Expire
                          604800 )       ; Negative Cache TTL
  ;
  @       IN      NS      vitamin.brokoli.B24.com.
  @       IN      A       192.205.2.4
  www     IN      CNAME   vitamin.brokoli.B24.com.
  @       IN      AAAA    ::1
  ```

<br>

## Soal 8

> Buatlah subdomain khusus untuk kandungan brokoli dengan akses k1.vitamin.brokoli.yyy.com dengan alias www.k1.vitamin.brokoli.yyy.com yang mengarah ke IP brokoli dan diatur di folder k1.  

> _Create a special subdomain for Brokoli content called k1.vitamin.brokoli.yyy.com with an alias called www.k1.vitamin.brokoli.yyy.com that point to Brokoli node and are organized in a folder called k1._

**Answer:**

- Screenshot

  ![alt text](Screenshot/8a.png)

- Explanation

  Untuk melakukan hal ini kita pertama harus mendelegasikan k1. Jadi kita akan modif zona vitamin.brokoli.B24.com.

  - vitamin.brokoli.B24.com
  ```
  ;
  ; BIND data file for local loopback interface
  ;
  $TTL    604800
  @       IN      SOA     vitamin.brokoli.B24.com. root.vitamin.brokoli.B24.com. (
                      2024100301         ; Serial
                          604800         ; Refresh
                            86400         ; Retry
                          2419200         ; Expire
                          604800 )       ; Negative Cache TTL
  ;
  @       IN      NS      vitamin.brokoli.B24.com.
  @       IN      A       192.205.2.4
  www     IN      CNAME   vitamin.brokoli.B24.com.
  ns1     IN      A       192.205.1.3
  k1      IN      NS      ns1
  @       IN      AAAA    ::1
  ```

  Selanjutnya kita akan buat zona dns record di folder k1 dengan nama k1.vitamin.brokoli.B24.com yang tentunya akan menunjuk ke IP brokoli.

  - k1.vitamin.brokoli.B24.com
  ```
  ;
  ; BIND data file for local loopback interface
  ;
  $TTL    604800
  @       IN      SOA     k1.vitamin.brokoli.B24.com. root.k1.vitamin.brokoli.B24.com. (
                      2024100301         ; Serial
                          604800         ; Refresh
                            86400         ; Retry
                          2419200         ; Expire
                          604800 )       ; Negative Cache TTL
  ;
  @       IN      NS      k1.vitamin.brokoli.B24.com.
  @       IN      A       192.205.2.4
  www     IN      CNAME   k1.vitamin.brokoli.B24.com.
  @       IN      AAAA    ::1
  ```

<br>

## Soal 9

> Bayam, Brokoli, dan Buncis masing-masing berfungsi sebagai web server nginx yang menyajikan resep khusus untuk jenis sayuran yang mereka tangani. Untuk mengaktifkan web server pada masing-masing worker, lakukan deployment website menggunakan sumber yang tersedia di sayur_webserver_nginx. Tambahkan konfigurasi untuk log error ke file /var/log/nginx/error.log dan log access ke file /var/log/nginx/access.log.

> _Bayam, Brokoli, and Buncis each function as nginx web servers that serve special recipes for the type of vegetables they handle. To activate the web server on each worker, do the deployment using the resources available in sayur_webserver_nginx. Add configuration for error log to the file /var/log/nginx/error.log and access log to the file /var/log/nginx/access.log._

**Answer:**

- Screenshot

  ```
  // Access The servers
  lynx 192.205.2.3
  lynx 192.205.2.4
  lynx 192.205.2.5
  ```

  ![alt text](Screenshot/9a.png)

- Explanation

  Untuk melakukan hal ini kita harus setup nginx kita. Jadi kita harus install nginx php dan php-fpm. Lalu pertama-tama kita akan mendownload sayur_webserver_nginx menggunakan wget di direktori /var/www. Setelah itu kita bisa unzip isinya. Lalu kita akan modifikasi server block nginx sayur ini dengan buat dan edit sayur_webserver_nginx di direktori /etc/nginx/sites-available.

  - sayur_webserver_nginx
  ```
  server {
      listen 80;
      root /var/www/sayur_webserver_nginx;
      index index.php index.html index.htm;
      server_name _;

      location / {
          try_files $uri $uri/ /index.php?$query_string;
      }

      location ~ \.php$ {
          include snippets/fastcgi-php.conf;
          fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
      }

      location ~ /\.ht {
          deny all;
      }

      error_log /var/log/nginx/error.log;
      access_log /var/log/nginx/access.log;
  }
  ```

  Disini kita listen untuk port 80. Lalu untuk dokumen rootnya kita tunjuk ke direktori hasil unzip tadi yang mengandung file php yang bersangkutan. Untuk modif konfigurasi lognya tinggal edit `error_log dan access_log`. Selanjutnya kita akan link file konfigurasi ini ke /etc/nginx/sites-enabled dengan cara `ln -s /etc/nginx/sites-available/sayur_webserver_nginx /etc/nginx/sites-enabled`. Setelah itu kita akan unlink konfigurasi default agar saat node diakses dengan lynx maka akan membuka yang sayur_webserver_nginx.

<br>

## Soal 10

> Pada masing masing worker nginx, akan terdapat beberapa hal yang perlu diperbaiki pada resource yang diberikan untuk bisa menampilkan resep saat halaman dimuat. Analisis kesalahan yang ada di resource melalui file /var/log/nginx/error.log dan perbaiki hingga halaman bisa menampilkan resep sesuai dengan worker nya.

> _On each nginx worker, there will be several things that need to be fixed in the resources provided to be able to display recipes when the page is loaded. Analyze the errors in the resource through the /var/log/nginx/error.log file and fix it until the page can display recipes according to its worker._

**Answer:**

- Screenshot

  ![alt text](Screenshot/10a.png)
  ![alt text](Screenshot/10b.png)

- Explanation

  Log menunjukan bahwa hostname tidak ditemukan. Oleh karena itu masalah utama ada di index.php yang menggunakan swtch case berdasarkan nama hostname. Masalahnya nama-nama mereka berbeda seperti sistem nama yang digunakan di topologi. Jadi untuk membenarkannya kita akan menyesuaikan switch case nya dengan nama hostname yang benar. Setelah itu muncul error tidak ada nama file resep_x.php. Hal ini dikarenakan nama file yang benar adalah resepx.php tanpa underline. Jadi tinggal kita hapus underlinenya di index.php. Berikut adalah index.php yang sudah dibenarkan.

  - index.php
  ```php
  <?php
  $hostname = gethostname();
  $date = date('l, d F Y H:i:s');
  $php_version = phpversion();
  $visitor_ip = $_SERVER['REMOTE_ADDR'];

  echo "<h1>Selamat Datang di Situs Resep Sayuran!</h1>";
  echo "<p>Pengunjung dari IP: <strong>$visitor_ip</strong></p>";
  echo "<p>Versi PHP: <strong>$php_version</strong></p>";
  echo "<p>Waktu: <strong>$date</strong></p>";
  echo "<hr>";
  echo "<p>Saat ini Anda sedang mengakses resep dari sayur: <strong>$hostname</strong></p>";

  switch ($hostname) {
      case 'BayamWebServer':
          if (!@include 'resep1.php') {
              echo "<p>Gagal Memuat Resep! Cek Error Log untuk info lebih lanjut.</p>";
              error_log("File resep1.php tidak ditemukan!\n", 3, "/var/log/nginx/error.log");
          }
          break;
      case 'BuncisWebServer':
          if (!@include 'resep2.php') {
              echo "<p>Gagal Memuat Resep! Cek Error Log untuk info lebih lanjut.</p>";
              error_log("File resep2.php tidak ditemukan!\n", 3, "/var/log/nginx/error.log");
          }
          break;
      case 'BrokoliWebServer':
          if (!@include 'resep3.php') {
              echo "<p>Gagal Memuat Resep! Cek Error Log untuk info lebih lanjut.</p>";
              error_log("File resep3.php tidak ditemukan!\n", 3, "/var/log/nginx/error.log");
          }
          break;
      default:
          echo "<p>Gagal Memuat Resep! Cek Error Log untuk info lebih lanjut.</p>";
          error_log("Hostname tidak ditemukan!\n", 3, "/var/log/nginx/error.log");
          break;
  }
  ?>
  ```

<br>

## Soal 11

> Setelah website berhasil dideploy pada masing-masing worker (Bayam, Brokoli, dan Buncis) dan halaman dapat menampilkan resep sayuran yang sesuai,  buatlah custom access log ke file /var/log/nginx/access.log di masing-masing web server worker menggunakan format log tertentu seperti di bawah:
> - Tanggal dan waktu akses dalam format standar log.
Nama worker yang sedang dilayani (misalnya: Bayam, Brokoli, atau Buncis).
> - Alamat IP klien yang mengakses website.
> - Metode HTTP dan URI yang diakses oleh klien.
> - Status respons HTTP yang diberikan oleh server.
> - Jumlah byte yang dikirimkan dalam respons.
> - Waktu yang dihabiskan oleh server untuk menangani permintaan.
> <br> </br>
> Contoh format log yang sesuai: 
[01/Oct/2024:11:30:45 +0000] Jarkom Node Bayam Access from 192.168.1.15 using method "GET /resep/bayam HTTP/1.1" returned stat


> _After successfully deploying the website on each worker (Bayam, Brokoli, and Buncis) and ensuring the pages display the appropriate vegetable recipes, create a custom access log file at /var/log/nginx/access.log on each web server worker using a specific log format as described below:_
> - _Access date and time in standard log format._
> - _Name of the worker serving the request (e.g., Bayam, Brokoli, or Buncis)._
> - _Client IP address accessing the website._
> - _HTTP method and URI accessed by the client._
> - _HTTP response status provided by the server.__
> - _Number of bytes sent in the response.
> - _Time taken by the server to handle the request._
> <br> </br>
> _Example of the appropriate log format:
[01/Oct/2024:11:30:45 +0000] Jarkom Node Bayam Access from 192.168.1.15 using method "GET /resep/bayam HTTP/1.1" returned status 200 with 2567 bytes sent in 0.038 seconds_


**Answer:**

- Screenshot

  ![alt text](Screenshot/11a.png)
  ![alt text](Screenshot/11b.png)
  ![alt text](Screenshot/11c.png)

- Explanation

  Untuk melakukan hal ini kita harus ubah konfigurasi dari etc/nginx/nginx.conf dan konfigurasi server block sayur_webserver_nginx. Pertama kita akan buat variabel `custom_format` sebagai format message log yang diinginkan di dalam nginx.conf.

  - line tambahan nginx.conf
  ```
  log_format custom_format '[$time_local] Access from Jarkom Node Bayam $remote_addr using method "$request" returned status $status with $body_bytes_sent bytes sent in $request_time seconds.
  ```

  Tentunya untuk nama nodenya disesuaikan sesuai webserver. Setelah itu jika ingin sayur_webserver_nginx untuk memakai format custom ini, kita harus ubah dikit konfigurasi block server.

  - sayur_webserver_nginx
  ```
  access_log /var/log/nginx/access.log custom_format;
  ```

<br>

## Soal 12

> Informasi vitamin pada sayur brokoli akan ditampilkan pada subdomain vitamin.brokoli.yyy.com di node brokoli, buatlah DocumentRoot yang disimpan pada /var/www/vitamin.brokoli.yyy. Konfigurasikan webserver dengan nama server vitamin.brokoli.yyy.com dan server alias www.vitamin.brokoli.yyy.com. Lakukan konfigurasi Apache Web Server pada Brokoli dengan menggunakan sumber yang tersedia di [sini](https://docs.google.com/uc?export=download&id=1QbGkKXo3jt4c68AdVAkl1hD4LolTUPg2).

> _For information on vitamins in brokoli will be displayed on the vitamin.brokoli.yyy.com subdomain on the brokoli node, create a DocumentRoot stored in /var/www/vitamin.brokoli.yyy. Configure the web server with the server name vitamin.brokoli.yyy.com and server alias www.vitamin.brokoli.yyy.com. Configure the Apache Web Server on Brokoli using [this resource](https://docs.google.com/uc?export=download&id=1QbGkKXo3jt4c68AdVAkl1hD4LolTUPg2)._

**Answer:**

- Screenshot

  ![alt text](Screenshot/12a.png)
  ![Berhasil diakses www dan biasa](Screenshot/12b.png)

- Explanation

  Untuk melakuakan ini kita membutuhkan apache2 karena itu yang akan dipakai. Pertama-tama kita akan download zip tersebut dan unzip di /var/www dan berinama sesuai yang diminta. Setelah itu kita akan membuat konfigurasi website yang berada di /etc/apache2/sites-available. Pertama kita akan buat filenya dengan cara copy kerangka `cp 000-default.conf vitamin.brokoli.B24.conf`. Lalu di file itu kita akan konfigurasi websitenya. Pertama kita ubah DocumentRoot-nya agar menunjuk ke folder yang telah kita unzip sebelumnya. Setelah itu kita akan set ServerName menjadi vitamin.brokoli.B24.com dan ServerAlias menjadi www.vitamin.brokoli.B24.com. Setelah itu kita akan enable virtual hostnya dengan command `a2ensite vitamin.brokoli.B24.conf`

  - vitamin.brokoli.B24.conf

  ```
  <VirtualHost *:80>
          # The ServerName directive sets the request scheme, hostname and port that
          # the server uses to identify itself. This is used when creating
          # redirection URLs. In the context of virtual hosts, the ServerName
          # specifies what hostname must appear in the request's Host: header to
          # match this virtual host. For the default virtual host (this file) this
          # value is not decisive as it is used as a last resort host regardless.
          # However, you must set it for any further virtual host explicitly.
          #ServerName www.example.com

          ServerAdmin webmaster@localhost
          DocumentRoot /var/www/vitamin.brokoli.B24
          ServerName vitamin.brokoli.B24.com
          ServerAlias www.vitamin.brokoli.B24.com

          # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
          # error, crit, alert, emerg.
          # It is also possible to configure the loglevel for particular
          # modules, e.g.
          #LogLevel info ssl:warn

          ErrorLog ${APACHE_LOG_DIR}/error.log
          CustomLog ${APACHE_LOG_DIR}/access.log combined

          # For most configuration files from conf-available/, which are
          # enabled or disabled at a global level, it is possible to
          # include a line for only one particular virtual host. For example the
          # following line enables the CGI configuration for this host only
          # after it has been globally disabled with "a2disconf".
          #Include conf-available/serve-cgi-bin.conf
  </VirtualHost>
  ```

<br>

## Soal 13

> Pada subdomain vitamin.brokoli.yyy.com, terdapat subfolder /nutrisi yang menyediakan informasi tentang berbagai vitamin dalam brokoli, seperti Vitamin A, C, dan K. Aktifkan directory listing untuk folder /nutrisi, dan buatlah rewrite rule di Apache untuk memperbaiki URL agar halaman seperti www.vitamin.brokoli.yyy.com/nutrisi/vitamin_a.php dapat diakses hanya dengan www.vitamin.brokoli.yyy.com/nutrisi/vitamin_a. Pastikan setiap halaman vitamin dapat diakses langsung melalui url yang telah disederhanakan.

> _On the vitamin.brokoli.yyy.com subdomain, there is a /nutrisi subfolder that provides information about various vitamins in brokoli, such as Vitamin A, C, and K. Activate directory listing for the /nutrisi folder, and create a rewrite rule in Apache to fix the URL so that pages like www.vitamin.brokoli.yyy.com/nutrisi/vitamin_a.php can be accessed only with www.vitamin.brokoli.yyy.com/nutrisi/vitamin_a. Make sure each vitamin page can be accessed directly through the simplified url._

**Answer:**

- Screenshot

  ![alt text](Screenshot/13a.png)
  ![alt text](Screenshot/13b.png)

- Explanation

  Untuk rewrite rule pertama kita harus menambahkan config dengan .htaccess. Kita akan buat file tersebut didalam /var/www/vitamin.brokoli.B24. Lalu disitu kita akan membuat rewrite rulenya.

  - .htaccess
  ```
  RewriteEngine On
  RewriteCond %{REQUEST_FILENAME} !-d
  RewriteRule ^([^\.]+)$ $1.php [NC,L]
  ``` 

  Setelah itu kita harus menambahkan beberapa hal di konfigurasi websitenya untuk direktori website. Untuk mengaktifkan direkctory listing kita juga harus mengubah config file website dan directory nutrisi akan diberi option `+Indexes` agar bisa generate listing. 

  - vitamin.brokoli.B24.conf

  ```
  <VirtualHost *:80>
          # The ServerName directive sets the request scheme, hostname and port that
          # the server uses to identify itself. This is used when creating
          # redirection URLs. In the context of virtual hosts, the ServerName
          # specifies what hostname must appear in the request's Host: header to
          # match this virtual host. For the default virtual host (this file) this
          # value is not decisive as it is used as a last resort host regardless.
          # However, you must set it for any further virtual host explicitly.
          #ServerName www.example.com

          ServerAdmin webmaster@localhost
          DocumentRoot /var/www/vitamin.brokoli.B24
          ServerName vitamin.brokoli.B24.com
          ServerAlias www.vitamin.brokoli.B24.com

          <Directory /var/www/vitamin.brokoli.B24/nutrisi>
                  Options +Indexes
          </Directory>

          <Directory /var/www/vitamin.brokoli.B24>
                  Options +FollowSymLinks -Multiviews
                  AllowOverride All
          </Directory>

          # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
          # error, crit, alert, emerg.
          # It is also possible to configure the loglevel for particular
          # modules, e.g.
          #LogLevel info ssl:warn

          ErrorLog ${APACHE_LOG_DIR}/error.log
          CustomLog ${APACHE_LOG_DIR}/access.log combined

          # For most configuration files from conf-available/, which are
          # enabled or disabled at a global level, it is possible to
          # include a line for only one particular virtual host. For example the
          # following line enables the CGI configuration for this host only
          # after it has been globally disabled with "a2disconf".
          #Include conf-available/serve-cgi-bin.conf
  </VirtualHost>
  ```

<br>

## Soal 14

> Tambahkan alias untuk folder /public/images/ pada subdomain www.vitamin.brokoli.yyy.com agar folder tersebut dapat diakses langsung melalui url www.vitamin.brokoli.yyy.com/img.

> _Add an alias for the /public/images/ folder on the www.vitamin.brokoli.yyy.com subdomain so that the folder can be accessed directly through the url www.vitamin.brokoli.yyy.com/img._

**Answer:**

- Screenshot

  ![alt text](Screenshot/14a.png)
  ![alt text](Screenshot/14b.png)

- Explanation

  Untuk melakukan hal ini hanya harus menambahkan Alias ke dalam konfigurasi website.

  - vitamin.brokoli.B24.conf

  ```
  <VirtualHost *:80>
          # The ServerName directive sets the request scheme, hostname and port that
          # the server uses to identify itself. This is used when creating
          # redirection URLs. In the context of virtual hosts, the ServerName
          # specifies what hostname must appear in the request's Host: header to
          # match this virtual host. For the default virtual host (this file) this
          # value is not decisive as it is used as a last resort host regardless.
          # However, you must set it for any further virtual host explicitly.
          #ServerName www.example.com

          ServerAdmin webmaster@localhost
          DocumentRoot /var/www/vitamin.brokoli.B24
          ServerName vitamin.brokoli.B24.com
          ServerAlias www.vitamin.brokoli.B24.com

          <Directory /var/www/vitamin.brokoli.B24/nutrisi>
                  Options +Indexes
          </Directory>
          Alias "/img" "/var/www/vitamin.brokoli.B24/public/images"

          # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
          # error, crit, alert, emerg.
          # It is also possible to configure the loglevel for particular
          # modules, e.g.
          #LogLevel info ssl:warn

          ErrorLog ${APACHE_LOG_DIR}/error.log
          CustomLog ${APACHE_LOG_DIR}/access.log combined

          # For most configuration files from conf-available/, which are
          # enabled or disabled at a global level, it is possible to
          # include a line for only one particular virtual host. For example the
          # following line enables the CGI configuration for this host only
          # after it has been globally disabled with "a2disconf".
          #Include conf-available/serve-cgi-bin.conf
  </VirtualHost>
  ```

<br>

## Soal 15

> Karena terdapat resep rahasia di file /secret/recipe_secret.txt pada subdomain www.vitamin.brokoli.yyy.com, konfigurasikan folder /secret agar tidak dapat diakses oleh pengguna (dengan menampilkan 403 Forbidden).

> _Because there is a secret recipe in the /secret/recipe_secret.txt file on the www.vitamin.brokoli.yyy.com subdomain, configure the /secret folder so that it cannot be accessed by users (by displaying 403 Forbidden)._

**Answer:**

- Screenshot

  ![alt text](Screenshot/15a.png)
  ![alt text](Screenshot/15b.png)

- Explanation

  Untuk melakukan hal ini kita akan konfigurasi website. Di direktori /secret kita akan tambahkan beberapa option. Pertama Option akan menjadi `-Indexes` agar direktori tidak di list. Lalu untuk deny semua access ke dalam file di direktori secret kita akan tambahkan `Require all denied`

  - vitamin.brokoli.B24

  ```
  <VirtualHost *:80>
          # The ServerName directive sets the request scheme, hostname and port that
          # the server uses to identify itself. This is used when creating
          # redirection URLs. In the context of virtual hosts, the ServerName
          # specifies what hostname must appear in the request's Host: header to
          # match this virtual host. For the default virtual host (this file) this
          # value is not decisive as it is used as a last resort host regardless.
          # However, you must set it for any further virtual host explicitly.
          #ServerName www.example.com

          ServerAdmin webmaster@localhost
          DocumentRoot /var/www/vitamin.brokoli.B24
          ServerName vitamin.brokoli.B24.com
          ServerAlias www.vitamin.brokoli.B24.com

          <Directory /var/www/vitamin.brokoli.B24/nutrisi>
                  Options +Indexes
          </Directory>

          <Directory /var/www/vitamin.brokoli.B24/secret>
                  Options -Indexes
                  Require all denied
          </Directory>

          Alias "/img" "/var/www/vitamin.brokoli.B24/public/images"

          # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
          # error, crit, alert, emerg.
          # It is also possible to configure the loglevel for particular
          # modules, e.g.
          #LogLevel info ssl:warn

          ErrorLog ${APACHE_LOG_DIR}/error.log
          CustomLog ${APACHE_LOG_DIR}/access.log combined

          # For most configuration files from conf-available/, which are
          # enabled or disabled at a global level, it is possible to
          # include a line for only one particular virtual host. For example the
          # following line enables the CGI configuration for this host only
          # after it has been globally disabled with "a2disconf".
          #Include conf-available/serve-cgi-bin.conf
  </VirtualHost>
  ```

<br>

## Soal 16

> Karena dinilai terlalu panjang coba ubah konfigurasi www.vitamin.brokoli.yyy.com/public/js menjadi www.vitamin.brokoli.yyy.com/js

> _Since it is considered too long, change the configuration from www.vitamin.brokoli.yyy.com/public/js to www.vitamin.brokoli.yyy.com/js._

**Answer:**

- Screenshot

  ![alt text](Screenshot/16a.png)
  ![alt text](Screenshot/16b.png)

- Explanation

  Untuk melakukan hal ini hanya harus menambahkan Alias untuk js ke dalam konfigurasi website.

  - vitamin.brokoli.B24

  ```
  <VirtualHost *:80>
          # The ServerName directive sets the request scheme, hostname and port that
          # the server uses to identify itself. This is used when creating
          # redirection URLs. In the context of virtual hosts, the ServerName
          # specifies what hostname must appear in the request's Host: header to
          # match this virtual host. For the default virtual host (this file) this
          # value is not decisive as it is used as a last resort host regardless.
          # However, you must set it for any further virtual host explicitly.
          #ServerName www.example.com

          ServerAdmin webmaster@localhost
          DocumentRoot /var/www/vitamin.brokoli.B24
          ServerName vitamin.brokoli.B24.com
          ServerAlias www.vitamin.brokoli.B24.com

          <Directory /var/www/vitamin.brokoli.B24/nutrisi>
                  Options +Indexes
          </Directory>

          <Directory /var/www/vitamin.brokoli.B24>
                  Options +FollowSymLinks -Multiviews
                  AllowOverride All
          </Directory>

          <Directory /var/www/vitamin.brokoli.B24/secret>
                  Options -Indexes
                  Require all denied
          </Directory>

          Alias "/img" "/var/www/vitamin.brokoli.B24/public/images"
          Alias "/js" "/var/www/vitamin.brokoli.B24/public/js"

          # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
          # error, crit, alert, emerg.
          # It is also possible to configure the loglevel for particular
          # modules, e.g.
          #LogLevel info ssl:warn

          ErrorLog ${APACHE_LOG_DIR}/error.log
          CustomLog ${APACHE_LOG_DIR}/access.log combined

          # For most configuration files from conf-available/, which are
          # enabled or disabled at a global level, it is possible to
          # include a line for only one particular virtual host. For example the
          # following line enables the CGI configuration for this host only
          # after it has been globally disabled with "a2disconf".
          #Include conf-available/serve-cgi-bin.conf
  </VirtualHost>
  ```

<br>

## Soal 17

> Supaya Web kita aman terkendali maka ubah konfigurasi www.k1.vitamin.brokoli.yyy.com menjadi hanya bisa di akses oleh port 9696 dan 8888 Gunakan sumber yang tersedia di [disini](https://docs.google.com/uc?export=download&id=1SRnelY4XrtmhJg_Ly1nUJo1Jf91SnmtB)

> _To keep our web secure, configure www.k1.vitamin.brokoli.yyy.com to only be accessible through ports 9696 and 8888. [Link](https://docs.google.com/uc?export=download&id=1SRnelY4XrtmhJg_Ly1nUJo1Jf91SnmtB)._

**Answer:**

- Screenshot

  ![alt text](Screenshot/17a.png)
  ![alt text](Screenshot/17b.png)

- Explanation

  Pertama-tama kita akan download zip nya dan unzip di /var/www. Setelah itu kita akan membuat konfigurasi website yang berada di /etc/apache2/sites-available. Pertama kita akan buat filenya dengan cara copy kerangka `cp 000-default.conf k1.vitamin.brokoli.B24.conf`. Lalu di file itu kita akan konfigurasi websitenya. Pertama kita ubah DocumentRoot-nya agar menunjuk ke folder yang telah kita unzip sebelumnya. Setelah itu kita akan set ServerName menjadi k1.vitamin.brokoli.B24.com dan ServerAlias menjadi www.k1.vitamin.brokoli.B24.com. Beda dari soal sebelumnya adalah kita akan membuat 2 virtual host untuk masing-masing port. Lalu aktifkan virtual host `a2ensite k1.vitamin.brokoli.B24.conf`

  - k1.vitamin.brokoli.B24.conf

  ```
  <VirtualHost *:9696>
          ServerAdmin webmaster@localhost
          DocumentRoot /var/www/k1.vitamin.brokoli.B24
          ServerName k1.vitamin.brokoli.B24.com
          ServerAlias www.k1.vitamin.brokoli.B24.com

          ErrorLog ${APACHE_LOG_DIR}/error.log
          CustomLog ${APACHE_LOG_DIR}/access.log combined


  </VirtualHost>

  <VirtualHost *:8888>
          ServerAdmin webmaster@localhost
          DocumentRoot /var/www/k1.vitamin.brokoli.B24
          ServerName k1.vitamin.brokoli.B24.com
          ServerAlias www.k1.vitamin.brokoli.B24.com

          ErrorLog ${APACHE_LOG_DIR}/error.log
          CustomLog ${APACHE_LOG_DIR}/access.log combined


  </VirtualHost>
  ```

  Setelah itu kita akan konfigurasi apache2 agar bisa listen untuk port 8888 dan 9696. Caranya adalah menambahkannya ke /etc/apache2/ports.conf.

  - ports.conf
  ```
  # If you just change the port or add more ports here, you will likely also
  # have to change the VirtualHost statement in
  # /etc/apache2/sites-enabled/000-default.conf

  Listen 80
  Listen 9696
  Listen 8888

  <IfModule ssl_module>
          Listen 443
  </IfModule>

  <IfModule mod_gnutls.c>
          Listen 443
  </IfModule>

  # vim: syntax=apache ts=4 sw=4 sts=4 sr noet
  ```

<br>

## Soal 18

> Lanjutkan dari nomor sebelumnya buatlah autentikasi dengan username “Seblak” dan password “sehatyyy” dengan yyy adalah kode kelompok. Letakkan Document Root pada /var/www/k1.vitamin.brokoli.yyy.

> _Continuing from the previous point, create authentication with the username “Seblak” and the password “sehatyyy” where yyy is the group code. Set the Document Root to /var/www/k1.vitamin.brokoli.yyy._

**Answer:**

- Screenshot

  ![alt text](Screenshot/17a.png)
  ![alt text](Screenshot/18a.png)

- Explanation

  Untuk melakukan hal ini kita akan gunakan command `htpasswd`. Kita akan menggunakan command itu untuk membuat user untuk autentikasi sesuai soal.

  - Command
  ```
  htpasswd -cb /etc/apache2/.htpasswd Seblak sehatB24
  ```

  Selanjutya kita akan modifikasi virtual host konfigurasi website agar bisa menggunakan autentikasi. Kita akan menspesifikasikan sebuah direktori yang diperlukan untuk autentikasi.

  - k1.vitamin.brokoli.B24.conf
  ```
  <VirtualHost *:9696>
          ServerAdmin webmaster@localhost
          DocumentRoot /var/www/k1.vitamin.brokoli.B24
          ServerName k1.vitamin.brokoli.B24.com
          ServerAlias www.k1.vitamin.brokoli.B24.com

          <Directory "/var/www/k1.vitamin.brokoli.B24">
                  AuthType Basic
                  AuthName "Restricted Content"
                  AuthUserFile /etc/apache2/.htpasswd
                  Require valid-user
          </Directory>

          ErrorLog ${APACHE_LOG_DIR}/error.log
          CustomLog ${APACHE_LOG_DIR}/access.log combined

  </VirtualHost>

  <VirtualHost *:8888>
          ServerAdmin webmaster@localhost
          DocumentRoot /var/www/k1.vitamin.brokoli.B24
          ServerName k1.vitamin.brokoli.B24.com
          ServerAlias www.k1.vitamin.brokoli.B24.com

          <Directory "/var/www/k1.vitamin.brokoli.B24">
                  AuthType Basic
                  AuthName "Restricted Content"
                  AuthUserFile /etc/apache2/.htpasswd
                  Require valid-user
          </Directory>

          ErrorLog ${APACHE_LOG_DIR}/error.log
          CustomLog ${APACHE_LOG_DIR}/access.log combined

  </VirtualHost>
  ```

<br>

## Soal 19

> Konfigurasikan agar setiap kali IP Brokoli diakses dengan lynx, secara otomatis akan dialihkan ke www.brokoli.yyy.com (alias).

> _Configure it so that every time Brokoli's IP is accessed using lynx, it is automatically redirected to www.brokoli.yyy.com (alias)._

**Answer:**

- Screenshot

  ```lynx 192.205.2.4```

  ![alt text](Screenshot/19a.png)

- Explanation

  Untuk melakukan hal ini kita akan menggunakan redirect. Saat kita akses IP brokoli langsung dengan lynx, apache akan mengarahkannya ke default. Karena itu kita harus modifikasi config default di `/etc/apache2/sites-availabe/000-default.conf` 

  - 000-default.conf
  ```
  <VirtualHost *:80>
          ServerAdmin webmaster@localhost
          DocumentRoot /var/www/html

          ErrorLog ${APACHE_LOG_DIR}/error.log
          CustomLog ${APACHE_LOG_DIR}/access.log combined

          Redirect / http://www.brokoli.B24.com/
  </VirtualHost>
  ```

  Dengan redirect itu maka jika akses ip dengan lynx akan langsung ke redirect ke http://www.brokoli.B24.com/ . Tentunya brokoli.B24.com pada apache harus merujuk sesuatu. Untuk kepentingan tes kita akan menggunakan folder sayur_webserver_nginx sebagai DocumentRoot. Jadi buat file config baru di sites-available. Dengan ini jika gunakan lynx dengan ip maka akan ke index sayur_webserver_nginx.

  - brokoli.B24.com.conf
  ```
  <VirtualHost *:80>
          ServerAdmin webmaster@localhost
          DocumentRoot /var/www/sayur_webserver_nginx
          ServerName brokoli.B24.com
          ServerAlias www.brokoli.B24.com

          ErrorLog ${APACHE_LOG_DIR}/error.log
          CustomLog ${APACHE_LOG_DIR}/access.log combined
  </VirtualHost>
  ```

<br>

## Soal 20

> Karena jumlah pengunjung website www.vitamin.brokoli.yyy.com semakin meningkat dan terdapat banyak gambar random, ubah permintaan gambar yang mengandung substring "vitamin" agar diarahkan ke file vitamin.png.

> _Since the number of visitors to www.vitamin.brokoli.yyy.com is increasing and there are many random images, redirect image requests that contain the substring "vitamin" to the file vitamin.png._

**Answer:**

- Screenshot

  ![alt text](Screenshot/20a.png)
  ![alt text](Screenshot/20b.png)

- Explanation

  Untuk melakukan ini kita harus membuat file png nya dulu agar bisa tahu jika ada file itu hal ini bisa berhasil atau tidak, jadi bisa copy saja dengan command `cp /var/www/vitamin.brokoli.B24/public/images/brokoli1.png /var/www/vitamin.brokoli.B24/public/images/vitamin.png`. Untuk memenuhi keinginan soal kita akan menggunakan rewrite rule lagi. Maka kita harus edit .htaccess pada /var/www/vitamin.brokoli.B24/ .

  - .htaccess

  ```
  RewriteEngine On

  RewriteCond %{REQUEST_FILENAME} !-d
  RewriteCond %{REQUEST_FILENAME}.php -f
  RewriteRule ^([^\.]+)$ $1.php [NC,L]

  # Untuk png
  RewriteCond %{REQUEST_URI} ^/public/images/(.*)(vitamin)(.*\.(png|jpg)) #kondisi pertama [(.*) adalah wildcard]
  RewriteCond %{REQUEST_URI} !/public/images/vitamin.png //kondisi kedua
  RewriteRule vitamin http://vitamin.brokoli.B24.com/public/images/vitamin.png [L,R=301] # The rule
  ```

  Ada 2 kondisi yang di cek untuk rule png ini. Kondisi yang pertama adalah untuk cek requested URI apakah mereka di direktori /public/images/. Lalu sisanya mencari pattern string vitamin di file namenya dan extension filenya harus png atau jpg karena image. Hal ini memastikan kalau dalam URI nya ada kata kata vitamin. Lalu pada kondisi kedua URI akan dicek apakah ia mencoba ke vitamin.png atau bukan agar tidak perlu di redirect. Lalu jika kondisi terpenuhi maka rule akan ter-apply dan akan rewrite URI agar ter redirect ke path vitamin.png.

<br>
  
## Problems

## Revisions (if any)