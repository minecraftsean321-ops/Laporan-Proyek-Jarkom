# Laporan-Proyek-Jarkom-

## Soal no 1.
Kita diminta untuk membuat topologi sesuai dengan skema CIDR yang telah dibuat.

- 1 router R-ISP + Server-PT "SRV-8.8.8.8"
  - 7 router: R-PUSAT, R-DC, R-UTARA, R-PELABUHAN, R-SELATAN, R-TIMUR, R-BARAT
  - 14 LAN. Tiap LAN: 1 switch (Switch 2960) + 1 perangkat perwakilan
    (soal: cukup 1 perangkat per LAN). LAN WiFi Publik: switch + Access Point
    + 1 laptop wireless.
  - Nama perangkat (sesuai soal: ganti nama bawaan):
      LAN                          Switch               Perangkat perwakilan
      Pegawai Balai Kota           SW-PEGAWAI-BK        PC-PEGAWAI-BK
      Server Balai Kota            SW-SERVER-BK         SRV-BALAIKOTA
      CCTV Kota                    SW-CCTV-KOTA         PC-CCTV-KOTA
      Server dan Storage DC        SW-SERVER-DC         SRV-DC
      WiFi Publik Utara            SW-WIFI-UTARA        AP-WIFI-UTARA + LAPTOP-WIFI-UTARA
      Sensor Lalu Lintas Utara     SW-SENSOR-LALIN      PC-SENSOR-LALIN
      Sensor dan SCADA Pelabuhan   SW-SCADA-PELABUHAN   PC-SCADA-PELABUHAN
      Kantor Pelabuhan             SW-KANTOR-PELABUHAN  PC-KANTOR-PELABUHAN
      WiFi Publik Selatan          SW-WIFI-SELATAN      AP-WIFI-SELATAN + LAPTOP-WIFI-SELATAN
      PJU Pintar Selatan           SW-PJU-SELATAN       PC-PJU-SELATAN
      WiFi Publik Timur            SW-WIFI-TIMUR        AP-WIFI-TIMUR + LAPTOP-WIFI-TIMUR
      Sensor Banjir Timur          SW-SENSOR-BANJIR     PC-SENSOR-BANJIR
      WiFi Publik Barat            SW-WIFI-BARAT        AP-WIFI-BARAT + LAPTOP-WIFI-BARAT
      Puskesmas dan Sekolah Barat  SW-PUSKESMAS-BARAT   PC-PUSKESMAS-BARAT
 
Kabel
  - Router ke router (L1-L6, R-ISP ke R-PUSAT)  : Copper Cross-Over
  - R-ISP ke SRV-8.8.8.8                        : Copper Cross-Over
  - Router ke switch, switch ke PC/server/AP    : Copper Straight-Through
  - Laptop WiFi: ganti modul NIC dengan WPC300N (wireless), lalu
    hubungkan ke SSID pada AP.
  - Backbone wajib mengikuti Gambar 1: R-PUSAT ke R-DC (L1), R-UTARA (L2),
    R-SELATAN (L3), R-TIMUR (L4), R-BARAT (L5); R-UTARA ke R-PELABUHAN (L6);
    R-PUSAT ke R-ISP; R-ISP ke server 8.8.8.8.

