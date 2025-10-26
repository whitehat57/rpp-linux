# Modul 5 — Deep Dive Protokol Inti (ARP, DNS, SMB, SMTP, SNMP, HTTP)

> Fokus modul: memahami cara kerja tiap protokol, membaca paketnya (tcpdump/Wireshark), menyiapkan layanan mini di Linux untuk **pembelajaran & hardening**, serta mengenali pola kerentanan **tanpa** praktik eksploitasi di sistem nyata yang tidak berizin.

## Tujuan Pembelajaran

Setelah modul ini kamu mampu:

* Menjelaskan fungsi & alur kerja ARP, DNS, SMB, SMTP, SNMP, dan HTTP serta **membaca paket** khasnya di Wireshark.      
* Melakukan **setup sederhana** server DNS (BIND) dan SMB (Samba) untuk eksperimen lokal.  
* Menggunakan utility bawaan/umum (mis. `arp`, `dig`, `snmpcheck`) untuk **observasi & inventaris** di lab sendiri.   
* Menjelaskan risiko umum (spoofing ARP, DNS tanpa autentikasi, community string SNMP lemah, dsb.) dan langkah mitigasi awal.   

---

## Peta Keterkaitan

* Konsep **tcp/ip, port, dan filter** dari Modul 2 dipakai untuk memfokuskan capture/analisis.
* Skill **Linux & permission** dari Modul 1 dipakai untuk instalasi layanan (BIND/Samba) serta pengelolaan berkas konfig.

---

## Materi Inti per Protokol

### A. ARP (Address Resolution Protocol)

* **Apa & bagaimana:** pemetaan IP ⇄ MAC; lihat tabel ARP dengan `arp -a` (Windows/Linux).  
* **Wireshark:** filter `arp` untuk melihat sender/target IP & MAC. 
* **Host discovery:** *gratuitous ARP* via `netdiscover -r 192.168.x.0/24` (lab jaringan sendiri/berizin). 
* **Risiko umum:** ARP spoofing → potensi MiTM (bahas sebagai wawasan deteksi). 

### B. DNS (Domain Name System)

* **Konsep:** domain, cara kerja, komponen, analisis paket, dan **DNSSEC** untuk otentikasi & integritas data.  
* **Praktik Linux:** `dig` (termasuk query MX) dan konfigurasi resolver `/etc/resolv.conf`.   
* **Build mini BIND:** instal `bind9`, edit `named.conf.options`, `named.conf.local`, buat **forward** & **reverse zone**, restart service.     

### C. SMB (Server Message Block)

* **Apa & untuk apa:** berbagi berkas/printer, interoperabilitas Windows–Linux via **Samba**. 
* **Risiko historis (literasi keamanan):** daftar exploit SMB (MS08-067, MS17-010/EternalBlue) sebagai contoh ancaman masa lalu—untuk konteks forensik & patching, **bukan** untuk disalahgunakan.  
* **Build Samba mini:** siapkan layanan untuk uji *share* dan analisis enumerasi di jaringan lab. 

### D. SMTP (Simple Mail Transfer Protocol)

* **Apa & model pemrosesan email:** alur pengiriman dan analisis paket SMTP. 
* **Kerentanan umum:** fokuskan ke kebijakan autentikasi, relay, dan header-based checks (untuk *hardening*). 

### E. SNMP (Simple Network Management Protocol)

* **Gambaran & versi:** v1/v2 (komunitas *public/private*) vs v3 (lebih aman). 
* **Wireshark & audit:** analisis paket, manfaat data MIB. 
* **Alat audit lab:** `snmpcheck -t <IP>` (membaca info dari MIB) dan `onesixtyone` (menguji community string—di lab sendiri & berizin).  

### F. HTTP/HTTPS

* **Dasar HTTP:** metode, header, **status codes**, dan perbedaan **HTTPS**. 
* **Praktik analisis:** baca GET/POST & TLS handshake di capture; pengujian autentikasi aplikatif dilakukan pada **lingkungan uji yang aman** (mis. DVWA/Juice Shop). 

> ⚠️ **Etika**: semua uji hanya di perangkat/jaringan milik sendiri atau yang **diizinkan tertulis**.

---

## Praktikum (Langkah-demi-Langkah, berorientasi defensif)

### Lab 5.1 — ARP Table & Paket ARP (observasi)

1. Tampilkan tabel ARP dan simpan ke logbook (`arp -a` / `arp -v`). 
2. Capture singkat dan filter `arp` di Wireshark; amati sender/target IP/MAC. 
3. (Opsional) *Passive host discovery* di lab: `netdiscover -r 192.168.X.0/24` dan bandingkan hasil dengan tabel ARP. 

### Lab 5.2 — DNS: `dig`, Resolver, & BIND mini

1. Lakukan query `dig <domain> mx` dan catat hasilnya. 
2. Ubah sementara `/etc/resolv.conf` untuk memakai DNS lokal/ publik (pahami urutan pencarian).  
3. **Setup BIND**: install `bind9`, edit `named.conf.options`, definisikan zone di `named.conf.local`, buat file **forward**/**reverse** dari templat, restart service; uji resolve internal.     

### Lab 5.3 — SMB: Samba untuk Latihan Enumerasi

1. Aktifkan **Samba** pada Kali/Ubuntu, buat satu *share* baca-saja untuk bahan uji. (Tujuan: observasi, bukan eksfiltrasi). 
2. Amati paket SMB saat akses *share* dari klien Windows/Linux; catat negosiasi & kredensial (jangan tampilkan rahasia—gunakan user dummy).
3. Diskusikan risiko historis (MS08-067/MS17-010) sebagai konteks **pentingnya patching**. 

### Lab 5.4 — SMTP: Alur Pengiriman Email (packet-level)

1. Kirim email uji dari klien lokal ke server lab; capture dan jelaskan perintah/protokol yang terlihat (HELO/EHLO, MAIL FROM, RCPT TO, DATA). 
2. Identifikasi status codes dan *banner* server; bahas *hardening* dasar (TLS, auth). 

### Lab 5.5 — SNMP: Inventaris & Community String (authorized-only)

1. Gunakan `snmpcheck -t <IP_lab>` untuk membaca informasi MIB; ringkas temuan (hardware/OS/uptime/perangkat lunak).  
2. Uji **onesixtyone** terhadap host lab untuk memastikan **community string** bukan nilai default; tekankan v3 sebagai opsi lebih aman.  

### Lab 5.6 — HTTP/HTTPS: Membaca Request/Response

1. Jalankan server HTTP lokal; capture GET/POST, identifikasi **status codes** & header penting. 
2. Buka situs HTTPS lab; jelaskan perbedaan handshake TLS (tanpa membongkar konten terenkripsi). 

---

## Kuis Cepat (contoh)

1. Mengapa ARP bisa dimanfaatkan untuk discovery/MiTM? Jelaskan singkat.  
2. Apa yang ditambahkan **DNSSEC** di atas DNS biasa? (dua poin kunci). 
3. Sebutkan **dua alasan** mengapa SMB harus selalu dipatch. (Rujuk kasus MS08-067/MS17-010). 
4. Sebutkan **tiga artefak** yang bisa diambil `snmpcheck` dari MIB saat community string diketahui. 
5. Bedakan **HTTP** vs **HTTPS** dari sisi observasi jaringan. 

---

## Tugas Mini (menyatukan semua)

Buat **laporan protokol** dari jaringan lab kamu:

* **ARP**: tangkapan 2 menit, jumlah request/reply, vendor NIC dominan. 
* **DNS**: uji `dig` ke domain publik & lokal BIND; bandingkan waktu & *resolver chain*.  
* **SMB**: deskripsikan *share* Samba dan paket negosiasi awal (tanpa rahasia). 
* **SMTP**: ringkas alur perintah saat pengiriman pesan uji. 
* **SNMP**: inventaris 1 host lab via `snmpcheck`; jika perlu, validasi community string bukan default dengan `onesixtyone`.  
* **HTTP/HTTPS**: tampilkan 3 contoh **status codes** dari server lokal dan satu sesi HTTPS. 

---

## Rubrik Penilaian

| Aspek              | Kriteria                                                    | Bobot |
| ------------------ | ----------------------------------------------------------- | ----- |
| Pemahaman Protokol | Menjelaskan fungsi & paket khas 6 protokol                  | 20%   |
| Analisis Paket     | Filter & interpretasi (Wireshark/tcpdump) akurat            | 25%   |
| Setup Layanan      | BIND/Samba minimal berjalan & terdokumentasi                | 20%   |
| Ops-Sec & Etika    | Seluruh uji pada lingkungan berizin + rekomendasi hardening | 20%   |
| Dokumentasi        | Logbook rapi (tujuan, langkah, bukti, insight)              | 15%   |

## Bacaan Sumber (kutipan relevan)

* **Struktur bab & cakupan protokol** (ARP, DNS, SMB, SMTP, SNMP, HTTP).      
* **ARP command & Wireshark**, *netdiscover*, kerentanan ARP.    
* **DNSSEC & BIND setup**; `dig` & resolver.     
* **SMB & Samba**, catatan eksploit historis (MS08-067, MS17-010).  
* **SMTP model**, **SNMP** (snmpcheck/onesixtyone), **HTTP/HTTPS** & status codes.    
