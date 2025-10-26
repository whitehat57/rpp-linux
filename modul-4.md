# Modul 4 — Wireless & Short-Range Networks (Wi-Fi & Bluetooth)

## Tujuan Pembelajaran

Setelah modul ini kamu mampu:

* Memetakan arsitektur **Wi-Fi (802.11)**, tipe frame (management/control/data), dan membaca *display filter* Wireshark untuk masing-masing.  
* Menjelaskan evolusi **protokol keamanan Wi-Fi** (WEP → WPA → WPA2-PSK, dan konteks pemakaiannya). 
* Mengidentifikasi antarmuka nirkabel di Linux dan membaca statusnya dengan utilitas dasar (mis. `iwconfig`).  
* Menjelaskan **konsep** serangan sisi Wi-Fi (deauthentication, *evil twin*, PMKID/WPS) untuk tujuan deteksi/mitigasi (tanpa praktik ofensif).  
* Memahami **dasar Bluetooth**: pairing, piconet, *BlueZ* tools (hciconfig/hcitool/hcidump), serta mode keamanan.    

## Peta Keterkaitan

* **Modul 1 (Linux)** → dipakai untuk memeriksa antarmuka nirkabel (`iwconfig`) dan bekerja dengan toolset *BlueZ*.  
* **Modul 2 (TCP/IP & Wireshark)** → landasan membaca frame 802.11, menerapkan *display filter*, dan analisis paket.  

---

## Materi Inti

### 1) Fondasi Wi-Fi (802.11) & Keamanannya

* Struktur bab materi: Wi-Fi basics, *security protocols*, *adapters*, *aircrack-ng*, anatomi frame, filter Wireshark, dan “Attacking Wi-Fi APs” (dibahas di sini sebagai wawasan defensif). 
* WEP (lemah) → WPA → WPA2-PSK (umum di rumah/kantor). 
* Tipe frame penting & *display filter*: Probe Request/Response, Beacon, Authentication, **Deauthentication** (sering dipakai alat serang → indikator serangan di log/pcap).  

### 2) Analisis Wi-Fi dengan Wireshark

* Panel *Packet List/Details/Bytes*, filter protokol/IP/port, dan pencarian *payload* (“contains”).   
* Cara memilih field 802.11: gunakan ekspresi `wlan.fc.subtype == …` untuk memfokuskan tipe frame tertentu.  

### 3) Konsep Serangan Wi-Fi (untuk Deteksi & Hardening)

* **Deauthentication**: frame `wlan.fc.type==0x0C` sering terlihat saat *bump* pengguna dari AP (indikator anomali). 
* **PMKID (WPA2-PSK)**: secara konsep, hash bisa didapat dari satu frame RSN IE tanpa klien; pahami alurnya untuk menilai paparan dan kebijakan kata sandi. (Tidak melakukan eksploit, hanya pemahaman). 
* **WPS**: dapat menjadi kelemahan jika aktif; relevan untuk asesmen konfigurasi AP. 

### 4) Dasar Bluetooth

* Jangkauan & pairing (link-key 128-bit), piconet (master + hingga 7 slave), *frequency hopping*.   
* *BlueZ* & tool dasar: **hciconfig**, **hcitool**, **hcidump**. 
* Mode keamanan Bluetooth (mode 1/2/3) dan implikasinya. 
* *BlueBorne* sebagai studi kasus kerentanan: serang **SDP**; relevan untuk patch compliance & inventaris aset. (Bahasan konseptual, tanpa langkah eksploit). 

> ⚠️ **Etika & Batasan Praktik**: Semua aktivitas harus dilakukan pada **perangkat & jaringan milik sendiri** atau yang **punya izin tertulis**. Materi eksploit pada sumber asli diposisikan **untuk pemahaman/deteksi** di kursus ini—bukan untuk disalahgunakan.

---

## Praktikum (berorientasi defensif & observasi)

### Lab 1 — Identifikasi Antarmuka Nirkabel (Linux)

1. Tampilkan antarmuka dan status nirkabel dengan utilitas Linux yang sesuai; catat mode (Managed/Monitor) dan parameter penting. Simpan hasil ke logbook. 

### Lab 2 — *Passive Capture* & Filter Frame 802.11 (Wireshark)

1. Lakukan *capture* pada antarmuka yang diizinkan (lab kamu).
2. Terapkan *display filter* untuk **Beacon** (`wlan.fc.type==0x08`), **Probe Request** (`0x04`), **Authentication** (`0x0B`), **Deauthentication** (`0x0C`).
3. Tulis apa fungsi masing-masing frame dan pola normal vs anomali (mis. lonjakan deauth).  

### Lab 3 — Membaca Parameter Keamanan Wi-Fi

1. Dari *capture* Beacon, identifikasi *security information element* untuk melihat WEP/WPA/WPA2. Ringkas temuan dan rekomendasi penguatan (mis. nonaktifkan WPS, pakai passphrase kuat).  

### Lab 4 — Bluetooth Inventory & Service Discovery (Perangkat Sendiri)

1. Inventaris adaptor & status Bluetooth Linux dengan *BlueZ* tools yang relevan.
2. *Scan* perangkat milik sendiri dan lakukan *service discovery* untuk memetakan profil/layanan (contoh: gunakan utilitas yang mendukung SDP *browse*). Dokumentasikan MAC, layanan, dan *reachability* tes (*echo request* L2CAP).   

### Lab 5 — *Threat Hunting* Sinyal Anomali

1. Di Wireshark, buat *display filter* untuk menyorot **deauth** massal.
2. Catat *timestamp* & *rate* kejadian sebagai indikator DoS nirkabel, lalu buat SOP mitigasi (ganti kanal, aktifkan 5 GHz, perkuat autentikasi, segmentasi tamu). 

---

## Kuis Cepat

1. Apa fungsi **Beacon** dan bagaimana kamu memfilternya di Wireshark? 
2. Jelaskan perbedaan WEP, WPA, dan WPA2-PSK secara singkat. 
3. Sebutkan tiga *BlueZ* tools dan kegunaannya. 
4. Mengapa *deauthentication* spike layak dianggap sinyal serangan? 

## Tugas Mini (menghubungkan Modul 2/3 ↔ 4)

* Buat **laporan analitik** dari satu sesi *capture* 10 menit yang memuat: jumlah Beacon/menit, jumlah deauth/menit, SSID yang terobservasi, dan rekomendasi *hardening* (tanpa melakukan serangan). Sertakan *filter* Wireshark yang dipakai dan tangkapan layar panel *Packet Details*.  

## Rubrik Penilaian

| Aspek              | Kriteria                                                       | Bobot |
| ------------------ | -------------------------------------------------------------- | ----- |
| Pemahaman 802.11   | Menjelaskan tipe frame & memetakan *filter* yang tepat         | 25%   |
| Analisis Wireshark | Menerapkan *display filter* dan menyajikan temuan yang relevan | 25%   |
| Bluetooth Basics   | Menginventaris perangkat sendiri & menjelaskan layanan (SDP)   | 20%   |
| Keamanan Praktis   | Rekomendasi *hardening* (WPA2-PSK/WPS/konfigurasi kanal)       | 20%   |
| Dokumentasi        | Logbook rapi + bukti (*screenshots*, filter, waktu)            | 10%   |

## Bacaan Sumber (kutipan relevan)

* Struktur bab Wi-Fi & Bluetooth pada sumber jaringan.  
* Keamanan Wi-Fi (WEP/WPA/WPA2). 
* Wireshark (panel & filter).  
* Tipe frame 802.11 & *display filter* `wlan.fc.subtype`.  
* *BlueZ* tools & dasar Bluetooth (pairing/piconet).   
* Mode keamanan Bluetooth & catatan kerentanan BlueBorne (konseptual).  
