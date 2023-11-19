# Jarkom-Modul-3-D04-2023
# Anggota Kelompok
|                Nama                |    NRP     |
| :--------------------------------: | :--------: |
|       Muhammad Rafi Sutrisno       | 5025211167 |
|      Nadira Milha Nailul Fath      | 5025211253 |
## Topologi
<img width="348" alt="Screenshot 2023-11-19 151156" src="https://github.com/nadiramlha/Jarkom-Modul-3-D04-2023/assets/88009253/ccee91f5-a45c-4142-a932-d79addecfa8f">

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


### No 10 
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

### No 11
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

### No 12
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

### No 13
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

### No 14
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

### No 15, 16, 17
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

### No 18
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


### No 20
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