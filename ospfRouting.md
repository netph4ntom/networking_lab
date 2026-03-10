## Open Shortest Path First (OSPF)
adalah interior gateway protocol (IGP) yang banyak digunakan karena non-proprietary atau bisa digunakan multi vendor. OSPF menggunakan Link-State protocol yaitu setiap router membangun peta lengkap topologi jaringan lalu menghitung jalur terbaik sendiri. Dalam pemilihan jalur OSPF menggunakan algoritma shortest path first (SPF) atau Djikstra dengan tujuan mencari jalur dengan cost paling kecil.

### Cara kerja link-state
1. Neighbor Discovery
    Router mengirim Hello packet untuk mencari tetangga, di ospf ``224.0.0.5``.
    kalau parameter cocok:
    - area sama
    - subnet sama
    - hello timer sama
    mereka jadi neighbor.
2. Adjacency
    Router membangun hubungan lebih dalam, artinya router siap bertukar databse.
    `Tidak semua neighbor jadi adjacency. Pada jaringan multi access ospf akan memilih DR, BDR, dan DROTHER.`
3. Link State Advertisement(LSA)
    Router mengirim informasi:
    - Network yang dipunya
    - link yang terhubung
    - Cost dari link
4. Flooding
    LSA tidak hanya dikirim ke satu router. Router akan flood ke semua router diarea jadi semua router mendapatkan informasi topologi yang sama. Ini disebut LSDB - Syncronization.
5. Link State Database(LSDB)
    Semua router menyimpan datbase topology, isi LSDB adalah peta jaringan lengkap.
6. SPF Calculation
    Router menjalankan algoritma SPF (Shortest Path First) atau Djikstra. Tujuannya mencari jalur dengan cost paling kecil, dan hasil perhitungannya masuk ke routing tabel.

### NOTE
OSPF tidak terus menerus mengirim routing update seperti RIP, OSPF hanya mengirim update ketika ada perubahan topologi tapi tetap ada mekanisme maitanance supaya database tetap syncron

kondisi normal:
router mengirim hello packet setiap 10 detik, tujuannya untuk memastikan neighbor masih hidup.

kondisi ada perubahan:
misal link putus, router mati, interface down, atau penambahan network
maka router akan:
- Generate LSA baru
- Flood LSA ke semua router dalam area
- Semua router update LSDB
- SPF Calculation
- Update routing table

ini biasa disebut ``Triggered Update``

### Jenis - jenis router 
1. Internal router
    Router dengan interface ospf dalam satu area yang sama
2. Backbone Router
    Router yang memiliki interface di area 0 (Backbone Area)
3. Area Border Router(ABR)
    Router yang menghubungkan area 0 dengan area lain
4. Autonomous System Boundary Router(ASBR)
    Router yang memasukkan router dari luar OSPF kedalam OSPF (redistribute)

```Aturan id OSPF semua area harus terhubung ke backbone area, namun jika area tersebut tidak memungkinkan untuk terhubung langsung dengan backbone area bisa menggunakan virtual link(tunnel ospf)```

## Simulasi Routing OSPF
### Single area
![alt text](image-1.png)
Topologi ini merupakan simulasi OSPF single-area dengan tiga router (R0, R1, dan R2) yang saling terhubung dalam Area 0 sebagai backbone. Konfigurasi ini memungkinkan router membentuk neighbor dan bertukar informasi routing untuk menentukan jalur terbaik menggunakan algoritma SPF.

### R0 konfigurasi
```bash
Router>ena
Router#conf t
Router(config)#host R0
R0(config)#int lo0
R0(config-if)#ip add 1.1.1.1 255.255.255.255
R0(config-if)#ip ospf 1 area 0
R0(config-if)#int fa0/0
R0(config-if)#ip add 12.12.12.1 255.255.255.128
R0(config-if)#ip ospf network point-to-point 
R0(config-if)#ip ospf 1 area 0
R0(config-if)#no sh
R0(config-if)#int fa0/1
R0(config-if)#ip add 12.12.12.129 255.255.255.128
R0(config-if)#ip ospf network point-to-point 
R0(config-if)#ip ospf
R0(config-if)#ip ospf 1 are
R0(config-if)#ip ospf 1 area 0
R0(config-if)#no sh
R0(config-if)#exit
R0(config)#router ospf 1
R0(config-router)#router-id 1.1.1.1
R0(config-router)#exit
R0(config)#do wr
Building configuration...
[OK]
```

### R1 konfigurasi
```bash
Router>ena
Router#conf t
Router(config)#host R1
R1(config)#int lo0
R1(config-if)#ip add 2.2.2.2 255.255.255.255
R1(config-if)#ip ospf 1 area 0
R1(config-if)#int fa0/0
R1(config-if)#ip add 12.12.12.2 255.255.255.128
R1(config-if)#ip ospf network point-to-point 
R1(config-if)#ip ospf 1 area 0
R1(config-if)#no sh
R1(config-if)#exit
R1(config)#router ospf 1
R1(config-router)#rou
R1(config-router)#router-id 2.2.2.2
R1(config-router)#exit
R1(config)#do wr
```

### R2 konfigurasi
```bash
Router>ena
Router#conf t
Router(config)#host R2
R2(config)#int lo0
R2(config-if)#ip add 3.3.3.3 255.255.255.255
R2(config-if)#ip ospf 1 area 0
R2(config-if)#int fa0/0
R2(config-if)#ip add 12.12.12.130 255.255.255.128
R2(config-if)#ip ospf network point-to-point 
R2(config-if)#ip ospf 1 area 0
R2(config-if)#no sh
R2(config-if)#exit
R2(config)#router ospf 1
R2(config-router)#router-id 3.3.3.3
R2(config-router)#exit
R2(config)#do wr
Building configuration...
[OK]
```

### Multi Area
![alt text](image.png)
Topologi ini merupakan simulasi OSPF multi-area dengan Area 0 sebagai backbone yang menghubungkan Area 10 dan Area 20. Router R1 dan R2 berperan sebagai ABR yang menghubungkan jaringan di masing-masing area, yaitu 192.16.10.0/24 pada Area 10 dan 192.16.20.0/24 pada Area 20. Komunikasi antar area dilakukan melalui Area 0 sebagai pusat pertukaran routing.

### R1 konfigurasi
```bash
R1(config)#int fa0/1
R1(config-if)#ip add 192.16.10.1 255.255.255.0
R1(config-if)#ip ospf network point-to-point 
R1(config-if)#ip ospf 1 area 10
R1(config-if)#no sh
R1(config-if)#exit
R1(config)#do wr
Building configuration...
[OK]
```

### R2 konfigurasi
```bash
R2(config)#int fa0/1
R2(config-if)#ip add 192.16.20.1 255.255.255.0
R2(config-if)#ip ospf network point-to-point 
R2(config-if)#ip ospf 1 area 20
R2(config-if)#no sh
R2(config-if)#exit
R2(config)#do wr
Building configuration...
[OK]
```

### R3 konfigurasi
```bash
Router>ena
Router#conf t
Router(config)#host R3
R3(config)#int lo0
R3(config-if)#ip add 4.4.4.4 255.255.255.255
R3(config-if)#ip ospf 1 area 10
R3(config-if)#int fa0/0
R3(config-if)#ip add 192.16.10.2 255.255.255.0
R3(config-if)#ip ospf network point-to-point 
R3(config-if)#ip ospf 1 area 10
R3(config-if)#no sh
R3(config-if)#exit
R3(config)#router ospf 1
R3(config-router)#rou
R3(config-router)#router-id 4.4.4.4
R3(config-router)#exit 
R3(config)#do wr
Building configuration...
[OK]
```

### R4 konfigurasi
```bash
Router>ena
Router#conf t
Router(config)#host R4
R4(config)#int lo0
R4(config-if)#ip add 5.5.5.5 255.255.255.255
R4(config-if)#ip ospf 1 area 20
R4(config-if)#int fa0/0
R4(config-if)#ip add 192.16.20.2
R4(config-if)#ip add 192.16.20.2 255.255.255.0
R4(config-if)#ip ospf network point-to-point 
R4(config-if)#ip ospf 1 area 20
R4(config-if)#no sh
R4(config-if)#exit
R4(config)#router ospf 1
R4(config-router)#rou
R4(config-router)#router-id 5.5.5.5
R4(config-router)#exit
R4(config)#do wr
Building configuration...
[OK]
R4(config)#
```
### Verifikasi dari R0
