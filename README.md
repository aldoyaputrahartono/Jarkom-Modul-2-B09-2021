# Jarkom-Modul-2-B09-2021

Repositori Praktikum Jarkom Modul 2

|NRP           |Nama                   |
|:------------:|:---------------------:|
|05111940000084|Aldo Yaputra Hartono   |
|05111940000092|Maximilian H M Lingga  |
|05111840000023|Izzulhaq Fawwaz Syauqiy|

## Soal 1
EniesLobby akan dijadikan sebagai DNS Master, Water7 akan dijadikan DNS Slave, dan Skypie akan digunakan sebagai Web Server. Terdapat 2 Client yaitu Loguetown, dan Alabasta. Semua node terhubung pada router Foosha, sehingga dapat mengakses internet.

### Jawaban

Membuat topologi sebagai berikut:

![01-01](https://user-images.githubusercontent.com/31863229/138602120-ba27cff0-326a-4a12-8baf-6cb9476e475d.PNG)

Lakukan setting network masing-masing node dengan fitur `Edit network configuration` dengan setting sebagai berikut:

**Foosha (sebagai Router)**
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.181.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.181.2.1
	netmask 255.255.255.0
```

**Loguetown (sebagai Client)**
```
auto eth0
iface eth0 inet static
	address 192.181.1.2
	netmask 255.255.255.0
	gateway 192.181.1.1
```

**Alabasta (sebagai Client)**
```
auto eth0
iface eth0 inet static
	address 192.181.1.3
	netmask 255.255.255.0
	gateway 192.181.1.1
```

**EniesLobby (sebagai DNS Master)**
```
auto eth0
iface eth0 inet static
	address 192.181.2.2
	netmask 255.255.255.0
	gateway 192.181.2.1
```

**Water7 (sebagai DNS Slave)**
```
auto eth0
iface eth0 inet static
	address 192.181.2.3
	netmask 255.255.255.0
	gateway 192.181.2.1
```

**Skypie (sebagai Web Server)**
```
auto eth0
iface eth0 inet static
	address 192.181.2.4
	netmask 255.255.255.0
	gateway 192.181.2.1
```

Ketikkan `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.181.0.0/16` pada router `Foosha`.

Ketikkan `echo nameserver 192.168.122.1 > /etc/resolv.conf` pada node ubuntu yang lain.

Restart semua node dan coba `ping google.com`. Berikut bukti `Loguetown` dapat mengakses internet.

![01-02](https://user-images.githubusercontent.com/31863229/138602128-fdeaf005-5b76-4cbf-b324-8701cba646af.PNG)

## Soal 2
Luffy ingin menghubungi Franky yang berada di `EniesLobby` dengan denden mushi. Kalian diminta Luffy untuk membuat website utama dengan mengakses `franky.yyy.com` dengan alias `www.franky.yyy.com` pada folder kaizoku.

### Jawaban

**Pada EniesLobby**
- Install aplikasi bind9.

  ```
  apt-get install bind9 -y
  ```
- Edit file `/etc/bind/named.conf.local` seperti pada gambar berikut:

  ![02-01](https://user-images.githubusercontent.com/31863229/138604972-862e5f21-579f-497b-88d8-a89c397ccc23.PNG)
- Buat folder `kaizoku` di dalam `/etc/bind`.

  ```
  mkdir /etc/bind/kaizoku
  ```
- Copykan file `db.local` pada path `/etc/bind` ke dalam folder `kaizoku` yang baru saja dibuat dan ubah namanya menjadi `franky.B09.com`.

  ```
  cp /etc/bind/db.local /etc/bind/kaizoku/franky.B09.com
  ```
- Edit file `/etc/bind/kaizoku/franky.B09.com` seperti pada gambar berikut:

  ![02-02](https://user-images.githubusercontent.com/31863229/138604974-f22d570b-8983-4057-b538-99a0b1eae771.PNG)
- Restart bind9.

  ```
  service bind9 restart
  ```

**Pada Loguetown**
- Edit file `/etc/resolv.conf` seperti pada gambar berikut:

  ![02-03](https://user-images.githubusercontent.com/31863229/138604976-c91c2357-e94c-4f4c-bd8e-9688a03cbd93.PNG)
- Lakukan ping domain `franky.B09.com` dan `www.franky.B09.com`.

  ![02-04](https://user-images.githubusercontent.com/31863229/138604968-bf89f8dc-6855-4aec-a54f-d51bd2aa9aa8.PNG)

## Soal 3
Setelah itu buat subdomain `super.franky.yyy.com` dengan alias `www.super.franky.yyy.com` yang diatur DNS nya di EniesLobby dan mengarah ke Skypie.

### Jawaban
**Pada EniesLobby**
- Edit file `/etc/bind/kaizoku/franky.B09.com` seperti pada gambar berikut:

  ![03-01](https://user-images.githubusercontent.com/31863229/138877763-578dbcc3-0da9-4f7c-a5eb-f9e16f70a017.PNG)
- Restart bind9.

  ```
  service bind9 restart
  ```

**Pada Loguetown**
- Lakukan ping domain `super.franky.B09.com` dan `www.super.franky.B09.com`.

  ![03-02](https://user-images.githubusercontent.com/31863229/138876207-01bbe761-be2c-444c-b8fd-2c619a6c79ea.PNG)

## Soal 4
Buat juga reverse domain untuk domain utama.

### Jawaban
**Pada EniesLobby**
- Edit file `/etc/bind/named.conf.local` seperti pada gambar berikut:

  ![04-01](https://user-images.githubusercontent.com/31863229/138608215-9c81c5c6-841d-4ee6-ac97-c9aeac83c857.PNG)
- Copykan file `db.local` pada path `/etc/bind` ke dalam folder `kaizoku` yang baru saja dibuat dan ubah namanya menjadi `2.181.192.in-addr.arpa`.

  ```
  cp /etc/bind/db.local /etc/bind/kaizoku/2.181.192.in-addr.arpa
  ```
- Edit file `/etc/bind/kaizoku/2.181.192.in-addr.arpa` seperti pada gambar berikut:

  ![04-02](https://user-images.githubusercontent.com/31863229/138608218-aad67a21-2e26-4515-8d30-0dbcc785486b.PNG)
- Restart bind9.

  ```
  service bind9 restart
  ```

**Pada Loguetown**
- Lakukan pengecekan konfigurasi dengan perintah `host -t PTR 192.181.2.2`.

  ![04-03](https://user-images.githubusercontent.com/31863229/138608220-e9af715c-628e-45c3-b9df-8dd90118ab26.PNG)

## Soal 5
Supaya tetap bisa menghubungi Franky jika server EniesLobby rusak, maka buat Water7 sebagai DNS Slave untuk domain utama.

### Jawaban
**Pada EniesLobby**
- Edit file `/etc/bind/named.conf.local` seperti pada gambar berikut:

  ![05-01](https://user-images.githubusercontent.com/31863229/138608897-e941962a-ae1c-4ad9-9bf9-661c8a66e0e0.PNG)
- Restart bind9.

  ```
  service bind9 restart
  ```

**Pada Water7**
- Install aplikasi bind9.

  ```
  apt-get install bind9 -y
  ```
- Edit file `/etc/bind/named.conf.local` seperti pada gambar berikut:

  ![05-02](https://user-images.githubusercontent.com/31863229/138608898-a4c81747-c995-48aa-85c9-8568fc688b21.PNG)
- Restart bind9.

  ```
  service bind9 restart
  ```

**Pada Loguetown**
- Pada `Enieslobby` silahkan matikan service bind9.
  ```
  service bind9 stop
  ```
- Edit file `/etc/resolv.conf` seperti pada gambar berikut:

  ![05-03](https://user-images.githubusercontent.com/31863229/138608902-ba9806a8-549e-48c7-993d-ae9310eee1ab.PNG)
- Lakukan ping domain `franky.B09.com`.

  ![05-04](https://user-images.githubusercontent.com/31863229/138608904-4f897143-9edb-4dbe-a7cf-bd6ff15136f3.PNG)

## Soal 6
Setelah itu terdapat subdomain `mecha.franky.yyy.com` dengan alias `www.mecha.franky.yyy.com` yang didelegasikan dari EniesLobby ke Water7 dengan IP menuju ke Skypie dalam folder sunnygo.

### Jawaban
**Pada EniesLobby**
- Edit file `/etc/bind/kaizoku/franky.B09.com` seperti pada gambar berikut:

  ![06-01](https://user-images.githubusercontent.com/31863229/138877770-9fbf39f5-44d8-4fc0-aaa1-dd866d427520.PNG)
- Edit file `/etc/bind/named.conf.options` seperti pada gambar berikut:

  ![06-02](https://user-images.githubusercontent.com/31863229/138610112-b124ab94-3095-434c-ae01-60a57421e6a6.PNG)
- Edit file `/etc/bind/named.conf.local` seperti pada gambar berikut:

  ![06-03](https://user-images.githubusercontent.com/31863229/138610113-ca288dab-be46-4c48-a19c-a17fcde19478.PNG)
- Restart bind9.

  ```
  service bind9 restart
  ```

**Pada Water7**
- Edit file `/etc/bind/named.conf.options` seperti pada gambar berikut:

  ![06-04](https://user-images.githubusercontent.com/31863229/138610114-8ee223f2-c04b-4b03-b809-4093ad35d3a3.PNG)
- Edit file `/etc/bind/named.conf.local` seperti pada gambar berikut:

  ![06-05](https://user-images.githubusercontent.com/31863229/138610115-7fff082c-e675-4b64-bcfd-8ec4d1e16379.PNG)
- Buat folder `sunnygo` di dalam `/etc/bind`.

  ```
  mkdir /etc/bind/sunnygo
  ```
- Copykan file `db.local` pada path `/etc/bind` ke dalam folder `sunnygo` yang baru saja dibuat dan ubah namanya menjadi `mecha.franky.B09.com`.

  ```
  cp /etc/bind/db.local /etc/bind/sunnygo/mecha.franky.B09.com
  ```
- Edit file `/etc/bind/sunnygo/mecha.franky.B09.com` seperti pada gambar berikut:

  ![06-06](https://user-images.githubusercontent.com/31863229/138610118-db206c8e-fa3e-48a9-b985-c00fb991f75f.PNG)
- Restart bind9.

  ```
  service bind9 restart
  ```

**Pada Loguetown**
- Lakukan ping domain `mecha.franky.B09.com` dan `www.mecha.franky.B09.com`.

  ![06-07](https://user-images.githubusercontent.com/31863229/138610119-68f7a6ec-87a2-49ca-b6bc-615d3380a52d.PNG)

## Soal 7
Untuk memperlancar komunikasi Luffy dan rekannya, dibuatkan subdomain melalui Water7 dengan nama `general.mecha.franky.yyy.com` dengan alias `www.general.mecha.franky.yyy.com` yang mengarah ke Skypie.

### Jawaban
**Pada Water7**
- Edit file `/etc/bind/sunnygo/mecha.franky.B09.com` seperti pada gambar berikut:

  ![07-01](https://user-images.githubusercontent.com/31863229/138877211-1ec807bd-4a9f-4704-a23c-88264f34f51f.PNG)
- Restart bind9.

  ```
  service bind9 restart
  ```

**Pada Loguetown**
- Lakukan ping domain `general.mecha.franky.B09.com` dan `www.general.mecha.franky.B09.com`.

  ![07-02](https://user-images.githubusercontent.com/31863229/138877235-460dafea-b343-497f-b038-67c002a8b675.PNG)

## Soal 8
Setelah melakukan konfigurasi server, maka dilakukan konfigurasi Webserver. Pertama dengan webserver `www.franky.yyy.com`. Pertama, luffy membutuhkan webserver dengan DocumentRoot pada `/var/www/franky.yyy.com`.

### Jawaban
**Pada Skypie**
- Install aplikasi apache, PHP, dan libapache2-mod-php7.0.

  ```
  apt-get install apache2 -y
  apt-get install php -y
  apt-get install libapache2-mod-php7.0 -y
  ```
- Pindah ke directory `/etc/apache2/sites-available`.
- Copy file `000-default.conf` menjadi file `franky.B09.com.conf`.
- Edit file `franky.B09.com.conf` seperti pada gambar berikut:

  ![08-01](https://user-images.githubusercontent.com/31863229/138613006-f2481e4f-c1c5-4fd9-96c2-3fbc4b3ec9f1.PNG)
- Aktifkan konfigurasi franky.B09.com.

  ```
  a2ensite franky.B09.com
  ```
- Restart apache.

  ```
  service apache2 restart
  ```
- Pindah ke directory `/var/www`.
- Download file zip menggunakan `wget`.

  ```
  wget https://github.com/FeinardSlim/Praktikum-Modul-2-Jarkom/raw/main/franky.zip
  ```
- Lakukan unzip.

  ```
  unzip franky.zip
  ```
- Rename folder `franky` menjadi `franky.B09.com` dan terdapat isi file seperti pada gambar berikut:

  ![08-02](https://user-images.githubusercontent.com/31863229/138613003-0bf147e7-68cd-460d-a82d-1d12cdd02581.PNG)

**Pada Loguetown**
- Install aplikasi lynx.

  ```
  apt-get install lynx -y
  ```
- Buka `www.franky.B09.com` menggunakan lynx.

  ![08-03](https://user-images.githubusercontent.com/31863229/138613005-39722a4d-d33f-4579-a8d9-be9b2c2e29ca.PNG)

## Soal 9
Setelah itu, Luffy juga membutuhkan agar url `www.franky.yyy.com/index.php/home` dapat menjadi menjadi `www.franky.yyy.com/home`.

### Jawaban
**Pada Skypie**
- Jalankan perintah `a2enmod rewrite` untuk mengaktifkan module rewrite.
- Restart apache dengan perintah `service apache2 restart`.
- Tambahkan file baru `.htaccess` pada folder `/var/www/franky.B09.com`, di mana file tersebut akan dimodifikasi menjadi:

  ![09-01](https://user-images.githubusercontent.com/31863229/139184365-dca56d87-94fd-4d93-9a8d-17f22f677e58.PNG)
- Pindah ke directory `/etc/apache2/sites-available`.
- Edit file `franky.B09.com.conf` agar file `.htaccess` dapat berjalan seperti pada gambar berikut:

  ![09-02](https://user-images.githubusercontent.com/31863229/139184373-20cb1846-1707-4e0b-804e-2fe34e1b3d0f.PNG)
- Restart apache.

  ```
  service apache2 restart
  ```

**Pada Loguetown**
- Buka `www.franky.B09.com/home` menggunakan lynx.

  ![09-03](https://user-images.githubusercontent.com/31863229/138647857-59c7e0ac-4a6c-496d-93de-a0176037f41b.PNG)

## Soal 10
Setelah itu, pada subdomain `www.super.franky.yyy.com`, Luffy membutuhkan penyimpanan aset yang memiliki DocumentRoot pada `/var/www/super.franky.yyy.com`.

### Jawaban
**Pada Skypie**
- Pindah ke directory `/etc/apache2/sites-available`.
- Copy file `000-default.conf` menjadi file `super.franky.B09.com.conf`.
- Edit file `super.franky.B09.com.conf` seperti pada gambar berikut:

  ![10-01](https://user-images.githubusercontent.com/31863229/138649466-0ade542c-3d0f-4779-9be7-3d011a519d03.PNG)
- Aktifkan konfigurasi super.franky.B09.com.

  ```
  a2ensite super.franky.B09.com
  ```
- Restart apache.

  ```
  service apache2 restart
  ```
- Pindah ke directory `/var/www`.
- Download file zip menggunakan `wget`.

  ```
  wget https://github.com/FeinardSlim/Praktikum-Modul-2-Jarkom/raw/main/super.franky.zip
  ```
- Lakukan unzip.

  ```
  unzip super.franky.zip
  ```
- Rename folder `super.franky` menjadi `super.franky.B09.com` dan terdapat isi file seperti pada gambar berikut:

  ![10-02](https://user-images.githubusercontent.com/31863229/138649479-746c84b9-960b-4d48-808e-cf1724964842.PNG)

**Pada Loguetown**
- Buka `www.super.franky.B09.com` menggunakan lynx.

  ![10-03](https://user-images.githubusercontent.com/31863229/138649483-5120d387-c212-4fdf-b09c-9ad5f7fac4b3.PNG)

## Soal 11
Akan tetapi, pada folder `/public`, Luffy ingin hanya dapat melakukan directory listing saja.

### Jawaban
**Pada Skypie**
- Pindah ke directory `/etc/apache2/sites-available`.
- Edit file `super.franky.B09.com.conf` seperti pada gambar berikut:

  ![11-01](https://user-images.githubusercontent.com/31863229/138650989-ce3f44f6-20af-40d7-bf1e-c90483ee7029.PNG)
- Restart apache.

  ```
  service apache2 restart
  ```

**Pada Loguetown**
- Buka `www.super.franky.B09.com/public` menggunakan lynx.

  ![11-02](https://user-images.githubusercontent.com/31863229/138694312-8e9505f9-e4b9-43b2-9525-896a9b6cbe68.PNG)
- Buka `www.super.franky.B09.com/public/css`, `www.super.franky.B09.com/public/images`, dan `www.super.franky.B09.com/public/js` menggunakan lynx.

  ![11-03](https://user-images.githubusercontent.com/31863229/138651176-a247c424-a618-48aa-8c03-3461a808d9e3.PNG)

## Soal 12
Tidak hanya itu, Luffy juga menyiapkan error file `404.html` pada folder `/error` untuk mengganti error kode pada apache.

### Jawaban
**Pada Skypie**
- Pindah ke directory `/etc/apache2/sites-available`.
- Edit file `super.franky.B09.com.conf` seperti pada gambar berikut:

  ![12-01](https://user-images.githubusercontent.com/31863229/138652177-bcaeb9ad-090e-4ae2-ac0f-06fe93ebcf1e.PNG)
- Restart apache.

  ```
  service apache2 restart
  ```

**Pada Loguetown**
- Buka `www.super.franky.B09.com/publoc` (terdapat typo) menggunakan lynx.

  ![12-02](https://user-images.githubusercontent.com/31863229/138652187-10cdf3a5-14f6-4ab7-a1f9-c13ba4e520e5.PNG)

## Soal 13
Luffy juga meminta Nami untuk dibuatkan konfigurasi virtual host. Virtual host ini bertujuan untuk dapat mengakses file asset `www.super.franky.yyy.com/public/js` menjadi `www.super.franky.yyy.com/js`.

### Jawaban
**Pada Skypie**
- Pindah ke directory `/etc/apache2/sites-available`.
- Edit file `super.franky.B09.com.conf` seperti pada gambar berikut:

  ![13-01](https://user-images.githubusercontent.com/31863229/138653250-28b03028-f476-4f28-b91e-e7da9691a080.PNG)
- Restart apache.

  ```
  service apache2 restart
  ```

**Pada Loguetown**
- Buka `www.super.franky.B09.com/js` menggunakan lynx.

  ![13-02](https://user-images.githubusercontent.com/31863229/138653260-2543aacb-8623-4fc4-8790-71ca479d3061.PNG)

## Soal 14
Dan Luffy meminta untuk web `www.general.mecha.franky.yyy.com` hanya bisa diakses dengan port 15000 dan port 15500.

### Jawaban
**Pada Skypie**
- Pindah ke directory `/etc/apache2/sites-available`.
- Copy file `000-default.conf` menjadi file `general.mecha.franky.B09.com.conf`.
- Edit file `general.mecha.franky.B09.com.conf` seperti pada gambar berikut:

  ![14-01](https://user-images.githubusercontent.com/31863229/138880599-c5626403-4692-42d0-92e9-097722038014.PNG)
- Edit file `/etc/apache2/ports.conf` untuk mengaktifkan port 15000 dan port 15500 seperti pada gambar berikut:

  ![14-02](https://user-images.githubusercontent.com/31863229/138662627-16ddbfb7-56c2-4f8d-9dce-2092d8cccbd3.PNG)
- Aktifkan konfigurasi general.mecha.franky.B09.com.

  ```
  a2ensite general.mecha.franky.B09.com
  ```
- Restart apache.

  ```
  service apache2 restart
  ```
- Pindah ke directory `/var/www`.
- Download file zip menggunakan `wget`.

  ```
  wget https://github.com/FeinardSlim/Praktikum-Modul-2-Jarkom/raw/main/general.mecha.franky.zip
  ```
- Lakukan unzip.

  ```
  unzip general.mecha.franky.zip
  ```
- Rename folder `general.mecha.franky` menjadi `general.mecha.franky.B09.com` dan terdapat isi file seperti pada gambar berikut:

  ![14-03](https://user-images.githubusercontent.com/31863229/138662631-42197d6c-557d-44e8-a569-be9a59c4abd8.PNG)

**Pada Loguetown**
- Buka `www.general.mecha.franky.B09.com` menggunakan lynx.

  ![14-04](https://user-images.githubusercontent.com/31863229/138662635-4721d99a-0698-46f6-ad73-29b1dcc08a3e.PNG)
- Buka `www.general.mecha.franky.B09.com:15000` menggunakan lynx.

  ![14-05](https://user-images.githubusercontent.com/31863229/138662644-484252fc-8cc6-42c4-9977-8f52d1700ae4.PNG)
- Buka `www.general.mecha.franky.B09.com:15500` menggunakan lynx.

  ![14-06](https://user-images.githubusercontent.com/31863229/138662647-d8726d77-37e9-4059-b57b-5907dccd2f92.PNG)

## Soal 15
Dengan authentikasi username `luffy` dan password `onepiece` dan file di `/var/www/general.mecha.franky.yyy`.

### Jawaban
**Pada Skypie**
- Pindah ke directory `/etc/apache2/sites-available`.
- Edit file `general.mecha.franky.B09.com.conf` seperti pada gambar berikut:

  ![15-01](https://user-images.githubusercontent.com/31863229/138665511-c995a6bb-88c3-4d15-99de-91cc18c79e8b.PNG)
- Jalankan perintah berikut untuk membuat akun autentikasi baru dengan username `luffy`. Kita akan diminta untuk memasukkan password baru dan confirm password tersebut diisi `onepiece`.

  ```
  htpasswd -c /etc/apache2/.htpasswd luffy
  ```
- Restart apache.

  ```
  service apache2 restart
  ```

**Pada Loguetown**
- Buka `www.general.mecha.franky.B09.com:15000` menggunakan lynx.

  ![15-02](https://user-images.githubusercontent.com/31863229/138665528-85ea4f7c-2b62-4f83-9e70-bfc165c2df44.PNG)
  ![15-03](https://user-images.githubusercontent.com/31863229/138665530-6ae720ac-15c8-472c-9f10-d55f3cb3f080.PNG)
  ![15-04](https://user-images.githubusercontent.com/31863229/138665534-db4cce14-77ae-4d97-b297-14c06a19dbc7.PNG)

## Soal 16
Dan setiap kali mengakses IP Skypie akan diahlikan secara otomatis ke `www.franky.yyy.com`.

### Jawaban
**Pada Skypie**
- Pindah ke directory `/etc/apache2/sites-available`.
- Edit file `000-default.conf` seperti pada gambar berikut:

  ![16-01](https://user-images.githubusercontent.com/31863229/138668608-5cbdd2ce-6804-4239-adec-4579bed6db13.PNG)
- Restart apache.

  ```
  service apache2 restart
  ```

**Pada Loguetown**
- Buka `192.181.2.4` (IP Skypie) menggunakan lynx.

  ![16-02](https://user-images.githubusercontent.com/31863229/138668611-6fe34a91-9581-46c2-8a53-6f7e05155cf9.PNG)

## Soal 17
Dikarenakan Franky juga ingin mengajak temannya untuk dapat menghubunginya melalui website `www.super.franky.yyy.com`, dan dikarenakan pengunjung web server pasti akan bingung dengan randomnya images yang ada, maka Franky juga meminta untuk mengganti request gambar yang memiliki substring `franky` akan diarahkan menuju `franky.png`.

### Jawaban
**Pada Skypie**
- Jalankan perintah `a2enmod rewrite` untuk mengaktifkan module rewrite.
- Restart apache dengan perintah `service apache2 restart`.
- Tambahkan file baru `.htaccess` pada folder `/var/www/super.franky.B09.com`, di mana file tersebut akan dimodifikasi menjadi:

  ![17-01](https://user-images.githubusercontent.com/31863229/138675514-f6ac8976-a897-483c-91ec-694c0ab376f1.PNG)
- Pindah ke directory `/etc/apache2/sites-available`.
- Edit file `super.franky.B09.com.conf` agar file `.htaccess` dapat berjalan seperti pada gambar berikut:

  ![17-02](https://user-images.githubusercontent.com/31863229/138675524-9fab5fb6-3991-4654-8beb-5ae8ac2d4623.PNG)
- Restart apache.

  ```
  service apache2 restart
  ```

**Pada Loguetown**
- Buka `www.super.franky.B09.com/public/images/franky.png` menggunakan lynx.

  ![17-03](https://user-images.githubusercontent.com/31863229/138675528-40995ec7-560a-4d9e-b2b9-b05ee3b3f64d.PNG)
- Buka `www.super.franky.B09.com/public/images/eyeoffranky.jpg` menggunakan lynx.

  ![17-04](https://user-images.githubusercontent.com/31863229/138675531-b28c94e8-656f-46d4-a759-28ba179577a5.PNG)
- Buka `www.super.franky.B09.com/public/images/background-frank.jpg` menggunakan lynx.

  ![17-05](https://user-images.githubusercontent.com/31863229/138675533-1e50bd51-09ed-4d6c-9ec8-d15651521052.PNG)

## Kendala
1. Sedikit kesulitan untuk memberikan alias pada subdomain pada soal 3 dan 7.
2. Sedikit kesulitan pada soal 9 karena harus memakai Module Rewrite.
3. Sedikit kesulitan pada soal 17 karena aplikasi lynx tidak dapat menampilkan gambar.

## Pembagian Tugas
|Nama                   |Soal  |
|:---------------------:|:----:|
|Maximilian H M Lingga  |1 - 4 |
|Izzulhaq Fawwaz Syauqiy|5 - 7 |
|Aldo Yaputra Hartono   |8 - 17|
