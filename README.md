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

  ![03-01](https://user-images.githubusercontent.com/31863229/138607683-fc9a0ab3-0f2d-495a-95a4-190a4406d690.PNG)
- Restart bind9.

**Pada Loguetown**
- Lakukan ping domain `super.franky.B09.com`.

  ![03-02](https://user-images.githubusercontent.com/31863229/138607616-7a3e2c4f-a9fa-46e1-8869-b06c06439484.PNG)

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

**Pada Water7**
- Install aplikasi bind9.

  ```
  apt-get install bind9 -y
  ```
- Edit file `/etc/bind/named.conf.local` seperti pada gambar berikut:

  ![05-02](https://user-images.githubusercontent.com/31863229/138608898-a4c81747-c995-48aa-85c9-8568fc688b21.PNG)
- Restart bind9.

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

  ![06-01](https://user-images.githubusercontent.com/31863229/138610108-06d654ab-72af-4542-84ca-d11191b4addf.PNG)
- Edit file `/etc/bind/named.conf.options` seperti pada gambar berikut:

  ![06-02](https://user-images.githubusercontent.com/31863229/138610112-b124ab94-3095-434c-ae01-60a57421e6a6.PNG)
- Edit file `/etc/bind/named.conf.local` seperti pada gambar berikut:

  ![06-03](https://user-images.githubusercontent.com/31863229/138610113-ca288dab-be46-4c48-a19c-a17fcde19478.PNG)
- Restart bind9.

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

**Pada Loguetown**
- Lakukan ping domain `mecha.franky.B09.com` dan `www.mecha.franky.B09.com`.

  ![06-07](https://user-images.githubusercontent.com/31863229/138610119-68f7a6ec-87a2-49ca-b6bc-615d3380a52d.PNG)

## Soal 7
Untuk memperlancar komunikasi Luffy dan rekannya, dibuatkan subdomain melalui Water7 dengan nama `general.mecha.franky.yyy.com` dengan alias `www.general.mecha.franky.yyy.com` yang mengarah ke Skypie.

### Jawaban
**Pada Water7**
- Edit file `/etc/bind/sunnygo/mecha.franky.B09.com` seperti pada gambar berikut:

  ![07-01](https://user-images.githubusercontent.com/31863229/138610375-671877c7-1fac-48be-ae53-e183062fde0b.PNG)
- Restart bind9.

**Pada Loguetown**
- Lakukan ping domain `general.mecha.franky.B09.com`.

  ![07-02](https://user-images.githubusercontent.com/31863229/138610376-36e4f097-cddd-4a9a-a5ad-222803544258.PNG)

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
- Pindah ke directory `/etc/apache2/sites-available`.
- Edit file `franky.B09.com.conf` seperti pada gambar berikut:

  ![09-01](https://user-images.githubusercontent.com/31863229/138647864-b9012e2d-8002-41cf-965d-b6b19d113b4a.PNG)
- Restart apache.

  ```
  service apache2 restart
  ```

**Pada Loguetown**
- Buka `www.franky.B09.com/home` menggunakan lynx.

  ![09-02](https://user-images.githubusercontent.com/31863229/138647857-59c7e0ac-4a6c-496d-93de-a0176037f41b.PNG)

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
- Buka `super.franky.B09.com` menggunakan lynx.

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
- Buka `super.franky.B09.com` menggunakan lynx.

  ![11-02](https://user-images.githubusercontent.com/31863229/138651172-3a21aab5-5699-42f9-9871-46bbad9514b5.PNG)
- Buka `super.franky.B09.com/public/css`, `super.franky.B09.com/public/images`, `super.franky.B09.com/public/js` menggunakan lynx.

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
- Buka `super.franky.B09.com/publoc` (terdapat typo) menggunakan lynx.

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
- Buka `super.franky.B09.com/js` menggunakan lynx.

  ![13-02](https://user-images.githubusercontent.com/31863229/138653260-2543aacb-8623-4fc4-8790-71ca479d3061.PNG)

## Soal 14
Dan Luffy meminta untuk web `www.general.mecha.franky.yyy.com` hanya bisa diakses dengan port 15000 dan port 15500.

### Jawaban
**Pada Skypie**
- Pindah ke directory `/etc/apache2/sites-available`.
- Copy file `000-default.conf` menjadi file `general.mecha.franky.B09.com.conf`.
- Edit file `general.mecha.franky.B09.com.conf` seperti pada gambar berikut:

  ![14-01](https://user-images.githubusercontent.com/31863229/138662605-7d4a055c-4755-4b79-8e7d-0c71bc13f151.PNG)
  ![14-02](https://user-images.githubusercontent.com/31863229/138662623-e0b7649c-e02d-4774-88b7-a91ab14387be.PNG)
- Edit file `/etc/apache2/ports.conf` untuk mengaktifkan port 15000 dan port 15500 seperti pada gambar berikut:

  ![14-03](https://user-images.githubusercontent.com/31863229/138662627-16ddbfb7-56c2-4f8d-9dce-2092d8cccbd3.PNG)
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

  ![14-04](https://user-images.githubusercontent.com/31863229/138662631-42197d6c-557d-44e8-a569-be9a59c4abd8.PNG)

**Pada Loguetown**
- Buka `general.mecha.franky.B09.com` menggunakan lynx.

  ![14-05](https://user-images.githubusercontent.com/31863229/138662635-4721d99a-0698-46f6-ad73-29b1dcc08a3e.PNG)
- Buka `general.mecha.franky.B09.com:15000` menggunakan lynx.

  ![14-06](https://user-images.githubusercontent.com/31863229/138662644-484252fc-8cc6-42c4-9977-8f52d1700ae4.PNG)
- Buka `general.mecha.franky.B09.com:15500` menggunakan lynx.

  ![14-07](https://user-images.githubusercontent.com/31863229/138662647-d8726d77-37e9-4059-b57b-5907dccd2f92.PNG)

## Soal 15


## Soal 16


## Soal 17


## Kendala
1. Kesulitan untuk memberikan alias pada subdomain pada soal 3 dan 7.
2. Sedikit kesulitan pada pengerjaan soal Web Server karena aplikasi lynx tidak dapat menampilkan gambar.

## Pembagian Tugas
|Nama                   |Soal   |
|:---------------------:|:-----:|
|nama|soal|
|nama|soal|
|nama|soal|
