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
Untuk memperlancar komunikasi Luffy dan rekannya, dibuatkan subdomain melalui Franky dengan nama `general.mecha.frank.yyy.com` dengan alias `www.general.mecha.franky.yyy.com` yang mengarah ke Skypie.

### Jawaban
**Pada Water7**
- Edit file `/etc/bind/sunnygo/mecha.franky.B09.com` seperti pada gambar berikut:

  ![07-01](https://user-images.githubusercontent.com/31863229/138610375-671877c7-1fac-48be-ae53-e183062fde0b.PNG)
- Restart bind9.

**Pada Loguetown**
- Lakukan ping domain `general.mecha.franky.B09.com`.

  ![07-02](https://user-images.githubusercontent.com/31863229/138610376-36e4f097-cddd-4a9a-a5ad-222803544258.PNG)

## Soal 8


## Soal 9


## Soal 10


## Soal 11


## Soal 12


## Soal 13


## Soal 14


## Soal 15


## Soal 16


## Soal 17


## Kendala
1. Kesulitan untuk memberikan alias pada subdomain pada soal 3 dan 7.

## Pembagian Tugas
|Nama                   |Soal   |
|:---------------------:|:-----:|
|nama|soal|
|nama|soal|
|nama|soal|
