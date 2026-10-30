# Laporan-Proyek-Jarkom-

## Soal no 1,3 dan 5 (Sean)

### Step pertama buat topologinya

Kita diminta untuk membuat topologi sesuai dengan skema CIDR yang telah dibuat.

- 1 router R-ISP + Server-PT "SRV-8.8.8.8"
  - 7 router: R-PUSAT, R-DC, R-UTARA, R-PELABUHAN, R-SELATAN, R-TIMUR, R-BARAT
  - 14 LAN. Tiap LAN: 1 switch (Switch 2960) + 1 perangkat perwakilan
    (soal: cukup 1 perangkat per LAN). LAN WiFi Publik: switch + Access Point
    + 1 laptop wireless.
  - Nama perangkat (sesuai soal: ganti nama bawaan):
    
    | LAN | Switch | Perangkat perwakilan |
    |---|---|---|
    | Pegawai Balai Kota | SW-PEGAWAI-BK | PC-PEGAWAI-BK |
    | Server Balai Kota | SW-SERVER-BK | SRV-BALAIKOTA |
    | CCTV Kota | SW-CCTV-KOTA | PC-CCTV-KOTA |
    | Server dan Storage DC | SW-SERVER-DC | SRV-DC |
    | WiFi Publik Utara | SW-WIFI-UTARA | AP-WIFI-UTARA + LAPTOP-WIFI-UTARA |
    | Sensor Lalu Lintas Utara | SW-SENSOR-LALIN | PC-SENSOR-LALIN |
    | Sensor dan SCADA Pelabuhan | SW-SCADA-PELABUHAN | PC-SCADA-PELABUHAN |
    | Kantor Pelabuhan | SW-KANTOR-PELABUHAN | PC-KANTOR-PELABUHAN |
    | WiFi Publik Selatan | SW-WIFI-SELATAN | AP-WIFI-SELATAN + LAPTOP-WIFI-SELATAN |
    | PJU Pintar Selatan | SW-PJU-SELATAN | PC-PJU-SELATAN |
    | WiFi Publik Timur | SW-WIFI-TIMUR | AP-WIFI-TIMUR + LAPTOP-WIFI-TIMUR |
    | Sensor Banjir Timur | SW-SENSOR-BANJIR | PC-SENSOR-BANJIR |
    | WiFi Publik Barat | SW-WIFI-BARAT | AP-WIFI-BARAT + LAPTOP-WIFI-BARAT |
    | Puskesmas dan Sekolah Barat | SW-PUSKESMAS-BARAT | PC-PUSKESMAS-BARAT |
 
Kabel
  - Router ke router (L1-L6, R-ISP ke R-PUSAT)  : Copper Cross-Over
  - R-ISP ke SRV-8.8.8.8                        : Copper Cross-Over
  - Router ke switch, switch ke PC/server/AP    : Copper Straight-Through
  - Laptop WiFi: ganti modul NIC dengan WPC300N (wireless), lalu
    hubungkan ke SSID pada AP.
  - Backbone wajib mengikuti Gambar 1: R-PUSAT ke R-DC (L1), R-UTARA (L2),
    R-SELATAN (L3), R-TIMUR (L4), R-BARAT (L5); R-UTARA ke R-PELABUHAN (L6);
    R-PUSAT ke R-ISP; R-ISP ke server 8.8.8.8.

<img width="984" height="651" alt="image" src="https://github.com/user-attachments/assets/3912f105-6924-4d03-bb62-d7c79b0d8e99" />

Pertimbangan desain (bahan dokumentasi)
  1. Satu switch per LAN: router hanya butuh 1 port per LAN, dan perangkat
     baru bisa ditambah tanpa mengubah router.
  2. LAN WiFi Publik memakai AP dan perangkat wireless karena penggunanya
     publik dan bergerak, sedangkan LAN lain memakai kabel karena perangkat
     tetap (server, CCTV, sensor, SCADA, PC kantor) dan butuh koneksi stabil.
  3. Backbone antar-router memakai copper Gigabit (cross-over) mengikuti
     Gambar 1. Tiap link memakai /30 agar hemat alamat.
  4. Router ditambah modul port sampai jumlah port cukup (R-PUSAT: 8 port,
     R-UTARA: 5 port termasuk cadangan L7, R-DC: 4 port termasuk L7).
  5. Server (SRV-DC, SRV-BALAIKOTA) ditempatkan di LAN tersendiri supaya
     layanan terpisah dari perangkat pengguna.

# Skema CIDR - Kelompok 12

## Tabel Interface dan IP

| Router | Port | IP Address | Mask | Prefix | Tersambung ke |
|---|---|---|---|---|---|
| R-ISP | Gi0/0 | 203.0.113.49 | 255.255.255.252 | /30 | KE R-PUSAT |
| R-ISP | Gi0/1 | 8.8.8.1 | 255.255.255.0 | /24 | KE SERVER 8.8.8.8 |
| R-PUSAT | Gi0/0 | 203.0.113.50 | 255.255.255.252 | /30 | KE R-ISP |
| R-PUSAT | Gi0/1 | 10.98.64.1 | 255.255.255.252 | /30 | L1 KE R-DC |
| R-PUSAT | Gi0/2 | 10.98.194.1 | 255.255.255.252 | /30 | L2 KE R-UTARA |
| R-PUSAT | Gi0/3 | 10.97.192.1 | 255.255.255.252 | /30 | L3 KE R-SELATAN |
| R-PUSAT | Gi0/4 | 10.97.64.1 | 255.255.255.252 | /30 | L4 KE R-TIMUR |
| R-PUSAT | Gi0/5 | 10.96.64.1 | 255.255.255.252 | /30 | L5 KE R-BARAT |
| R-PUSAT | Gi0/6 | 10.99.0.1 | 255.255.248.0 | /21 | LAN Pegawai Balai Kota |
| R-PUSAT | Gi0/7 | 10.99.8.1 | 255.255.255.224 | /27 | LAN Server Balai Kota |
| R-DC | Gi0/0 | 10.98.64.2 | 255.255.255.252 | /30 | L1 KE R-PUSAT |
| R-DC | Gi0/1 | 10.98.0.1 | 255.255.224.0 | /19 | LAN CCTV Kota |
| R-DC | Gi0/2 | 10.98.32.1 | 255.255.255.0 | /24 | LAN Server & Storage DC |
| R-DC | Gi0/3 | 10.99.8.33 | 255.255.255.252 | /30 | L7 KE R-UTARA *(hanya file OSPF, Soal 7)* |
| R-UTARA | Gi0/0 | 10.98.194.2 | 255.255.255.252 | /30 | L2 KE R-PUSAT |
| R-UTARA | Gi0/1 | 10.96.192.1 | 255.255.255.252 | /30 | L6 KE R-PELABUHAN |
| R-UTARA | Gi0/2 | 10.98.128.1 | 255.255.192.0 | /18 | LAN WiFi Publik Utara |
| R-UTARA | Gi0/3 | 10.98.192.1 | 255.255.254.0 | /23 | LAN Sensor Lalu Lintas Utara |
| R-UTARA | Gi0/4 | 10.99.8.34 | 255.255.255.252 | /30 | L7 KE R-DC *(hanya file OSPF, Soal 7)* |
| R-PELABUHAN | Gi0/0 | 10.96.192.2 | 255.255.255.252 | /30 | L6 KE R-UTARA |
| R-PELABUHAN | Gi0/1 | 10.96.128.1 | 255.255.224.0 | /19 | LAN Sensor & SCADA Pelabuhan |
| R-PELABUHAN | Gi0/2 | 10.96.160.1 | 255.255.255.128 | /25 | LAN Kantor Pelabuhan |
| R-SELATAN | Gi0/0 | 10.97.192.2 | 255.255.255.252 | /30 | L3 KE R-PUSAT |
| R-SELATAN | Gi0/1 | 10.97.128.1 | 255.255.224.0 | /19 | LAN WiFi Publik Selatan |
| R-SELATAN | Gi0/2 | 10.97.160.1 | 255.255.255.128 | /25 | LAN PJU Pintar Selatan |
| R-TIMUR | Gi0/0 | 10.97.64.2 | 255.255.255.252 | /30 | L4 KE R-PUSAT |
| R-TIMUR | Gi0/1 | 10.97.0.1 | 255.255.224.0 | /19 | LAN WiFi Publik Timur |
| R-TIMUR | Gi0/2 | 10.97.32.1 | 255.255.252.0 | /22 | LAN Sensor Banjir Timur |
| R-BARAT | Gi0/0 | 10.96.64.2 | 255.255.255.252 | /30 | L5 KE R-PUSAT |
| R-BARAT | Gi0/1 | 10.96.0.1 | 255.255.224.0 | /19 | LAN WiFi Publik Barat |
| R-BARAT | Gi0/2 | 10.96.32.1 | 255.255.255.0 | /24 | LAN Puskesmas & Sekolah Barat |

## Label LAN

| Switch | Nama Subnet | Network ID/Prefix | Jumlah Host |
|---|---|---|---|
| SW-PEGAWAI-BK | Pegawai Balai Kota | 10.99.0.0/21 | 1.800 |
| SW-SERVER-BK | Server Balai Kota | 10.99.8.0/27 | 20 |
| SW-CCTV-KOTA | CCTV Kota | 10.98.0.0/19 | 6.000 |
| SW-SERVER-DC | Server & Storage DC | 10.98.32.0/24 | 180 |
| SW-WIFI-UTARA | WiFi Publik Utara | 10.98.128.0/18 | 11.000 |
| SW-SENSOR-LALIN | Sensor Lalu Lintas Utara | 10.98.192.0/23 | 450 |
| SW-SCADA-PELABUHAN | Sensor & SCADA Pelabuhan | 10.96.128.0/19 | 5.000 |
| SW-KANTOR-PELABUHAN | Kantor Pelabuhan | 10.96.160.0/25 | 120 |
| SW-WIFI-SELATAN | WiFi Publik Selatan | 10.97.128.0/19 | 6.500 |
| SW-PJU-SELATAN | PJU Pintar Selatan | 10.97.160.0/25 | 90 |
| SW-WIFI-TIMUR | WiFi Publik Timur | 10.97.0.0/19 | 5.500 |
| SW-SENSOR-BANJIR | Sensor Banjir Timur | 10.97.32.0/22 | 700 |
| SW-WIFI-BARAT | WiFi Publik Barat | 10.96.0.0/19 | 4.500 |
| SW-PUSKESMAS-BARAT | Puskesmas & Sekolah Barat | 10.96.32.0/24 | 220 |

## Static Route

| Router | Network Tujuan | Next Hop | Keterangan |
|---|---|---|---|
| R-PUSAT | 0.0.0.0/0 | 203.0.113.49 | default ke R-ISP |
| R-PUSAT | 10.98.0.0/17 | 10.98.64.2 | ke R-DC |
| R-PUSAT | 10.98.128.0/17 | 10.98.194.2 | ke R-UTARA |
| R-PUSAT | 10.96.128.0/17 | 10.98.194.2 | ke R-PELABUHAN |
| R-PUSAT | 10.97.128.0/17 | 10.97.192.2 | ke R-SELATAN |
| R-PUSAT | 10.97.0.0/17 | 10.97.64.2 | ke R-TIMUR |
| R-PUSAT | 10.96.0.0/17 | 10.96.64.2 | ke R-BARAT |
| R-UTARA | 0.0.0.0/0 | 10.98.194.1 | default ke R-PUSAT |
| R-UTARA | 10.96.128.0/17 | 10.96.192.2 | ke R-PELABUHAN |
| R-DC | 0.0.0.0/0 | 10.98.64.1 | default ke R-PUSAT |
| R-BARAT | 0.0.0.0/0 | 10.96.64.1 | default ke R-PUSAT |
| R-TIMUR | 0.0.0.0/0 | 10.97.64.1 | default ke R-PUSAT |
| R-SELATAN | 0.0.0.0/0 | 10.97.192.1 | default ke R-PUSAT |
| R-PELABUHAN | 0.0.0.0/0 | 10.96.192.1 | default ke R-UTARA |

Proyek ini menggunakan skema CIDR karena konfigurasi static route pada R-PUSAT menjadi lebih efisien dan ringkas (hanya membutuhkan satu prefix rute per cabang atau router distribusi) dibandingkan dengan skema VLSM.

### Masukkan skrip ke setiap Router

-- Skrip CLI per Router 

```
!----- R-ISP -----
enable
configure terminal
hostname R-ISP
no ip domain-lookup
interface FastEthernet0/0
 description KE R-PUSAT
 ip address 203.0.113.49 255.255.255.252
 no shutdown
exit
interface FastEthernet1/0
 description KE SERVER 8.8.8.8
 ip address 8.8.8.1 255.255.255.0
 no shutdown
exit
! TIDAK ada ip route apa pun di R-ISP (tidak boleh punya rute ke alamat privat)
end
write memory
```

```
!----- R-PUSAT -----
enable
configure terminal
hostname R-PUSAT
no ip domain-lookup
interface FastEthernet0/0
 description KE R-ISP
 ip address 203.0.113.50 255.255.255.252
 ip nat outside
 no shutdown
exit
interface FastEthernet1/0
 description L1 KE R-DC
 ip address 10.98.64.1 255.255.255.252
 ip nat inside
 no shutdown
exit
interface FastEthernet7/0
 description L2 KE R-UTARA
 ip address 10.98.194.1 255.255.255.252
 ip nat inside
 no shutdown
exit
interface FastEthernet9/0
 description L3 KE R-SELATAN
 ip address 10.97.192.1 255.255.255.252
 ip nat inside
 no shutdown
exit
interface FastEthernet8/0
 description L4 KE R-TIMUR
 ip address 10.97.64.1 255.255.255.252
 ip nat inside
 no shutdown
exit
interface FastEthernet6/0
 description L5 KE R-BARAT
 ip address 10.96.64.1 255.255.255.252
 ip nat inside
 no shutdown
exit
interface FastEthernet5/0
 description LAN Pegawai Balai Kota
 ip address 10.99.0.1 255.255.248.0
 ip nat inside
 no shutdown
exit
interface FastEthernet4/0
 description LAN Server Balai Kota
 ip address 10.99.8.1 255.255.255.224
 ip nat inside
 no shutdown
exit
! default ke R-ISP
ip route 0.0.0.0 0.0.0.0 203.0.113.49
! ke R-DC
ip route 10.98.0.0 255.255.128.0 10.98.64.2
! ke R-UTARA
ip route 10.98.128.0 255.255.128.0 10.98.194.2
! ke R-PELABUHAN
ip route 10.96.128.0 255.255.128.0 10.98.194.2
! ke R-SELATAN
ip route 10.97.128.0 255.255.128.0 10.97.192.2
! ke R-TIMUR
ip route 10.97.0.0 255.255.128.0 10.97.64.2
! ke R-BARAT
ip route 10.96.0.0 255.255.128.0 10.96.64.2
ip access-list standard NAT-INTERNAL
 permit 10.96.0.0 0.3.255.255
 deny any
exit
ip nat inside source list NAT-INTERNAL interface FastEthernet0/0 overload
end
write memory
```

```
!----- R-DC -----
enable
configure terminal
hostname R-DC
no ip domain-lookup
interface FastEthernet0/0
 description L1 KE R-PUSAT
 ip address 10.98.64.2 255.255.255.252
 no shutdown
exit
interface FastEthernet6/0
 description LAN CCTV Kota
 ip address 10.98.0.1 255.255.224.0
 no shutdown
exit
interface FastEthernet1/0
 description LAN Server & Storage DC
 ip address 10.98.32.1 255.255.255.0
 no shutdown
exit
! default ke R-PUSAT
ip route 0.0.0.0 0.0.0.0 10.98.64.1
end
write memory
```
```
!----- R-UTARA -----
enable
configure terminal
hostname R-UTARA
no ip domain-lookup
interface FastEthernet0/0
 description L2 KE R-PUSAT
 ip address 10.98.194.2 255.255.255.252
 no shutdown
exit
interface FastEthernet7/0
 description L6 KE R-PELABUHAN
 ip address 10.96.192.1 255.255.255.252
 no shutdown
exit
interface FastEthernet1/0
 description LAN WiFi Publik Utara
 ip address 10.98.128.1 255.255.192.0
 no shutdown
exit
interface FastEthernet6/0
 description LAN Sensor Lalu Lintas Utara
 ip address 10.98.192.1 255.255.254.0
 no shutdown
exit
! default ke R-PUSAT
ip route 0.0.0.0 0.0.0.0 10.98.194.1
! ke R-PELABUHAN
ip route 10.96.128.0 255.255.128.0 10.96.192.2
end
write memory
```

```
!----- R-PELABUHAN -----
enable
configure terminal
hostname R-PELABUHAN
no ip domain-lookup
interface FastEthernet1/0
 description L6 KE R-UTARA
 ip address 10.96.192.2 255.255.255.252
 no shutdown
exit
interface FastEthernet0/0
 description LAN Sensor & SCADA Pelabuhan
 ip address 10.96.128.1 255.255.224.0
 no shutdown
exit
interface FastEthernet6/0
 description LAN Kantor Pelabuhan
 ip address 10.96.160.1 255.255.255.128
 no shutdown
exit
! default ke R-UTARA
ip route 0.0.0.0 0.0.0.0 10.96.192.1
end
write memory
```

```
!----- R-SELATAN -----
enable
configure terminal
hostname R-SELATAN
no ip domain-lookup
interface FastEthernet0/0
 description L3 KE R-PUSAT
 ip address 10.97.192.2 255.255.255.252
 no shutdown
exit
interface FastEthernet1/0
 description LAN WiFi Publik Selatan
 ip address 10.97.128.1 255.255.224.0
 no shutdown
exit
interface FastEthernet6/0
 description LAN PJU Pintar Selatan
 ip address 10.97.160.1 255.255.255.128
 no shutdown
exit
! default ke R-PUSAT
ip route 0.0.0.0 0.0.0.0 10.97.192.1
end
write memory
```

```
!----- R-TIMUR -----
enable
configure terminal
hostname R-TIMUR
no ip domain-lookup
interface FastEthernet0/0
 description L4 KE R-PUSAT
 ip address 10.97.64.2 255.255.255.252
 no shutdown
exit
interface FastEthernet1/0
 description LAN WiFi Publik Timur
 ip address 10.97.0.1 255.255.224.0
 no shutdown
exit
interface FastEthernet6/0
 description LAN Sensor Banjir Timur
 ip address 10.97.32.1 255.255.252.0
 no shutdown
exit
! default ke R-PUSAT
ip route 0.0.0.0 0.0.0.0 10.97.64.1
end
write memory
```

```
!----- R-BARAT -----
enable
configure terminal
hostname R-BARAT
no ip domain-lookup
interface FastEthernet0/0
 description L5 KE R-PUSAT
 ip address 10.96.64.2 255.255.255.252
 no shutdown
exit
interface FastEthernet1/0
 description LAN WiFi Publik Barat
 ip address 10.96.0.1 255.255.224.0
 no shutdown
exit
interface FastEthernet6/0
 description LAN Puskesmas & Sekolah Barat
 ip address 10.96.32.1 255.255.255.0
 no shutdown
exit
! default ke R-PUSAT
ip route 0.0.0.0 0.0.0.0 10.96.64.1
end
write memory
```

<img width="934" height="656" alt="image" src="https://github.com/user-attachments/assets/b97f90b4-fcec-499c-a57d-d0dc55495fca" />

### Pengujian 

- Pengujian di CLI Router

  - Di semua router

    Memastikan semua port up/up

    ```show ip interface brief```

    Mencocokan rute dengan tabel

    ```show ip route static```
    
    <img width="905" height="248" alt="image" src="https://github.com/user-attachments/assets/1b061e96-89ec-4538-9b75-408f06c0bef3" />

    <img width="878" height="215" alt="image" src="https://github.com/user-attachments/assets/07d16172-c132-446d-b94b-aa0b263ffcdd" />

- Pengujian di Router Pusat
  
  - Mengecek default route ke R-ISP

    ```show ip route | include 0.0.0.0```

  - Cek fungsi NAT
 
    ```show ip nat translations```

    ```show ip nat statistics```

  <img width="917" height="265" alt="image" src="https://github.com/user-attachments/assets/b29ff48b-212c-499f-a713-a25c78b2b076" />

- Pengujin di R-ISP
  
  - Memastikan tidak ada rute ke IP Private
 
    ```show ip route```

    <img width="897" height="295" alt="image" src="https://github.com/user-attachments/assets/41da870b-95ba-4329-b180-d606a04f1ca2" />

    Dari foto tersebut kita bisa melihat bahwa konfigurasi NAT di R-Pusat berhasil karena tidak adanya rute privat.

  - Lakukan ping ke salah satu alamat privat yang hasilnya harus gagal
    
    Disini aku melakukan ping ke PC-Pegawai-Bk
 
    ```ping 10.99.0.2```
    
    <img width="893" height="125" alt="image" src="https://github.com/user-attachments/assets/1161e3b1-e9ba-419c-a148-327a3629e680" />

    Dari foto diatas kita bisa tahu bahwa konfigurasi NAT di R-PUSAT bekerja dengan benar karena R-ISP memang sengaja tidak diberi rute statis ke jaringan internal.

- Pengujian di PC

  - Tes Antar-LAN 1:
    Dari PC-Puskesmas-Barat ping ke PC-Scada-Pelabuhan:
    
    ```ping 10.96.128.2```
    
    <img width="795" height="268" alt="image" src="https://github.com/user-attachments/assets/e1aeb33c-2e03-4f57-bda2-3586a7c9ef60" />

  - Tes Antar-LAN 2:
    Dari PC-Cctv-Kota ping ke PC-Sensor-Banjir:

    ```ping 10.97.32.2```

    <img width="762" height="266" alt="image" src="https://github.com/user-attachments/assets/515c6e1b-a4ac-4c8b-b9e7-16a4654295a9" />

  - Tes jalur tracert
    Dari PC-Kantor-Pelabuhan ke PC-Pegawai-Bk, harusnya lewat R-UTARA (10.96.192.1) dan R-PUSAT(10.98.194.1).

    ```tracert 10.99.0.2```

    <img width="635" height="213" alt="image" src="https://github.com/user-attachments/assets/314903fc-807e-4b0b-9e85-cab1e3a69bb7" />

    Dari foto diatas kita bisa melihat bahwa jalur yang dilewati PC-Kantor-Pelabuhan ke PC-Pegawai-Bk berhasil melewati R-UTARA (10.96.192.1) dan R-PUSAT(10.98.194.1).

  - Tes internet

    ```ping 8.8.8.8```

    <img width="703" height="267" alt="image" src="https://github.com/user-attachments/assets/dc8bc56e-6258-44e3-9f66-2c9e0fcc5ff4" />



    


    


  

  





