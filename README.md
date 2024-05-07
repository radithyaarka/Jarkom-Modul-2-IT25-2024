# Lapres-Jarkom-Modul-2-IT25-2024

## Anggota

| Nama                                          | NRP          |
| ----------------------------------------------| ------------ |
| Mohammad Arkananta Radithya Taratugang        | `5027221003` |
| Michael Wayne                                 | `5027221037` |


## SOAL 1
Untuk membantu pertempuran di Erangel, kamu ditugaskan untuk membuat jaringan komputer yang akan digunakan sebagai alat komunikasi. Sesuaikan rancangan Topologi dengan rancangan dan pembagian yang berada di link yang telah disediakan, dengan ketentuan nodenya sebagai berikut :
- DNS Master akan diberi nama `Pochinki`, sesuai dengan kota tempat dibuatnya server tersebut
- Karena ada kemungkinan musuh akan mencoba menyerang Server Utama, maka buatlah DNS Slave `Georgopol` yang mengarah ke Pochinki
- Markas pusat juga meminta dibuatkan tiga Web Server yaitu `Severny, Stalber, dan Lipovka`. Sedangkan `Mylta` akan bertindak sebagai Load Balancer untuk server-server tersebut

### Topologi Jaringan
![image](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/48807bac-8335-4c4b-92e3-332e6327271b)

#### Network Configuration
#### Erangel
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.76.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.76.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.76.3.1
	netmask 255.255.255.0

auto eth4
iface eth4 inet static
	address 10.76.4.1
	netmask 255.255.255.0
```
Pochinki (DNS Master)
```
auto eth0
iface eth0 inet static
    address 10.76.3.2
    netmask 255.255.255.0
    gateway 10.76.3.1
```
Georgopol (DNS Slave)
```
auto eth0
iface eth0 inet static
    address 10.76.4.2
    netmask 255.255.255.0
    gateway 10.76.4.1
```
Stalber (Web Server)
```
auto eth0
iface eth0 inet static
    address 10.76.2.2
    netmask 255.255.255.0
    gateway 10.76.2.1
```

Severny (Web Server)
```
auto eth0
iface eth0 inet static
    address 10.76.2.3
    netmask 255.255.255.0
    gateway 10.76.2.1
```

Mylta (Load Balancer)
```
auto eth0
iface eth0 inet static
    address 10.76.2.4
    netmask 255.255.255.0
    gateway 10.76.2.1
```

Lipovka (Web Server)
```
auto eth0
iface eth0 inet static
    address 10.76.2.5
    netmask 255.255.255.0
    gateway 10.76.2.1
```

Zharki (Client)
```
auto eth0
iface eth0 inet static
    address 10.76.1.2
    netmask 255.255.255.0
    gateway 10.76.1.1
```

YasnayaPolyana (Client)
```
auto eth0
iface eth0 inet static
    address 10.76.1.3
    netmask 255.255.255.0
    gateway 10.76.1.1
```

Primorsk (Client)
```
auto eth0
iface eth0 inet static
    address 10.76.1.4
    netmask 255.255.255.0
    gateway 10.76.1.1
```

Jalankan command berikut pada Erangle
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.76.0.0/16
```
Ketikkan command `cat /etc/resolv.conf` pada Erangel
![image](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/272e89f2-8b9e-4a2e-920a-97cf84f56882)

Masukkan command `echo nameserver 192.168.122.1 > /etc/resolv.conf`.

Untuk node yang lain, gunakan command `echo nameserver [IP DNS] > /etc/resolv.conf` dengan IP Pochinki yaitu `10.76.3.2`. Maka command nya adalah

Pada Pochinki jalankan command
```
apt-get update
apt-get install bind9 -y
```

## SOAL 2
Karena para pasukan membutuhkan koordinasi untuk mengambil airdrop, maka buatlah sebuah domain yang mengarah ke Stalber dengan alamat airdrop.xxxx.com dengan alias www.airdrop.xxxx.com dimana xxxx merupakan kode kelompok. Contoh : airdrop.it25.com

### Jawab
Jalankan command
```
nano /etc/bind/named.conf.local
```

Isi config sebagai berikut
```
zone "airdrop.it25.com" {
	type master;
	file "/etc/bind/airdrop/airdrop.it25.com";
};
```

Buat folder airdrop di dalam /etc/bind
```
mkdir /etc/bind/airdrop
```

Copy file db.local di /etc/bind ke folder airdrop, ubah namanya sesuai nama domain
```
cp /etc/bind/db.local /etc/bind/airdrop/airdrop.it25.com
```

Lalu jalankan command `nano /etc/bind/airdrop/airdrop.it25.com` dan isi config seperti ini
![image](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/67619214-cfde-4a51-9639-994e8ee450d6)

Lalu restart bind9 dengan `service bind9 restart`

soal_2.sh
```
echo nameserver 192.168.122.1 > /etc/resolv.conf

apt-get update

apt-get install bind9 -y

mkdir /etc/bind/airdrop /etc/bind/redzone /etc/bind/loot

echo '
zone "airdrop.it25.com" {
        type master;
        file "/etc/bind/airdrop/airdrop.it25.com";
};

zone "redzone.it25.com" {
        type master;
        file "/etc/bind/redzone/redzone.it25.com";
};

zone "loot.it25.com" {
        type master;
        file "/etc/bind/loot/loot.it25.com";
};
' > /etc/bind/named.conf.local

cp /etc/bind/db.local /etc/bind/airdrop/airdrop.it25.com

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     airdrop.it25.com. root.airdrop.it25.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      airdrop.it25.com.
@       IN      A       10.76.2.2
@       IN      AAAA    ::1
www     IN      CNAME   airdrop.it25.com.
' > /etc/bind/airdrop/airdrop.it25.com

service bind9 restart
```

## SOAL 3
Para pasukan juga perlu mengetahui mana titik yang sedang di bombardir artileri, sehingga dibutuhkan domain lain yaitu redzone.xxxx.com dengan alias www.redzone.xxxx.com yang mengarah ke Severny


### Jawab
Jalankan command
```
nano /etc/bind/named.conf.local
```

Isi config sebagai berikut
```
zone "redzone.it25.com" {
	type master;
	file "/etc/bind/redzone/redzone.it25.com";
};
```

Buat folder airdrop di dalam /etc/bind
```
mkdir /etc/bind/redzone
```

Copy file db.local di /etc/bind ke folder airdrop, ubah namanya sesuai nama domain
```
cp /etc/bind/db.local /etc/bind/redzone/redzone.it25.com
```

Lalu jalankan command `nano /etc/bind/airdrop/redzone.it25.com` dan isi config seperti ini
![image](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/55194293-5629-40b2-9cba-0755d67e124b)

Lalu restart bind9 dengan `service bind9 restart`

soal_3.sh
```
cp /etc/bind/db.local /etc/bind/redzone/redzone.it25.com

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     redzone.it25.com. root.redzone.it25.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      redzone.it25.com.
@       IN      A       10.76.2.3
@       IN      AAAA    ::1
www     IN      CNAME   redzone.it25.com.
' > /etc/bind/redzone/redzone.it25.com

service bind9 restart
```

## SOAL 4
Markas pusat meminta dibuatnya domain khusus untuk menaruh informasi persenjataan dan suplai yang tersebar. Informasi persenjataan dan suplai tersebut mengarah ke Mylta dan domain yang ingin digunakan adalah loot.xxxx.com dengan alias www.loot.xxxx.com


### Jawab
Jalankan command
```
nano /etc/bind/named.conf.local
```

Isi config sebagai berikut
```
zone "loot.it25.com" {
	type master;
	file "/etc/bind/loot/loot.it25.com";
};
```

Buat folder airdrop di dalam /etc/bind
```
mkdir /etc/bind/loot
```

Copy file db.local di /etc/bind ke folder airdrop, ubah namanya sesuai nama domain
```
cp /etc/bind/db.local /etc/bind/loot/loot.it25.com
```

Lalu jalankan command `nano /etc/bind/loot/loot.it25.com` dan isi config seperti ini
![image](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/c0dfc399-d731-4a8a-9b5d-ae9e6999e5a6)

Lalu restart bind9 dengan `service bind9 restart`

soal_4.sh
```
cp /etc/bind/db.local /etc/bind/loot/loot.it25.com

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     loot.it25.com. root.loot.it25.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      loot.it25.com.
@       IN      A       10.76.2.4
@       IN      AAAA    ::1
www     IN      CNAME   loot.it25.com.
' > /etc/bind/loot/loot.it25.com

service bind9 restart
```

## SOAL 5
Pastikan domain-domain tersebut dapat diakses oleh seluruh komputer (client) yang berada di Erangel

### Jawab

- Zharki
![Screenshot 2024-05-04 003054](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/8b3374f0-54c2-431c-96ce-46d069df1750)

- YasnayaPolyana
![image](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/89961080-8235-4bfa-8c73-9b751c192d36)

- Primorsk
![Screenshot 2024-05-04 003212](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/542b3488-0e6d-429e-9f67-b92ec4191ecb)

## SOAL 6
Beberapa daerah memiliki keterbatasan yang menyebabkan hanya dapat mengakses domain secara langsung melalui alamat IP domain tersebut. Karena daerah tersebut tidak diketahui secara spesifik, pastikan semua komputer (client) dapat mengakses domain redzone.xxxx.com melalui alamat IP Severny (Notes : menggunakan pointer record)

Jalankan command
```
nano /etc/bind/named.conf.local
```

Lalu tambahkan konfigurasi di bawah ini ke dalam file `named.conf.local`. Tambahkan reverse dari 3 byte awal dari IP Severny yaitu `10.76.2.3`, maka reverse 3 byte awalnya `2.76.10`.
```
zone "2.76.10.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/2.76.10.in-addr.arpa";
};
```

Buat direktori jarkom
```
mkdir /etc/bind/jarkom
```

Copy file `db.local` di `/etc/bind ke folder jarkom`, ubah namanya menjadi `2.76.10.in-addr.arpa`
```
cp /etc/bind/db.local /etc/bind/jarkom/2.76.10.in-addr.arpa
```

Jalankan command `nano /etc/bind/jarkom/2.76.10.in-addr.arpa` dan edit seperti ini

![image](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/bc7fa118-8ebf-4146-ba73-4890d545b50b)

Restart bind9 dengan `service bind9 restart`

Untuk mengecek apakah konfigurasi sudah benar atau belum, pertama kembalikan nameserver client ke Erangel dengan `echo nameserver 192.168.122.1 > /etc/resolv.conf`, lalu lakukan command ini untuk setiap client
```
apt-get update
apt-get install dnsutils
```
Lalu kembalikan nameserver ke Pochinki dengan `echo nameserver 10.76.3.2 > /etc/resolv.conf`

Jalankan command
```
host -t PTR 10.76.2.2
```

#### Testing
- Zharki
  
![Screenshot 2024-05-04 005112](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/46080433-474c-4aa3-81aa-8b8d1b695a75)

- YasnayaPolyana
  
![Screenshot 2024-05-04 005317](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/9ebd2480-e901-44b8-be77-7f714a497090)

- Primorsk
  
![Screenshot 2024-05-04 004930](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/0a7964c6-51d9-4d9d-838b-b5e12e2d99a4)

## SOAL 7
Akhir-akhir ini seringkali terjadi serangan siber ke DNS Server Utama, sebagai tindakan antisipasi kamu diperintahkan untuk membuat DNS Slave di Georgopol untuk semua domain yang sudah dibuat sebelumnya

### Set up Pochinki
Jalankan `nano /etc/bind/named.conf.local`

Edit konfigurasi untuk setiap domain menjadi seperti ini, dimana `10.76.4.2` adalah IP Georgopol yang akan dijadikan DNS Slave
```
zone "airdrop.it25.com" {
    type master;
    also-notify { 10.76.4.2; };
    allow-transfer { 10.76.4.2; };
    file "/etc/bind/airdrop/airdrop.it25.com";
};

zone "redzone.it25.com" {
    type master;
    also-notify { 10.76.4.2; };
    allow-transfer { 10.76.4.2; };
    file "/etc/bind/redzone/redzone.it25.com";
};

zone "loot.it25.com" {
    type master;
    also-notify { 10.76.4.2; };
    allow-transfer { 10.76.4.2; };
    file "/etc/bind/loot/loot.it25.com";
};
```

Restart bind9 dengan `service bind9 restart`

### Georgopol
Pastikan Georgopol terhubung dengan internet menggunakan `echo nameserver 192.168.122.1 > /etc/resolv.conf` dimana `192.168.122.1` adalah IP Erangel

Jalankan command berikut
```
apt-get update
apt-get install bind9 -y
```

Jalankan command `nano /etc/bind/named.conf.local`

Buka file dengan `nano /etc/bind/named.conf.local`. Tambahkan konfigurasi berikut pada file tersebut
```
zone "airdrop.it25.com" {
    type slave;
    masters { 10.76.3.2; };
    file "/var/lib/bind/airdrop.it25.com";
};

zone "redzone.it25.com" {
    type slave;
    masters { 10.76.3.2; };
    file "/var/lib/bind/redzone.it25.com";
};

zone "loot.it25.com" {
    type slave;
    masters { 10.76.3.2; };
    file "/var/lib/bind/loot.it25.com";
};
```

Restart bind9 dengan `service bind9 restart`

### Testing
- Matikan bind9 pada Pochinki dengan command `service bind9 stop`

- Lakukan command berikut untuk semua client (Zharki, YasnayaPolyana, Primorsk)
```
nano /etc/resolv.conf
```

- Edit Konfigurasi untuk semua file `resolv.conf` pada setiap client, tambahkan IP Georgopol
```
nameserver 10.76.3.2
nameserver 10.76.4.2
```
![Screenshot 2024-05-04 010340](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/efa1cb04-056c-4c19-857d-856afc7ec0ac)

- Test dengan ping ke domain-domain yang telah dibuat tadi
- Zharki
![Screenshot 2024-05-04 003054](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/c42854d0-fc81-40fd-bd62-c543d8e487fd)
- YasnayaPolyana
![Screenshot 2024-05-04 010808](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/a363fd7f-d117-481f-92a4-677ea80c0b48)
- Primorsk
![Screenshot 2024-05-04 010851](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/aaf69db2-0dcc-4f8b-be46-ad30c5030e7f)

Terlihat bahwa konfigurasi DNS Slave telah berhasil

## SOAL 8
Kamu juga diperintahkan untuk membuat subdomain khusus melacak airdrop berisi peralatan medis dengan subdomain medkit.airdrop.xxxx.com yang mengarah ke Lipovka

#### Membuat subdomain
- Pada Pochinki, jalankan command `nano /etc/bind/airdrop/airdrop.it25.com`
- Tambahkan konfigurasi untuk subdomain seperti ini, ip adalah ip Lipovka
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     airdrop.it25.com. root.airdrop.it25.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      airdrop.it25.com.
@       IN      A       10.76.2.4
@       IN      AAAA    ::1
www     IN      CNAME   airdrop.it25.com.
medkit  IN      A       10.76.2.5
```
Restart bind9 dengan `service bind9 restart`

### Testing
![Screenshot 2024-05-04 014251](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/2653b0b7-92bc-46f5-8b20-238ed9dfcbbe)

## SOAL 9
Terkadang red zone yang pada umumnya di bombardir artileri akan dijatuhi bom oleh pesawat tempur. Untuk melindungi warga, kita diperlukan untuk membuat sistem peringatan air raid dan memasukkannya ke domain siren.redzone.xxxx.com dalam folder siren dan pastikan dapat diakses secara mudah dengan menambahkan alias www.siren.redzone.xxxx.com dan mendelegasikan subdomain tersebut ke Georgopol dengan alamat IP menuju radar di Severny

### Konfigurasi Pochinki
Jalankan `mkdir /etc/bind/siren` lalu `cp /etc/bind/redzone/redzone.it25.com /etc/bind/siren/siren.redzone.it25.com`

Jalankan `nano /etc/bind/siren/siren.redzone.it25.com`
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     redzone.it25.com. root.redzone.it25.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      redzone.it25.com.
@       IN      A       10.76.2.3
www     IN      CNAME   redzone.it25.com.
siren   IN      A       10.76.4.2
ns1     IN      A       10.76.4.2
siren   IN      NS      ns1
@       IN      AAAA    ::1
```
Edit file `etc/bind/named.conf.options` pada Pochinki
```
nano /etc/bind/named.conf.options
```
Tambahkan line berikut
```
allow-query{any;};
```
![Screenshot 2024-05-04 041942](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/a9d81ba3-1892-4239-a8d8-4a29cdb55e03)

Jalankan `nano /etc/bind/named.conf.local` lalu tambahkan:
```
zone "siren.redzone.it25.com" {
    type master;
    file "/etc/bind/siren/siren.redzone.it25.com";
    allow-transfer { 10.76.4.2; };
};
```

Restart bind9 dengan `service bind9 restart`

#### Konfigurasi Georgopol
- Edit file `/etc/bind/named.conf.options` pada Georgopol
```
nano /etc/bind/named.conf.options
```

- Tambahkan line berikut
```
allow-query{any;};
```
![Screenshot 2024-05-04 041942](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/a9d81ba3-1892-4239-a8d8-4a29cdb55e03)

- Jalankan `nano /etc/bind/named.conf.local`, tambahkan konfigurasi di bawah ini
```
zone "siren.redzone.it25.com" {
    type master;
    file "/etc/bind/siren/siren.redzone.it25.com";
};
```

Jalankan command `mkdir /etc/bind/siren` lalu `cp /etc/bind/db.local /etc/bind/siren/siren.redzone.it25.com`

Buka dan edit file `/etc/bind/siren/siren.redzone.it25.com`
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     siren.redzone.it25.com. root.siren.redzone.it25.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      siren.redzone.it25.com.
@       IN      A       10.76.4.2
www     IN      CNAME   siren.redzone.it25.com.
@       IN      AAAA    ::1
```

Restart bind9 dengan `service bind9 restart`


#### Testing
- Zharki
![image](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/4e30a99b-54ef-4c00-a369-695967142ac0)
- YasnayaPolyana
![image](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/926569e4-8168-4296-9448-e1a5ea6159af)
- Primorsk
![Screenshot 2024-05-04 195017](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/066b5361-1e52-4d01-9077-cb44945faa47)

## SOAL 10
Markas juga meminta catatan kapan saja pesawat tempur tersebut menjatuhkan bom, maka buatlah subdomain baru di subdomain siren yaitu log.siren.redzone.xxxx.com serta aliasnya www.log.siren.redzone.xxxx.com yang juga mengarah ke Severny

Jalankan command `nano /etc/bind/siren/siren.redzone.it25.com` Lalu edit seperti ini
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     siren.redzone.it25.com. root.siren.redzone.it25.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      siren.redzone.it25.com.
@       IN      A       10.76.4.2
www     IN      CNAME   siren.redzone.it25.com.
log     IN      A       10.76.4.2
www.log IN      CNAME   siren.redzone.it25.com.
@       IN      AAAA    ::1
```

Restart bind9 dengan `service bind9 restart`

## Soal 11
Setelah pertempuran mereda, warga Erangel dapat kembali mengakses jaringan luar, tetapi hanya warga Pochinki saja yang dapat mengakses jaringan luar secara langsung. Buatlah konfigurasi agar warga Erangel yang berada diluar Pochinki dapat mengakses jaringan luar melalui DNS Server Pochinki

Jalankan command
```
nano /etc/bind/named.conf.options
```

Lalu uncomment forwarder seperti pada gambar, ip diisi ip Erangel

![Screenshot 2024-05-04 033959](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/42b63d58-f7bb-4f1c-b871-b47832923c6a)

Restart bind9 dengan `service bind9 restart`

### Testing
![image](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/521a8ce3-b6d5-4007-ae4b-589a9595b47a)

## Soal 12

- instalasi semua dependencies
  
```
apt-get update
apt-get install lynx
apt-get install apache2
apt-get install php
apt-get install libapache2-mod-php7.0
apt-get install nginx -y
apt install nginx php php-fpm -y
```

- jalankan `cd /etc/apache2/sites-available` lalu copy file defaultnya

```
cp 000-default.conf it.25.conf
```

ubah isi filenya dengan command `nano it.25.conf`

ubah bagian ini saja

```
<VirtualHost *:8080> #ubah portnya menjadi 8080

ServerAdmin webmaster@localhost
DocumentRoot /var/www/it25 #ubah document rootnya
```

![image](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/03160f98-c088-47f1-94e5-e765df80efa9)


- Tambahkan port 8080 pada file ports.conf di directory `/etc/apache2`

```
Listen 8080
```

- Aktifkan konfigurasi
  
`a2ensite it25.conf`

- Restart apache

`service apache2 restart`

- buat directory it25 di /var/www lalu tambahkan index phpnya

`mkdir /var/www/it25`

```
<?php
$hostname = gethostname();
$date = date('Y-m-d H:i:s');
$php_version = phpversion();
$username = get_current_user();



echo "Hello World!<br>";
echo "Saya adalah: $username<br>";
echo "Saat ini berada di: $hostname<br>";
echo "Versi PHP yang saya gunakan: $php_version<br>";
echo "Tanggal saat ini: $date<br>";
?>
```

- jalankan
  
```  
lynx http://10.67.1.2:8080
```

![Screenshot 2024-05-04 205242](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/e1022e3f-1a22-4466-9491-8aebfd274374)

## Soal 13

- instalasi semua dependencies
  
```
apt-get update
apt-get install apache2
a2enmod proxy
a2enmod proxy_http
a2enmod proxy_balancer
a2enmod lbmethod_byrequests
service apache2 restart
```
Jalankan semua webserver

pindah ke direktori /etc/apache2/sites-available/ dengan command `cd /etc/apache2/sites-available/`

- buat file /etc/apache2/sites-available/it25.conf

- lalu masukkan

```
<VirtualHost *:8080>
        <proxy balancer://itbalancer>
                BalancerMember http://10.76.2.2:8080
                BalancerMember http://10.76.2.3:8080
                BalancerMember http://10.76.2.5:8080
                ProxySet lbmethod=bytraffic
        </proxy>
        ProxyPreserveHost On
        ProxyPass / balancer://itbalancer/
        ProxyPassReverse / balancer://itbalancer/
</VirtualHost>
```

ProxySet bytraffice berarti load-balancer menggunakan metode round robin

- atur port tambahan. Tambahkan line berikut di /etc/apache2/ports.conf

Listen 8080


- Jalankan semua command berikut
  
```
a2enmod proxy
a2enmod proxy_http
a2enmod proxy_balancer
a2enmod lbmethod_bytraffic
```

- pindah direktori ke /etc/apache2/sites-available

- lalu jalankan

`a2ensite it25.conf`


- kembali ke root dan restart

`service apache2 restart`
![image](https://github.com/radithyaarka/Jarkom-Modul-2-IT25-2024/assets/143694651/94dbf3ca-838c-4018-9f36-2c62484e88b5)

## Soal 15
Markas pusat meminta laporan hasil benchmark dengan menggunakan apache benchmark dari load balancer dengan 2 web server yang berbeda tersebut dan meminta secara detail dengan ketentuan:
a. Nama Algoritma Load Balancer
b. Report hasil testing apache benchmark 
c. Grafik request per second untuk masing masing algoritma. 
d. Analisis

**Client (Zharki, Yasnaya Polyana, dan Primorsk)**

```
ab -n 10 -c 2 http://10.76.2.4/
```
**Round Robin (Default)**
```
This is ApacheBench, Version 2.3 <$Revision: 1706008 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 10.76.2.4 (be patient).....done


Server Software:        nginx/1.10.3
Server Hostname:        10.76.2.4
Server Port:            80

Document Path:          /
Document Length:        166 bytes

Concurrency Level:      2
Time taken for tests:   0.026 seconds
Complete requests:      10
Failed requests:        0
Total transferred:      3120 bytes
HTML transferred:       1660 bytes
Requests per second:    387.61 [#/sec] (mean)
Time per request:       5.160 [ms] (mean)
Time per request:       2.580 [ms] (mean, across all concurrent requests)
Transfer rate:          118.10 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        1    1   0.1      1       1
Processing:     2    4   2.7      3       9
Waiting:        2    4   2.7      3       9
Total:          3    5   2.7      3      10

Percentage of the requests served within a certain time (ms)
  50%      3
  66%      7
  75%      7
  80%      9
  90%     10
  95%     10
  98%     10
  99%     10
 100%     10 (longest request)
```

**Least-connection**
```
This is ApacheBench, Version 2.3 <$Revision: 1706008 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 10.76.2.4 (be patient).....done


Server Software:        nginx/1.10.3
Server Hostname:        10.76.2.4
Server Port:            80

Document Path:          /
Document Length:        166 bytes

Concurrency Level:      2
Time taken for tests:   0.022 seconds
Complete requests:      10
Failed requests:        0
Total transferred:      3120 bytes
HTML transferred:       1660 bytes
Requests per second:    451.92 [#/sec] (mean)
Time per request:       4.426 [ms] (mean)
Time per request:       2.213 [ms] (mean, across all concurrent requests)
Transfer rate:          137.69 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    1   0.4      1       2
Processing:     2    3   2.0      2       7
Waiting:        2    3   2.0      2       7
Total:          3    4   2.4      3       9

Percentage of the requests served within a certain time (ms)
  50%      3
  66%      4
  75%      4
  80%      9
  90%      9
  95%      9
  98%      9
  99%      9
 100%      9 (longest request)
 ```


**IP Hash**
 ```
This is ApacheBench, Version 2.3 <$Revision: 1706008 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 10.76.2.4 (be patient).....done


Server Software:        nginx/1.10.3
Server Hostname:        10.76.2.4
Server Port:            80

Document Path:          /
Document Length:        166 bytes

Concurrency Level:      2
Time taken for tests:   0.025 seconds
Complete requests:      10
Failed requests:        0
Total transferred:      3120 bytes
HTML transferred:       1660 bytes
Requests per second:    392.74 [#/sec] (mean)
Time per request:       5.092 [ms] (mean)
Time per request:       2.546 [ms] (mean, across all concurrent requests)
Transfer rate:          119.66 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    1   0.1      1       1
Processing:     2    4   2.2      4       7
Waiting:        2    4   2.2      3       7
Total:          3    5   2.1      4       8

Percentage of the requests served within a certain time (ms)
  50%      4
  66%      7
  75%      7
  80%      8
  90%      8
  95%      8
  98%      8
  99%      8
 100%      8 (longest request)
 ```


**Generic Hash**
 ```
This is ApacheBench, Version 2.3 <$Revision: 1706008 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 10.76.2.4 (be patient).....done


Server Software:        nginx/1.10.3
Server Hostname:        10.76.2.4
Server Port:            80

Document Path:          /
Document Length:        166 bytes

Concurrency Level:      2
Time taken for tests:   0.024 seconds
Complete requests:      10
Failed requests:        0
Total transferred:      3120 bytes
HTML transferred:       1660 bytes
Requests per second:    424.39 [#/sec] (mean)
Time per request:       4.713 [ms] (mean)
Time per request:       2.356 [ms] (mean, across all concurrent requests)
Transfer rate:          129.31 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    1   0.3      1       1
Processing:     2    4   2.9      3      10
Waiting:        2    4   2.9      3      10
Total:          2    5   2.9      3      10

Percentage of the requests served within a certain time (ms)
  50%      3
  66%      5
  75%      6
  80%      9
  90%     10
  95%     10
  98%     10
  99%     10
 100%     10 (longest request)
 ```