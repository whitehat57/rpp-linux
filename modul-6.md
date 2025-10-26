# Modul 6 — Jaringan Khusus & Lanjutan (Automobile/CAN, SCADA/ICS, Radio/SDR)

## Tujuan Pembelajaran

Setelah modul ini kamu mampu:

* Menjelaskan konsep & *tooling* dasar jaringan **Automobile/CAN** (vcan, can-utils, ICSim; serta gambaran ISO-TP).    
* Memetakan ekosistem **SCADA/ICS**: produsen, ragam protokol (mis. Modbus, DNP3), dan tantangan keamanannya.   
* Memahami dasar **Radio/SDR** (istilah pokok, pola serangan umum pada kanal radio) dan praktik **terbatas pada penerimaan** sinyal legal seperti **ADS-B**.    
* Menghubungkan hasil observasi ke kebijakan *hardening*, *threat modeling*, dan etika penggunaan.

> ⚠️ **Etika & Legal**: Semua uji hanya pada **lingkungan lab** (simulator/offline) atau **sistem yang kamu miliki izin tertulis**. Modul ini **tidak** memandu transmisi/eksploit nyata (mis. memancarkan sinyal GPS palsu). Aktivitas RF tunduk aturan lokal.

---

## Peta Keterkaitan

* **Modul 1** (Linux/CLI/permission) → dipakai untuk memuat modul kernel (`modprobe`), membuat antarmuka `vcan`, dan menjalankan alat CLI.  
* **Modul 2** (tcpdump/Wireshark/filter) → dipakai untuk membaca lalu lintas CAN/Modbus/ADS-B dan menyusun filter.  
* **Modul 3–5** → konsep port/protokol, *service setup*, dan analisis protokol mendasari studi kasus ICS & SDR.

---

## Materi Inti

### A) Automobile/CAN (simulasi aman)

* **Virtual CAN (vcan)**: muat modul & buat antarmuka uji; cocok untuk belajar tanpa hardware mobil.  
* **ICSim (Instrument Cluster Simulator)**: panel & *controller* untuk “mengemudi” mobil virtual; dipakai bersama `vcan0`.  
* **can-utils yang esensial**: `cansniffer`, `candump`, `canplayer`, `cansend` untuk observasi & pemutaran lalu lintas **di simulator**. 
* **ISO-TP tools (gambaran)**: `isotpsend`, `isotprecv`, `isotpdump`, dll. (diagnostik lapisan transport pada CAN). 

> Catatan: langkah memuat modul `can`/`vcan` disebutkan dalam sumber jaringan dan merujuk ke pembahasan modul kernel pada buku Linux. 

### B) SCADA/ICS (gambaran arsitektur & protokol)

* **Karakteristik**: banyak protokol non-TCP/IP standar; variasi antarpabrikan menyulitkan *security baseline*.  
* **Vendor & keragaman**: Siemens, Honeywell, Rockwell/Allen-Bradley, Schneider, dll.  
* **Protokol lazim**: Modbus, DNP3 (dan lain-lain) — penting untuk dikenali pola header/func-code ketika menganalisis PCAP. 

### C) Radio/SDR (receive-only yang legal)

* **Istilah dasar**: amplitude, frequency, sample rate, filter, *digital signal processing*. 
* **Metode serangan di kanal radio (konseptual)**: sniffing, replay, spoofing (signal deception), hijacking/DoS — dipahami untuk **deteksi/pertahanan**, bukan praktik ofensif.   
* **ADS-B (penerbangan) — contoh penerimaan aman**: gunakan *receiver* SDR murah + perangkat lunak `dump1090` untuk menampilkan posisi/altitude pesawat; mode *interactive* dan *web map* lokal (`--net` → buka `localhost:8080`).   
* **Jangan dipraktikkan**: transmisi/spoofing GPS di udara bebas (contoh di buku bersifat demonstrasi riset; hindari transmisi RF tanpa izin & peredam RF). 

---

## Praktikum (berorientasi defensif & observasi)

### Lab 6.1 — **Membangun Virtual CAN + ICSim** (100% simulasi)

1. **Buat antarmuka vcan**

   ```bash
   sudo modprobe vcan
   sudo ip link add dev vcan0 type vcan
   sudo ip link set up vcan0
   ```

   Verifikasi `ifconfig vcan0`.  
2. **Jalankan ICSim**

   * Panel: `./icsim vcan0`
   * Kontrol: `./controls vcan0`  
3. **Sniff CAN** di simulator: `cansniffer -c vcan0` (nilai berubah ditandai jelas).  

### Lab 6.2 — **candump / canplayer** (hanya terhadap ICSim)

* Rekam lalu lintas (mis. saat menyalakan *turn signal* di ICSim) dan **putar ulang** ke ICSim untuk memahami korelasi ID-frame ↔ fungsi (dokumentasikan hanya di lab simulasi). (Rujukan daftar utilitas.) 

### Lab 6.3 — **SCADA/ICS Packet Walkthrough** (PCAP contoh / lab privat)

* Buka PCAP Modbus/DNP3 (lab) di Wireshark. Identifikasi *function code* dan device addressing, lalu catat perbedaannya. Kaitkan dengan keragaman vendor & protokol.  

### Lab 6.4 — **SDR Receive-Only (ADS-B)**

* Instal `dump1090`, jalankan *interactive* dan opsi *web map* lokal (`--net`) untuk memantau pesawat di area kamu; **terima** data saja. Buka `localhost:8080` untuk peta. Dokumentasikan *callsign*, *altitude*, *ground speed*.  

### Lab 6.5 — **Threat Modeling singkat**

* Dari Lab 6.1–6.4, tulis daftar “apa yang bisa salah” + kontrol mitigasi: *network segmentation*, daftar izin komunikasi antar node, monitoring anomali (mis. CAN ID burst, *function code* ICS tak lazim), RF *receive-only policy*.

---

## Kuis Cepat

1. Sebutkan empat utilitas **can-utils** yang umum untuk observasi lalu lintas CAN. 
2. Mengapa **SCADA/ICS** lebih sulit distandardisasi keamanannya dibanding IT biasa? Beri dua alasan.  
3. Jelaskan perbedaan **sniffing** dan **replay** pada kanal radio (dua kalimat).  
4. Pada ADS-B, data apa yang bisa kamu pantau secara legal dengan `dump1090`? (Sebut 3). 

---

## Tugas Mini (integrasi)

Susun **laporan observasi** 2–3 halaman:

* **CAN (ICSim)**: 3 ID-frame yang sering muncul, deskripsi perubahan nilai saat aksi (mis. indikator), dan hipotesis fungsi. (Lampirkan potongan output `cansniffer`.) 
* **SCADA/ICS**: 1 cuplikan paket Modbus/DNP3—jelaskan field penting dan potensi mis-konfigurasi yang bisa dimonitor. 
* **SDR (ADS-B)**: tabel 5 penerbangan: *callsign/altitude/ground-speed* dari mode *interactive*. (Screenshot peta lokal diperbolehkan.) 
* **Rekomendasi**: 5 kontrol prioritas (mis. whitelisting CAN ID di ECU gateway, *network zoning* ICS, *anomaly detection*, RF receive-only SOP, logging terpusat).

---

## Rubrik Penilaian

| Aspek              | Kriteria                                               | Bobot |
| ------------------ | ------------------------------------------------------ | ----- |
| CAN (simulasi)     | vcan & ICSim berjalan; observasi `cansniffer` bernilai | 25%   |
| SCADA/ICS          | Pemahaman protokol & analisis paket dasar              | 20%   |
| SDR (receive-only) | Menampilkan data ADS-B & interpretasi metrik           | 20%   |
| Threat Modeling    | Kontrol mitigasi realistis & terhubung dengan temuan   | 20%   |
| Dokumentasi        | Bukti, kejelasan, dan kepatuhan etika/legal            | 15%   |

## Bacaan Sumber (kutipan relevan)

* **TOC & cakupan bab** Automobile/SCADA/RF. 
* **vcan & verifikasi antarmuka**; **ICSim** (panel/kontrol) & *can-utils*.    
* **ISO-TP tools** (gambaran). 
* **SCADA/ICS** (ciri, vendor, protokol).   
* **SDR—terminologi & metode**; **ADS-B** (dump1090, *interactive*, peta lokal).    
* **Peringatan GPS spoofing (konsep, jangan transmisikan)**. 
