# Jarkom-Modul-3-D04-2023
# Anggota Kelompok
|                Nama                |    NRP     |
| :--------------------------------: | :--------: |
|       Muhammad Rafi Sutrisno       | 5025211167 |
|      Nadira Milha Nailul Fath      | 5025211253 |
## Topologi
<img width="348" alt="Screenshot 2023-11-19 151156" src="https://github.com/nadiramlha/Jarkom-Modul-3-D04-2023/assets/88009253/ccee91f5-a45c-4142-a932-d79addecfa8f">

## Konfigurasi masing-masing node
Ketentuan konfigurasi
| Node    | Kategori            | Konfigurasi IP |
| :-----: | :-----------------: | :------------: |
| Aura    | Router (DHCP Relay) | Dynamic        |
| Himmel  | DHCP Server         | Static         |
| Heiter  | DNS Server          | Static         |
| Denken  | Database Server     | Static         |
| Eisen   | Load Balancer       | Static         |
| Frieren | Laravel Worker      | Static         |
| Flamme  | Laravel Worker      | Static         |
| Fern    | Laravel Worker      | Static         |
| Lawine  | PHP Worker          | Static         |
| Linie   | PHP Worker          | Static         |         
| Lugner  | PHP Worker          | Static         |
| Revolte | Client              | Dynamic        |
| Richter | Client              | Dynamic        |
| Sein    | Client              | Dynamic        |
| Stark   | Client              | Dynamic        |

Dengan menggunakan image ``danielcristh0/debian-buster:1.1``

- Aura
  ```bash
  auto eth0
  iface eth0 inet dhcp
  up iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.193.0.0/16

  auto eth1
  iface eth1 inet static
        address 192.193.1.0
	    netmask 255.255.255.0

  auto eth2
  iface eth2 inet static
    	address 192.193.2.0
	    netmask 255.255.255.0

  auto eth3
  iface eth3 inet static
    	address 192.193.3.0
	    netmask 255.255.255.0

  auto eth4
  iface eth4 inet static
    	address 192.193.4.0
	    netmask 255.255.255.0
  ```
- Himmel
  ```bash
  auto eth0
  iface eth0 inet static
    	address 192.193.1.1
	    netmask 255.255.255.0
	    gateway 192.193.1.0
  ```
- Heiter
  ```bash
  auto eth0
  iface eth0 inet static
    	address 192.193.1.2
	    netmask 255.255.255.0
	    gateway 192.193.1.0
  ```
- Client
  ```bash
  auto eth0
  iface eth0 inet dhcp
  ```
Konfigurasi seluruh node 
(Fixed Address), kecuali rooter, dns server, dhcp server
```
auto eth0
iface eth0 inet dhcp
hwaddress ether 'hwaddress_milik_Node'
```

## Nomor 1
### Soal
Setelah mengalahkan Demon King, perjalanan berlanjut. Kali ini, kalian diminta untuk melakukan register domain berupa riegel.canyon.D04.com untuk worker Laravel dan granz.channel.D04.com untuk worker PHP (0) mengarah pada worker yang memiliki IP 192.193.x.1.

Lakukan konfigurasi sesuai dengan peta yang sudah diberikan. (1)
### Penyelesaian
Register domain riegel.canyon.D04.com untuk worker Laravel & domain granz.channel.D04.com untuk worker PHP
- Pada DNS Server (Heiter)
  Langkah pertama adalah dengan menyambungkan node Heiter ke internet dengan cara menuliskan ip dns
  ```bash
  echo 'nameserver 192.168.122.1' > /etc/resolv.conf
  ```
  lakukan instalasi pada bind9, buat folder baru, kemudian copy file ``/etc/bind/`` ganti nama file dengan nama masing-masing domain sebagai berikut
  ```bash
  apt-get update
  apt-get install bind9 -y
  mkdir /etc/bind/jarkom
  cp /etc/bind/db.local /etc/bind/jarkom/riegel.canyon.D04.com
  cp /etc/bind/db.local /etc/bind/jarkom/granz.channel.D04.com
  ```
  Ubah isi file ``/etc/bind/named.conf.local``
  ```bash
  zone "riegel.canyon.D04.com" {
  	  type master;
  	  file "/etc/bind/jarkom/riegel.canyon.D04.com";
  };

  zone "granz.channel.D04.com" {
  	  type master;
  	  file "/etc/bind/jarkom/granz.channel.D04.com";
  };

  zone "4.193.192.in-addr.arpa" {
  	  type master;
  	  file "/etc/bind/jarkom/4.193.192.in-addr.arpa";
  };

  zone "3.193.192.in-addr.arpa" {
  	  type master;
  	  file "/etc/bind/jarkom/3.193.192.in-addr.arpa";
  };
  ```
  Pada masing-masing file yang di copy isikan konfigurasi domain berikut
  - riegel.canyon.D04.com
    ```bash
    ;
    ; BIND data file for local loopback interface
    ;
    $TTL    604800
    @       IN      SOA     riegel.canyon.D04.com. root.riegel.canyon.D04.com. (
                   2022100601         ; Serial
                       604800         ; Refresh
                        86400         ; Retry
                      2419200         ; Expire
                       604800 )       ; Negative Cache TTL
    ;
    @       IN      NS      riegel.canyon.D04.com.
    @       IN      A       192.193.4.1     ; IP Frieren
    @       IN      AAAA    ::1
    ```
  - granz.channel.D04.com
    ```bash
    ;
    ; BIND data file for local loopback interface
    ;
    $TTL    604800
    @       IN      SOA     granz.channel.D04.com. root.granz.channel.D04.com. (
                       2022100601         ; Serial
                           604800         ; Refresh
                            86400         ; Retry
                          2419200         ; Expire
                           604800 )       ; Negative Cache TTL
    ;
    @       IN      NS      granz.channel.D04.com.
    @       IN      A       192.193.3.1     ; IP Lawine
    @       IN      AAAA    ::1
    ```
  Tambahkan juga reverse domain pada masing-masing domain dengan melakukan langkah konfigurasi yang sama seperti membuat domain
  - riegel.canyon.D04.com
    ```bash
    cp /etc/bind/db.local /etc/bind/jarkom/4.193.192.in-addr.arpa
    ```
    ubah isi file ``4.193.192.in-addr.arpa``
    ```bash
    ;
    ; BIND data file for local loopback interface
    ;
    $TTL    604800
    @       IN      SOA     riegel.canyon.D04.com. root.riegel.canyon.D04.com. (
                         2022100601         ; Serial
                             604800         ; Refresh
                              86400         ; Retry
                            2419200         ; Expire
                             604800 )       ; Negative Cache TTL
    ;
    4.193.192.in-addr.arpa.       	IN      NS      riegel.canyon.D04.com.
    1               		IN      PTR     riegel.canyon.D04.com.       ; byte ke 4 Freiner
    ```
  - granz.channel.D04.com
    ```bash
    cp /etc/bind/db.local /etc/bind/jarkom/3.193.192.in-addr.arpa
    ```
    ubah file ``/etc/bind/jarkom/3.193.192.in-addr.arpa``
    ```bash
    ;
    ; BIND data file for local loopback interface
    ;
    $TTL    604800
    @       IN      SOA     granz.channel.D04.com. root.granz.channel.D04.com. (
                        2022100601         ; Serial
                            604800         ; Refresh
                             86400         ; Retry
                           2419200         ; Expire
                            604800 )       ; Negative Cache TTL
    ;
    3.193.192.in-addr.arpa.       	IN      NS      granz.channel.D04.com.
    1               		IN      PTR     granz.channel.D04.com.       ; byte ke 4 Lawine
    ```
  Lakukan restart pada bind9
  ```bash
  service bind9 restart
  ```
## Fixed Address
Pada soal 1 diminta mengarahkan domain pada worker laravel dan php yang memiliki ip 192.193.x.1, sedangkan konfigurasi ip yang dimiliki oleh worker harus merupakan fixed address, maka perlu dilakukan beberapa hal berikut.
- Pada DNS Server (Heiter)
  Tambahkan forwarder pada file ``/etc/bind/named.conf.options``
  ```bash
  options {
        directory "/var/cache/bind";

    forwarders {
            192.168.122.1;
    	};

        allow-query{any;};
        auth-nxdomain no;       # conform to RFC1035
        listen-on-v6 { any; };
    };
  ```
  lakukan restart
  ```bash
  service bind9 restart
  ```
- Pada DHCP Relay (Aura)
  lakukan instalasi dhcp relay
  ```bash
  apt-get update
  apt-get install isc-dhcp-relay -y
  service isc-dhcp-relay start
  ```
  ubah file ``/etc/default/isc-dhcp-relay``
  ```bash
  SERVERS="192.193.1.1"  
  INTERFACES="eth1 eth2 eth3 eth4"
  OPTIONS=""
  ```
  ubah file ``/etc/sysctl.conf``
  ```bash
  net.ipv4.ip_forward=1
  ```
  lakukan restart
  ```bash
  service isc-dhcp-relay restart
  ```
- Pada DHCP Server (Himmel)
  lakukan instalasi dhcp server
  ```bash
  echo 'nameserver 192.168.122.1' > /etc/resolv.conf
  apt-get update
  apt-get install isc-dhcp-server -y
  dhcpd --version
  ``` 
  ubah isi file ``/etc/default/isc-dhcp-server``
  ```bash
  INTERFACESv4="eth0" #koneksi ke DHCP Relay
  ```
  Tetapkan ip pada worker laravel, worker php, Database Server, dan Load Balancer
  ubah file ``/etc/dhcp/dhcpd.conf``
  ```bash
  host Eisen {
      hardware ethernet 8e:a6:94:5f:53:dd;
      fixed-address 192.193.2.2;
  }

  host Denken {
      hardware ethernet 7e:9a:d3:59:dd:86;
      fixed-address 192.193.2.1;
  }

  host Frieren {
      hardware ethernet de:a4:41:c3:6b:41;
      fixed-address 192.193.4.1;
  }

  host Flamme {
      hardware ethernet 06:8b:19:f6:a1:50;
      fixed-address 192.193.4.2;
  }

  host Fren {
      hardware ethernet 3a:a6:66:cd:b9:5c;
      fixed-address 192.193.4.3;
  }

  host Lawine {
      hardware ethernet 36:1d:54:44:fd:94;
      fixed-address 192.193.3.1;
  }

  host Linie {
      hardware ethernet 42:d3:fc:54:58:55;
      fixed-address 192.193.3.2;
  }

  host Lugner {
      hardware ethernet 2a:a0:c8:ac:90:f1;
      fixed-address 192.193.3.3;
  }
  ```
## Nomor 2, 3, 4, & 5
### Soal
Client yang melalui Switch3 mendapatkan range IP dari 192.193.3.16 - 192.193.3.32 dan 192.193.3.64 - 192.193.3.80 (2)
Client yang melalui Switch4 mendapatkan range IP dari 192.193.4.12 - 192.193.4.20 dan 192.193.4.160 - 192.193.4.168 (3)
Client mendapatkan DNS dari Heiter dan dapat terhubung dengan internet melalui DNS tersebut (4)
Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch3 selama 3 menit sedangkan pada client yang melalui Switch4 selama 12 menit. Dengan waktu maksimal dialokasikan untuk peminjaman alamat IP selama 96 menit (5)
### Jawaban
- DHCP Server (Himmel)
  Pada soal diminta menentukan range IP pada Client yang melalui Switch3 dan Switch4
  ubah file ``/etc/dhcp/dhcpd.conf`` sesuai ketentuan pada soal
  ```bash
  subnet 192.193.1.0 netmask 255.255.255.0 {
  }
  subnet 192.193.2.0 netmask 255.255.255.0 {
      option routers 192.193.2.0;
      option broadcast-address 192.193.2.255;
      option domain-name-servers 192.193.1.2;
  }
  subnet 192.193.3.0 netmask 255.255.255.0 {
      range 192.193.3.16 192.193.3.32;
      range 192.193.3.64 192.193.3.80;
      option routers 192.193.3.0;
      option broadcast-address 192.193.3.255;
      option domain-name-servers 192.193.1.2;
      default-lease-time 180;
      max-lease-time 5760;
  }
  subnet 192.193.4.0 netmask 255.255.255.0 {
      range 192.193.4.12 192.193.4.20;
      range 192.193.4.160 192.193.4.168;
      option routers 192.193.4.0;
      option broadcast-address 192.193.4.255;
      option domain-name-servers 192.193.1.2;
      default-lease-time 720;
      max-lease-time 5760;
  }
  ``` 
  lakukan retart
  ```bash
  service isc-dhcp-server restart
  ```
## Nomor 6
### Soal
Pada masing-masing worker PHP, lakukan konfigurasi virtual host untuk website berikut dengan menggunakan php 7.3. (6)
### Jawaban
Pada php worker (Lawine, Linie, & Lugner), set ``resolv.conf`` pada alamat ip dns ``192.168.122.1``. Download website archieve menggunakan ``wget``, lalu simpan di ``/var/www/``. unzip folder dan hapus folder zip. ubah file ``resolv.conf`` pada alamat ip dns server (heiter) ``192.193.1.1``. Lakukan konfigurasi virtual host untuk nginx
```bash
apt-get update
apt-get install nginx -y
apt-get install php php-fpm -y
apt-get install wget -y
apt-get install unzip -y

mkdir /var/www/granz.channel.D04


wget -O /var/www/granz.channel.D04/granz.channel.yyy.com "https://drive.google.com/u/0/uc?id=1ViSkRq7SmwZgdK64eRbr5Fm1EGCTPrU1&export=download"

unzip -d /var/www/granz.channel.D04 /var/www/granz.channel.D04/granz.channel.yyy.com && rm /var/www/granz.channel.D04/granz.channel.yyy.com

mv /var/www/granz.channel.D04/modul-3/css /var/www/granz.channel.D04
mv /var/www/granz.channel.D04/modul-3/js /var/www/granz.channel.D04
mv /var/www/granz.channel.D04/modul-3/index.php /var/www/granz.channel.D04
mv /var/www/granz.channel.D04/modul-3/info.php /var/www/granz.channel.D04

rm -r /var/www/granz.channel.D04/modul-3

echo '
 server {

 	listen 80; 

 	root /var/www/granz.channel.D04;

 	index index.php index.html index.htm;
 	server_name granz.channel.D04.com;

 	location / {
 			try_files $uri $uri/ /index.php?$query_string;
 	}

 	# pass PHP scripts to FastCGI server
 	location ~ \.php$ {
 	include snippets/fastcgi-php.conf;
 	fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
 	}

 location ~ /\.ht {
 			deny all;
 	}

 	error_log /var/log/nginx/granz.channel.D04_error.log;
 	access_log /var/log/nginx/granz.channel.D04_access.log;
 }
' > /etc/nginx/sites-available/granz.channel.D04

ln -s /etc/nginx/sites-available/granz.channel.D04 /etc/nginx/sites-enabled
rm -rf /etc/nginx/sites-enabled/default
service nginx restart
service php7.3-fpm start
apt-get install lynx
```
Testing
```bash
lynx http://192.193.3.1
# lawine
```
<img width="217" alt="image" src="https://github.com/nadiramlha/Jarkom-Modul-3-D04-2023/assets/88009253/df58d4c9-2836-4374-b393-d3deed67ad64">

## Nomor 7
### Soal
Kepala suku dari Bredt Region memberikan resource server sebagai berikut:
Lawine, 4GB, 2vCPU, dan 80 GB SSD.
Linie, 2GB, 2vCPU, dan 50 GB SSD.
Lugner 1GB, 1vCPU, dan 25 GB SSD.
aturlah agar Eisen dapat bekerja dengan maksimal, lalu lakukan testing dengan 1000 request dan 100 request/second. (7)
### Jawaban
Konfigurasi load balancer menggunakan metode round robin, menetapkan ip worker (Lawine, Linie, Lugner) dalam grup upstream yang akan digunakan oleh load balancer. Load balancer listen port 80 dan memproses permintaan ``granz.channel.D04.com``. Melakukan restart nginx
```bash
echo 'nameserver 192.168.122.1' > /etc/resolv.conf

apt-get update
apt-get install nginx -y

echo '
upstream backend  {
 	server 192.193.3.1; #IP Lawine
 	server 192.193.3.2; #IP Linie
	server 192.193.3.3; #IP Lugner
}

server {
 	listen 80;
 	server_name granz.channel.D04.com;

 	location / {
	    proxy_pass http://backend;
            proxy_set_header    X-Real-IP $remote_addr;
            proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header    Host $http_host;

            auth_basic "Administrator'/'''s Area";
            auth_basic_user_file /etc/nginx/.htpasswd;
 	}
	location ~ /\.ht {
            deny all;
        }

	error_log /var/log/nginx/lb_error.log;
	access_log /var/log/nginx/lb_access.log;
}
' > /etc/nginx/sites-available/lb-jarkom

ln -s /etc/nginx/sites-available/lb-jarkom /etc/nginx/sites-enabled
rm -rf /etc/nginx/sites-enabled/default
service nginx restart
```
## Nomor 8 & 9
### Soal
Karena diminta untuk menuliskan grimoire, buatlah analisis hasil testing dengan 200 request dan 10 request/second masing-masing algoritma Load Balancer dengan ketentuan sebagai berikut:
Nama Algoritma Load Balancer
Report hasil testing pada Apache Benchmark
Grafik request per second untuk masing masing algoritma. 
Analisis (8)
Dengan menggunakan algoritma Round Robin, lakukan testing dengan menggunakan 3 worker, 2 worker, dan 1 worker sebanyak 100 request dengan 10 request/second, kemudian tambahkan grafiknya pada grimoire. (9)
### Jawaban
Jawaban terdapat pada link berikut https://drive.google.com/file/d/1UyDPcKpouqoNJDJaighqwxBuh7om04Ic/view?usp=sharing

## No 10 
Selanjutnya coba tambahkan konfigurasi autentikasi di LB dengan dengan kombinasi username: “netics” dan password: “ajkyyy”, dengan yyy merupakan kode kelompok. Terakhir simpan file “htpasswd” nya di /etc/nginx/rahasisakita/
#### Penyelesaian
```bash
apt-get install apache2-utils - y

# htpasswd -c /etc/nginx/.htpasswd netics (di terminal)

mkdir /etc/nginx/rahasisakita/

echo '
netics:$apr1$RFEsAAJ2$YyXVPV/f9MKTgzBBB5RI00 (kode didalam /etc/nginx/.htpasswd)
' > /etc/nginx/rahasisakita/.htpasswd
```
Pada Load balancer (Eisen) pertama install apache2 utils. Setelah itu lakukan perintah htpasswd pada file .htpasswd di terminal untuk memasukkan username dan password. Setelah itu buka kode didalam /etc/nginx/.
htpasswd dan copy lalu masukkan ke .htpasswd di direktori rahasisakita yang telah dibuat menggunakan mkdir.
Lalu agar password dapat digunakan tambahkan:
```bash
auth_basic "Administrator'/'''s Area";
auth_basic_user_file /etc/nginx/.htpasswd;
location ~ /\.ht {
            deny all;
}
```
kedalam server location.

## No 11
Lalu buat untuk setiap request yang mengandung /its akan di proxy passing menuju halaman https://www.its.ac.id. (11) hint: (proxy_pass)

#### Penyelesaian
```bash
location /its {
        	proxy_pass https://www.its.ac.id;
	        auth_basic "Administrator'/'''s Area";
            auth_basic_user_file /etc/nginx/.htpasswd;
    	}
```
Buat lokasi baru pada server yang mengarah ke www.its.ac.id menggunakan proxy_pass, lalu tambahkan juga auth untuk registrasinya.

## No 12
Selanjutnya LB ini hanya boleh diakses oleh client dengan IP [Prefix IP].3.69, [Prefix IP].3.70, [Prefix IP].4.167, dan [Prefix IP].4.168. (12) hint: (fixed in dulu clinetnya)
#### Penyelesaian
```bash
    allow 192.193.3.69; 
	allow 192.193.3.70; 
	allow 192.193.4.167;
	allow 192.193.4.168;
    deny all;
```
tambahkan allow ke Ip tersebut di masing masing location pada server lalu deny all, agar hanya memperbolehkan IP tersebut yang dapat mengakses Lb ini.

## No 13
Semua data yang diperlukan, diatur pada Denken dan harus dapat diakses oleh Frieren, Flamme, dan Fern.
#### Penyelesaian
```bash
apt-get update
apt-get install mariadb-server -y
service mysql start

mysql <<EOF
CREATE USER 'kelompokD04'@'%' IDENTIFIED BY 'passwordD04';
CREATE USER 'kelompokD04'@'localhost' IDENTIFIED BY 'passwordD04';
CREATE DATABASE dbkelompokD04;
GRANT ALL PRIVILEGES ON *.* TO 'kelompokD04'@'%';
GRANT ALL PRIVILEGES ON *.* TO 'kelompokD04'@'localhost';
FLUSH PRIVILEGES;
EOF
```
Pada Denken, pertama install mariadb dan start mysql. Setelah itu lakukan perintah create user untuk menambah user baru dan berikan semua akses ke user tersebut.
```bash
echo '
[client-server]

!includedir /etc/mysql/conf.d/
!includedir /etc/mysql/mariadb.conf.d/

[mysqld]
skip-networking=0
skip-bind-address
' > /etc/mysql/my.cnf

echo '
[server]

[mysqld]

user                    = mysql
pid-file                = /run/mysqld/mysqld.pid
socket                  = /run/mysqld/mysqld.sock
#port                   = 3306
basedir                 = /usr
datadir                 = /var/lib/mysql
tmpdir                  = /tmp
lc-messages-dir         = /usr/share/mysql
#skip-external-locking

# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
bind-address            = 0.0.0.0

query_cache_size        = 16M

log_error = /var/log/mysql/error.log

expire_logs_days        = 10

character-set-server  = utf8mb4
collation-server      = utf8mb4_general_ci

[embedded]

[mariadb]

[mariadb-10.3]
' > /etc/mysql/mariadb.conf.d/50-server.cnf

service mysql restart
```
Lalu konfigurasi server MySQL dengan mengubah file my.cnf dan 50-server.cnf, ubah bind address menjadi 0.0.0.0

## No 14
Frieren, Flamme, dan Fern memiliki Riegel Channel sesuai dengan quest guide berikut. Jangan lupa melakukan instalasi PHP8.0 dan Composer (14)
#### Penyelesaian
Pada masing masing worker lakukan hal berikut :
Lakukan pembaruan paket, instalasi MariaDB client, pemasangan paket pendukung PHP dan Nginx, konfigurasi repositori PHP, instalasi PHP 8.0 dan modulnya, pemasangan Nginx, pengunduhan dan pengaturan Composer, instalasi Git, serta setup proyek Laravel dari repositori GitHub. Selain itu, skrip ini menyelesaikan instalasi dependensi Laravel menggunakan Composer.
```bash
apt-get update
apt-get install mariadb-client -y

apt-get install -y lsb-release ca-certificates apt-transport-https software-properties-common gnupg2

curl -sSLo /usr/share/keyrings/deb.sury.org-php.gpg https://packages.sury.org/php/apt.gpg

sh -c 'echo "deb [signed-by=/usr/share/keyrings/deb.sury.org-php.gpg] https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list'

apt-get update

apt-get install php8.0-mbstring php8.0-xml php8.0-cli php8.0-common php8.0-intl php8.0-opcache php8.0-readline php8.0-mysql php8.0-fpm php8.0-curl unzip wget -y

apt-get install nginx -y

wget https://getcomposer.org/download/2.0.13/composer.phar

chmod +x composer.phar

mv composer.phar /usr/bin/composer

apt-get install --reinstall tzdata

apt-get install git -y

cd /var/www

git clone https://github.com/martuafernando/laravel-praktikum-jarkom.git

cd laravel-praktikum-jarkom

composer update
composer install
```
Lalu pada file .env ubah host, database, username, dan password sesuai dengan database yang telah dibuat di denken. 
```bash
cd /var/www/laravel-praktikum-jarkom
cp .env.example .env

echo '
APP_NAME=Laravel
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost

LOG_CHANNEL=stack
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=192.193.2.1
DB_PORT=3306
DB_DATABASE=dbkelompokD04
DB_USERNAME=kelompokD04
DB_PASSWORD=passwordD04

BROADCAST_DRIVER=log
CACHE_DRIVER=file
FILESYSTEM_DISK=local
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120

MEMCACHED_HOST=127.0.0.1

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS="hello@example.com"
MAIL_FROM_NAME="${APP_NAME}"

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=
AWS_USE_PATH_STYLE_ENDPOINT=false

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
PUSHER_HOST=
PUSHER_PORT=443
PUSHER_SCHEME=https
PUSHER_APP_CLUSTER=mt1

VITE_PUSHER_APP_KEY="${PUSHER_APP_KEY}"
VITE_PUSHER_HOST="${PUSHER_HOST}"
VITE_PUSHER_PORT="${PUSHER_PORT}"
VITE_PUSHER_SCHEME="${PUSHER_SCHEME}"
VITE_PUSHER_APP_CLUSTER="${PUSHER_APP_CLUSTER}"
' > .env


php artisan key:generate
php artisan config:cache
php artisan migrate:fresh
php artisan db:seed
php artisan storage:link
php artisan jwt:secret
php artisan config:clear
```

## No 15, 16, 17
Riegel Channel memiliki beberapa endpoint yang harus ditesting sebanyak 100 request dengan 10 request/second. Tambahkan response dan hasil testing pada grimoire.
POST /auth/register (15)
POST /auth/login (16)
GET /me (17)

#### Penyelesaian
```bash
apt-get install apache2-utils -y
apt-get install jq -y
```
Pertama pada client install apache2 utils dan jq.
```bash
cd ~
echo '
{
        "username": "kelompokD04",
        "password": "passwordD04"
}
' > register.json
```
Setelah itu baut file json yang akan digunakan untuk melakukan register.
Setelah itu lakukan perintah berikut pada terminal untuk melakukan register:
```bash
ab -n 100 -c 10 -p register.json -T application/json http://192.193.4.1:8001/api/auth/register
```
Setelah itu lakukan perintah berikut pada terminal untuk melakukan login:
```bash
ab -n 100 -c 10 -p register.json -T application/json http://192.193.4.1:8001/api/auth/login
```
setelah itu laku
```bash
curl -X POST -H "Content-Type: application/json" -d @register.json http://192.193.4.1:8001/api/auth/login > login.txt
token=$(cat login.txt | jq -r '.token')
ab -n 100 -c 10 -H "Authorization: Bearer $token" http://192.193.4.1:8001/api/me
```
Lakukan registrasi pengguna dengan mengirimkan data dari file JSON menggunakan cURL. Setelah itu, skrip mengekstrak token dari respons dan menyimpannya dalam variabel. Selanjutnya, menggunakan Apache Benchmark (ab), skrip melakukan pengujian beban dengan mengirimkan 100 permintaan secara bersamaan dengan token otentikasi ke endpoint tertentu pada alamat IP 192.193.4.1 dan port 8001. Hasil pengujian beban, termasuk waktu rata-rata per permintaan dan statistik koneksi, ditampilkan sebagai output.

## No 18
Untuk memastikan ketiganya bekerja sama secara adil untuk mengatur Riegel Channel maka implementasikan Proxy Bind pada Eisen untuk mengaitkan IP dari Frieren, Flamme, dan Fern. (18)
#### Penyelesaian
buat load balancer baru untuk laravel dan berikan port nya kemasing masing worker.
```bash
# LB untuk LARAVEL
echo '
upstream backend-laravel  {
 	server 192.193.4.1:8001; #IP Frieren
 	server 192.193.4.2:8002; #IP Flamme
	server 192.193.4.3:8003; #IP Fern
}
```
buat server baru untuk riegel.canyon yang mengarah ke load balancer. Lalu masukkan ke file load balancer baru dengan nama lb-riegel.
```bash
server {
 	listen 81;
 	server_name riegel.canyon.D04.com;

 	location / {
	    proxy_bind 192.193.2.2;	
	
	    proxy_pass http://backend-laravel;
            proxy_set_header    X-Real-IP $remote_addr;
            proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header    Host $http_host;
 	}

	error_log /var/log/nginx/lb_riegel_error.log;
	access_log /var/log/nginx/lb_riegel_access.log;
}
' > /etc/nginx/sites-available/lb-riegel

ln -s /etc/nginx/sites-available/lb-riegel /etc/nginx/sites-enabled
rm -rf /etc/nginx/sites-enabled/default
service nginx restart
```
Lalu lakukan ln, rm default dan restart nginx.

Lalu pada masing2 worker lakukan pembuatan server baru dengan listen yang sesuai dengan yang ada di load balancer.
```bash
echo '
server {

    listen 8001;

    root /var/www/laravel-praktikum-jarkom/public;

    index index.php index.html index.htm;
    server_name riegel.canyon.D04.com;

    location / {
            try_files $uri $uri/ /index.php?$query_string;
    }

    # pass PHP scripts to FastCGI server
    location ~ \.php$ {
    	include snippets/fastcgi-php.conf;
    	fastcgi_pass unix:/var/run/php/php8.0-fpm.sock;
    }

    location ~ /\.ht {
            deny all;
    }

    error_log /var/log/nginx/implementasi_error.log;
    access_log /var/log/nginx/implementasi_access.log;
}
' > /etc/nginx/sites-available/riegel.canyon.D04

ln -s /etc/nginx/sites-available/riegel.canyon.D04 /etc/nginx/sites-enabled/
rm -rf /etc/nginx/sites-enabled/default
chown -R www-data.www-data /var/www/laravel-praktikum-jarkom/storage
service php8.0-fpm start

apt-get install lynx -y
service nginx restart
```

### No 19
Untuk meningkatkan performa dari Worker, coba implementasikan PHP-FPM pada Frieren, Flamme, dan Fern. Untuk testing kinerja naikkan 
- pm.max_children
- pm.start_servers
- pm.min_spare_servers
- pm.max_spare_servers
sebanyak tiga percobaan dan lakukan testing sebanyak 100 request dengan 10 request/second kemudian berikan hasil analisisnya pada Grimoire.(19)
#### Penyelesaian
```bash
echo '
[www]
user = www-data
group = www-data
listen = /run/php/php8.0-fpm.sock
listen.owner = www-data
listen.group = www-data
php_admin_value[disable_functions] = exec,passthru,shell_exec,system
php_admin_flag[allow_url_fopen] = off

; Choose how the process manager will control the number of child processes.

pm = dynamic
pm.max_children = 10
pm.start_servers = 3
pm.min_spare_servers = 1
pm.max_spare_servers = 5
' > /etc/php/8.0/fpm/pool.d/www.conf

service php8.0-fpm restart

```
pada file /etc/php/8.0/fpm/pool.d/www.conf di masing masing worker laravel ubah pm.max_children, pm.start_servers, pm.min_spare_servers, pm.max_spare_servers


## No 20
Nampaknya hanya menggunakan PHP-FPM tidak cukup untuk meningkatkan performa dari worker maka implementasikan Least-Conn pada Eisen. Untuk testing kinerja dari worker tersebut dilakukan sebanyak 100 request dengan 10 request/second. (20)
#### Penyelesaian
Pada upstream tambahkan least_conn untuk mengimplementasikan least_conn.
```bash
upstream backend-laravel  {
	least_conn;
 	server 192.193.4.1:8001; #IP Frieren
 	server 192.193.4.2:8002; #IP Flamme
	server 192.193.4.3:8003; #IP Fern
}
```
