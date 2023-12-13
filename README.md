## Jarkom Modul 5 F08

    Nama : Keyisa Raihan Illah Setiadi
    NRP : 5025211002

    Nama : Syukra Wahyu Ramadhan
    NRP : 5025211037

    Kelas : JARKOM F

### A
Tugas pertama, buatlah peta wilayah sesuai berikut ini:

<img width="458" alt="image" src="https://github.com/MyNameIsSyukra/Jarkom-Modul-5-F08-2023/assets/85614845/caaae09b-9533-4497-9329-b33cfb707414">

### B
Untuk menghitung rute-rute yang diperlukan, gunakan perhitungan dengan metode VLSM. Buat juga pohonnya, dan lingkari subnet yang dilewati.

- Rute
  
  <img width="568" alt="image" src="https://github.com/MyNameIsSyukra/Jarkom-Modul-5-F08-2023/assets/85614845/142e292e-8d51-4814-8d5c-7f63162aa655">

- Tree

  ![VLSM TREE - Frame 1](https://github.com/MyNameIsSyukra/Jarkom-Modul-5-F08-2023/assets/85614845/9585f994-1180-4ce8-9b47-58d39e9a4646)

- Pembagian IP

  <img width="378" alt="image" src="https://github.com/MyNameIsSyukra/Jarkom-Modul-5-F08-2023/assets/85614845/df0a889e-3308-49af-b6f9-1e2eb77e2b2e">

### C
Kemudian buatlah rute sesuai dengan pembagian IP yang kalian lakuka

Akan dicontohkna pada Subnet dan routing di pusat `Aura`
```

# DHCP config for eth0
auto eth0
iface eth0 inet dhcp
#	hostname ubuntu-1-1

# Static config for eth1
auto eth1
iface eth1 inet static
	address 192.225.14.129
	netmask 255.255.255.252

# Static config for eth2
auto eth2
iface eth2 inet static
	address 192.225.14.133
	netmask 255.255.255.252

up route add -net 192.225.8.0 netmask 255.255.252.0 gw 192.225.14.130
up route add -net 192.225.0.0 netmask 255.255.248.0 gw 192.225.14.130

up route add -net 192.225.14.144 netmask 255.255.255.252 gw 192.225.14.134
up route add -net 192.225.14.148 netmask 255.255.255.252 gw 192.225.14.134
up route add -net 192.225.14.0 netmask 255.255.255.128 gw 192.225.14.134
up route add -net 192.225.12.0 netmask 255.255.254.0 gw 192.225.14.134
up route add -net 192.225.14.140 netmask 255.255.255.252 gw 192.225.14.134
up route add -net 192.225.14.136 netmask 255.255.255.252 gw 192.225.14.134
```
### D
Tugas berikutnya adalah memberikan ip pada subnet SchwerMountain, LaubHills, TurkRegion, dan GrobeForest menggunakan bantuan DHCP.

Kita perlu menginstall DHCP server pada `revolte`, lalu mensetting `dhcp.conf` sesuai kebutuhan DHCP subnet client kita
```
subnet 192.225.14.0 netmask 255.255.255.128 {
    range 192.225.14.4 192.225.14.126;
    option routers 192.225.14.1;
    option broadcast-address 192.225.14.127;
    default-lease-time 600;
    max-lease-time 7200;
}

subnet 192.225.12.0 netmask 255.255.254.0 {
    range 192.225.12.2 192.225.13.254;
    option routers 192.225.12.1;
    option broadcast-address 192.225.13.255;
    default-lease-time 600;
    max-lease-time 7200;
}

subnet 192.225.0.0 netmask 255.255.248.0 {
    range 192.225.0.2 192.225.7.254;
    option routers 192.225.0.1;
    option broadcast-address 192.255.7.255;
    default-lease-time 600;
    max-lease-time 7200;
}

subnet 192.225.8.0 netmask 255.255.252.0 {
    range 192.225.8.4 192.225.11.254;
    option routers 192.225.8.1;
    option broadcast-address 192.225.11.255;
    default-lease-time 600;
    max-lease-time 7200;
}
```
Lalu tidak lupa di setup untuk setiap router yaitu `dhcp-relay`

Test dapat dilakukan dengan mencoba melihat IP pada client, jika mendapatkan IP maka DHCP berhasil : 

<img width="493" alt="image" src="https://github.com/MyNameIsSyukra/Jarkom-Modul-5-F08-2023/assets/85614845/3bb388a9-039b-49bd-a0c3-b8ec157ca71f">

### No 1
Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Aura menggunakan iptables, tetapi tidak ingin menggunakan MASQUERADE.

Di dalam Aura kita perlu menambahkan scrypt berikut : 
```
ETH0_IP=$(ip -4 addr show eth0 | grep -oP '(?<=inet\s)\d+(\.\d+){3}')

iptables -t nat -A POSTROUTING -o eth0 -j SNAT --to-source $ETH0_IP
```
Lalu untuk setiap node ditambahkan `nameserver 192.168.122.1` pada `/etc/resolv.conf`

Test dapat dilakukan dengan mengping google dari node : 

<img width="446" alt="image" src="https://github.com/MyNameIsSyukra/Jarkom-Modul-5-F08-2023/assets/85614845/785879c5-75e8-40ce-8ab3-9799223f6e74">


### No 2
Kalian diminta untuk melakukan drop semua TCP dan UDP kecuali port 8080 pada TCP.

Sebagai contoh akan digunakan Client `LaubHills` , jalan kan script : 
```
iptables -A INPUT -p tcp --dport 8080 -j ACCEPT
iptables -A INPUT -p tcp -j DROP
iptables -A INPUT -p udp -s 192.225.0.0/20 -j DROP
```

Untuk mengetesnya, bisa dilakukan netcat pada port 8080 dan 80 : 

<img width="696" alt="image" src="https://github.com/MyNameIsSyukra/Jarkom-Modul-5-F08-2023/assets/85614845/4cc92dce-3d0f-4297-9914-1dd22233841d">

<img width="719" alt="image" src="https://github.com/MyNameIsSyukra/Jarkom-Modul-5-F08-2023/assets/85614845/d3d3486b-31e0-4175-ab47-5746cfdcb55a">


### No 3
Kepala Suku North Area meminta kalian untuk membatasi DHCP dan DNS Server hanya dapat dilakukan ping oleh maksimal 3 device secara bersamaan, selebihnya akan di drop.

Seabgai contoh pada `Laubhills` juga kita jalankan script : 
```
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```

Untuk mengetestnya kita perlu meng ping `LaubHills` dengan 4 macam klient, klient ke 4 akan di drop : 

<img width="959" alt="image" src="https://github.com/MyNameIsSyukra/Jarkom-Modul-5-F08-2023/assets/85614845/910d7433-d7ce-4315-8a2a-2ddb81b3e2dc">


### No 4
Lakukan pembatasan sehingga koneksi SSH pada Web Server hanya dapat dilakukan oleh masyarakat yang berada pada GrobeForest.

Pada webserver, kita coba dengan `sein` kita tambahkan script berikut : 
```
iptables -A INPUT -p tcp --dport 22 -s 192.225.8.0/22 -j ACCEPT
iptables -A INPUT -p tcp --dport 22 -j DROP
```

Test dapat dilakukan dengan Nmap dari `GroubForest` dan selain itu

<img width="926" alt="image" src="https://github.com/MyNameIsSyukra/Jarkom-Modul-5-F08-2023/assets/85614845/f767995f-9803-4133-83b7-8ba741ecff5a">

### No 5 & 6
5 Selain itu, akses menuju WebServer hanya diperbolehkan saat jam kerja yaitu Senin-Jumat pada pukul 08.00-16.00.
6 Lalu, karena ternyata terdapat beberapa waktu di mana network administrator dari WebServer tidak bisa stand by, sehingga perlu ditambahkan rule bahwa akses pada hari Senin - Kamis pada jam 12.00 - 13.00 dilarang (istirahat maksi cuy) dan akses di hari Jumat pada jam 11.00 - 13.00 juga dilarang (maklum, Jumatan rek).

Kita perlu tambahkan script pada sein :
```
iptables -F
iptables -A INPUT -p tcp --dport 22 -s 192.225.8.0/22 -m time --timestart 11:00 --timestop 13:00 --weekdays Fri -j DROP
iptables -A INPUT -p tcp --dport 22 -s 192.225.8.0/22 -m time --timestart 12:00 --timestop 13:00 --weekdays Mon,Tue,Wed,Thu -j DROP
iptables -A INPUT -p tcp --dport 22 -s 192.225.8.0/22 -m time --timestart 08:00 --timestop 16:00 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT
iptables -A INPUT -p tcp --dport 22 -j DROP
```

untuk mengetestnya kita hanya perlu melakukan nmap dari `GroubeForest` ke sein pada waktu yang di tentukan maupun tidak

<img width="458" alt="image" src="https://github.com/MyNameIsSyukra/Jarkom-Modul-5-F08-2023/assets/85614845/f1e090b3-7d2e-4c0b-ba76-f23254f5ec39">

### No 7
Karena terdapat 2 WebServer, kalian diminta agar setiap client yang mengakses Sein dengan Port 80 akan didistribusikan secara bergantian pada Sein dan Stark secara berurutan dan request dari client yang mengakses Stark dengan port 443 akan didistribusikan secara bergantian pada Sein dan Stark secara berurutan

Sebagai contoh pada router `Heiter` akan ditambahkan scritp berikut, yg kemudian akan di test dari turk region :
```
iptables -t nat -F

# Akses ke Sein port 80
iptables -A PREROUTING -t nat -p tcp --dport 80 -d 192.225.8.2 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.225.8.2
iptables -A PREROUTING -t nat -p tcp --dport 80 -d 192.225.8.2 -j DNAT --to-destination 192.225.14.138

# Akses ke Stark port 443

iptables -A PREROUTING -t nat -p tcp --dport 443 -d 192.225.14.138 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.225.8.2
iptables -A PREROUTING -t nat -p tcp --dport 443 -d 192.225.14.138 -j DNAT --to-destination 192.225.14.138
```

dari `turk region` kita akan melakukan netcat, dengan membuka stark dan sein secara terus menerus
`while true; do nc -l -p 80 -c 'echo "ini sein"'; done` : 

<img width="417" alt="image" src="https://github.com/MyNameIsSyukra/Jarkom-Modul-5-F08-2023/assets/85614845/f77281b7-6650-4b69-bbf6-c3cbdb5c1c66">

### No 8
Karena berbeda koalisi politik, maka subnet dengan masyarakat yang berada pada Revolte dilarang keras mengakses WebServer hingga masa pencoblosan pemilu kepala suku 2024 berakhir. Masa pemilu (hingga pemungutan dan penghitungan suara selesai) kepala suku bersamaan dengan masa pemilu Presiden dan Wakil Presiden Indonesia 2024.

Pada `Sein` ditambahkan script : 
```
Revolte_Subnet="192.225.14.148/30"
Pemilu_Start=$(date -d "2023-10-19T00:00" +"%Y-%m-%dT%H:%M")
Pemilu_End=$(date -d "2024-02-15T00:00" +"%Y-%m-%dT%H:%M")

# Atur waktu untuk masa pemilu
iptables -A INPUT -p tcp -s $Revolte_Subnet --dport 80 -m time --datestart "$Pemilu_Start" --datestop "$Pemilu_End" -j DROP
```
Dapat di test dengan mencoba nmap dari Revolte dan juga node lain

<img width="637" alt="image" src="https://github.com/MyNameIsSyukra/Jarkom-Modul-5-F08-2023/assets/85614845/705f8539-a328-4b07-abed-f0d77837e149">

<img width="634" alt="image" src="https://github.com/MyNameIsSyukra/Jarkom-Modul-5-F08-2023/assets/85614845/d3b85fb1-754b-4792-940c-07f2033680d5">

