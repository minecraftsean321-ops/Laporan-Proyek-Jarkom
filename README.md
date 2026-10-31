# Laporan-Proyek-Jarkom

## Soal no 1,3 dan 5 (Sean)

### Step Pertama

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

<img width="750" height="auto" alt="image" src="https://github.com/user-attachments/assets/3912f105-6924-4d03-bb62-d7c79b0d8e99" />

<img width="750" height="auto" alt="image" src="https://github.com/user-attachments/assets/bbe841c2-a4fa-4d34-9438-eee09838d130" />


### Pertimbangan desain
  1. Satu switch per LAN: router hanya butuh 1 port per LAN, dan perangkat
     baru bisa ditambah tanpa mengubah router.
  2. LAN WiFi Publik memakai AP dan perangkat wireless karena penggunanya
     publik dan bergerak, sedangkan LAN lain memakai kabel karena perangkat
     tetap (server, CCTV, sensor, SCADA, PC kantor) dan butuh koneksi stabil.
  3. Backbone antar-router memakai copper FastEthernet (cross-over) mengikuti Gambar 1.
     Tiap link memakai /30 agar hemat alamat.
  4. Router ditambah modul port FastEthernet sampai jumlah port cukup untuk satu port per
     link dan per LAN. Kebutuhan port: R-PUSAT 8 port (1 ke R-ISP, 5 link L1-L5, dan 2 LAN),
     R-UTARA 4 port, R-DC 3 port, R-PELABUHAN, R-SELATAN, R-TIMUR, dan R-BARAT masing-masing
     3 port, serta R-ISP 2 port. Link cadangan L7 (R-DC ke R-UTARA) tidak dipasang pada file
     utama; port tambahannya baru disiapkan pada file OSPF (Soal 7).
  5. Server (SRV-DC, SRV-BALAIKOTA) ditempatkan di LAN tersendiri supaya
     layanan terpisah dari perangkat pengguna.

## Skema CIDR - Kelompok 12

## Tabel Interface dan IP

| Router | Port | IP Address | Mask | Prefix | Tersambung ke |
|---|---|---|---|---|---|
| R-ISP | Fa0/0 | 203.0.113.49 | 255.255.255.252 | /30 | KE R-PUSAT |
| R-ISP | Fa1/0 | 8.8.8.1 | 255.255.255.0 | /24 | KE SERVER 8.8.8.8 |
| R-PUSAT | Fa0/0 | 203.0.113.50 | 255.255.255.252 | /30 | KE R-ISP |
| R-PUSAT | Fa1/0 | 10.98.64.1 | 255.255.255.252 | /30 | L1 KE R-DC |
| R-PUSAT | Fa7/0 | 10.98.194.1 | 255.255.255.252 | /30 | L2 KE R-UTARA |
| R-PUSAT | Fa9/0 | 10.97.192.1 | 255.255.255.252 | /30 | L3 KE R-SELATAN |
| R-PUSAT | Fa8/0 | 10.97.64.1 | 255.255.255.252 | /30 | L4 KE R-TIMUR |
| R-PUSAT | Fa6/0 | 10.96.64.1 | 255.255.255.252 | /30 | L5 KE R-BARAT |
| R-PUSAT | Fa5/0 | 10.99.0.1 | 255.255.248.0 | /21 | LAN Pegawai Balai Kota |
| R-PUSAT | Fa4/0 | 10.99.8.1 | 255.255.255.224 | /27 | LAN Server Balai Kota |
| R-DC | Fa0/0 | 10.98.64.2 | 255.255.255.252 | /30 | L1 KE R-PUSAT |
| R-DC | Fa6/0 | 10.98.0.1 | 255.255.224.0 | /19 | LAN CCTV Kota |
| R-DC | Fa1/0 | 10.98.32.1 | 255.255.255.0 | /24 | LAN Server & Storage DC |
| R-UTARA | Fa0/0 | 10.98.194.2 | 255.255.255.252 | /30 | L2 KE R-PUSAT |
| R-UTARA | Fa7/0 | 10.96.192.1 | 255.255.255.252 | /30 | L6 KE R-PELABUHAN |
| R-UTARA | Fa1/0 | 10.98.128.1 | 255.255.192.0 | /18 | LAN WiFi Publik Utara |
| R-UTARA | Fa6/0 | 10.98.192.1 | 255.255.254.0 | /23 | LAN Sensor Lalu Lintas Utara |
| R-PELABUHAN | Fa1/0 | 10.96.192.2 | 255.255.255.252 | /30 | L6 KE R-UTARA |
| R-PELABUHAN | Fa0/0 | 10.96.128.1 | 255.255.224.0 | /19 | LAN Sensor & SCADA Pelabuhan |
| R-PELABUHAN | Fa6/0 | 10.96.160.1 | 255.255.255.128 | /25 | LAN Kantor Pelabuhan |
| R-SELATAN | Fa0/0 | 10.97.192.2 | 255.255.255.252 | /30 | L3 KE R-PUSAT |
| R-SELATAN | Fa1/0 | 10.97.128.1 | 255.255.224.0 | /19 | LAN WiFi Publik Selatan |
| R-SELATAN | Fa6/0 | 10.97.160.1 | 255.255.255.128 | /25 | LAN PJU Pintar Selatan |
| R-TIMUR | Fa0/0 | 10.97.64.2 | 255.255.255.252 | /30 | L4 KE R-PUSAT |
| R-TIMUR | Fa1/0 | 10.97.0.1 | 255.255.224.0 | /19 | LAN WiFi Publik Timur |
| R-TIMUR | Fa6/0 | 10.97.32.1 | 255.255.252.0 | /22 | LAN Sensor Banjir Timur |
| R-BARAT | Fa0/0 | 10.96.64.2 | 255.255.255.252 | /30 | L5 KE R-PUSAT |
| R-BARAT | Fa1/0 | 10.96.0.1 | 255.255.224.0 | /19 | LAN WiFi Publik Barat |
| R-BARAT | Fa6/0 | 10.96.32.1 | 255.255.255.0 | /24 | LAN Puskesmas & Sekolah Barat |

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

### Alasan Pemilihan Skema CIDR

Proyek ini menggunakan skema CIDR (supernetting) dengan blok `10.96.0.0/14`. Alasannya:

1. **Static route lebih ringkas.** Blok setiap cabang disusun hierarkis, sehingga R-PUSAT
   cukup memiliki satu rute ringkasan /17 per cabang (6 rute), dan R-UTARA satu rute
   ke blok R-PELABUHAN. Pada skema VLSM, R-PUSAT membutuhkan satu rute per LAN (12 rute)
   dan R-UTARA dua rute. Rute yang lebih sedikit mengurangi risiko salah ketik dan
   mempermudah pemeriksaan.
2. **Mudah dikembangkan.** Penambahan LAN baru di dalam blok sebuah cabang tidak
   mengubah konfigurasi R-PUSAT, karena rute ringkasannya sudah mencakup seluruh blok.
3. **Sesuai kebutuhan Soal 3.** Aturan default route ke atas dan rute ke bawah di
   R-PUSAT serta R-UTARA dapat dipenuhi dengan jumlah rute yang kecil.

**Trade-off.** CIDR kurang hemat alamat karena blok tiap cabang dibulatkan ke pangkat 2.
Dari total kebutuhan 61.752 alamat, CIDR memakai kapasitas /14 (262.144 alamat) sehingga
pemakaiannya 23,56%, sedangkan VLSM cukup /16 (65.536 alamat) dengan pemakaian 94,23%.
Pemborosan ini dapat diterima karena ruang privat `10.0.0.0/8` masih sangat longgar dan
seluruh blok /14 masih berada dalam jatah alamat kelompok (10.96.0.0 - 10.103.255.255).

### Penjelasan Konfigurasi

**Pengalamatan.** Gateway tiap LAN adalah host pertama subnet dan dipasang pada
interface router. Link L1-L5 memakai /30 dengan R-PUSAT di alamat .1 dan router
cabang di .2; pada L6 R-UTARA .1 dan R-PELABUHAN .2. Link R-ISP ke R-PUSAT memakai
203.0.113.48/30 (4 x K, K = 12). Server 8.8.8.8 memakai 8.8.8.8/24 dengan gateway 8.8.8.1.

**Static routing.** Default route hanya mengarah ke router di atas atau ke luar:
R-DC, R-BARAT, R-TIMUR, dan R-SELATAN ke R-PUSAT, R-PELABUHAN ke R-UTARA, serta
R-PUSAT ke R-ISP. R-PUSAT memiliki satu rute ringkasan /17 per cabang (R-DC, R-UTARA,
R-PELABUHAN via R-UTARA, R-SELATAN, R-TIMUR, R-BARAT), sedangkan R-UTARA memiliki
rute ke blok R-PELABUHAN 10.96.128.0/17. Ringkasan ini berlaku karena tiap blok /17
sudah mencakup LAN dan link milik cabang tersebut.

**NAT.** R-PUSAT memakai PAT (overload) pada interface Fa0/0 (outside); semua interface
lain adalah inside. ACL NAT-INTERNAL mengizinkan blok privat kelompok 10.96.0.0/14,
sehingga semua host berbagi satu IP publik 203.0.113.50. R-ISP sengaja tanpa rute privat.

**Alamat PC saat pengujian.** Karena DHCP dikerjakan pada Soal 4, PC perwakilan
sementara memakai IP statis host ke-2 tiap subnet (misalnya PC-Pegawai-Bk
10.99.0.2/21, gateway 10.99.0.1).

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

<img width="750" height="auto" alt="image" src="https://github.com/user-attachments/assets/b97f90b4-fcec-499c-a57d-d0dc55495fca" />

### Pengujian 

- Pengujian di CLI Router

  - Di semua router

    Memastikan semua port up/up

    ```show ip interface brief```

    Mencocokan rute dengan tabel

    ```show ip route static```  

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

    Dari `show ip route` terlihat R-ISP hanya memiliki dua rute terhubung langsung (203.0.113.48/30 dan 8.8.8.0/24) dan tidak ada rute ke alamat privat, sesuai ketentuan        soal.

  - Lakukan ping ke salah satu alamat privat yang hasilnya harus gagal
    
    Disini aku melakukan ping ke PC-Pegawai-Bk
 
    ```ping 10.99.0.2```
    
    <img width="893" height="125" alt="image" src="https://github.com/user-attachments/assets/1161e3b1-e9ba-419c-a148-327a3629e680" />

    Ping dari R-ISP ke 10.99.0.2 gagal karena R-ISP tidak memiliki rute ke jaringan internal, sehingga jaringan privat tidak terekspos ke sisi internet. Bukti NAT bekerja       ada pada `ping 8.8.8.8` dari PC yang berhasil dan `show ip nat translations` yang menunjukkan alamat privat diterjemahkan menjadi 203.0.113.50.

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



    


    


  

  





