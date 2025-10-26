# Modul 7 — Capstone & Praktikum Terpandu (Menyatukan Linux + Network Basics)

> Tujuan modul ini: menyatukan skill dari Modul 1–6 menjadi satu rangkaian praktik terpandu dan laporan akhir yang rapi. Semua aktivitas **hanya** pada lab yang kamu miliki/diizinkan.

## Hasil Belajar

Setelah modul ini kamu mampu:

1. Mendesain **mini-lab jaringan** (topologi, aset, layanan) dan **threat model** sederhana.
2. Membangun layanan dasar (DNS lokal/BIND, HTTP lokal, file sharing minimal via Samba) untuk kebutuhan uji.   
3. Menyusun **kebijakan firewall** (iptables) berbasis prinsip *default deny + whitelist* dan memahami dampak **urutan rule**.  
4. Melakukan **packet capture & analisis** menggunakan tcpdump/Wireshark (filter host/port/flags + display filter HTTP/DNS/ARP).   
5. Menyusun **laporan komprehensif** (temuan, bukti, rekomendasi hardening) yang dapat diaudit.

---

## Ringkasan Kegiatan Capstone

1. **Rancang Lab** kecil (1 server Linux + 1 klien; opsional 1 router VM).
2. **Siapkan Layanan**: BIND untuk DNS lokal; HTTP server sederhana; *share* baca-saja via Samba.  
3. **Atur Firewall (iptables)**: terapkan strategi whitelist dan uji pengaruh **ordering** rule.  
4. **Observasi Lalu Lintas**: tangkap dan analisis ARP/DNS/HTTP; dokumentasikan filter dan hasil.  
5. **Simulasi Skenario**: (a) akses web internal berhasil/terblokir sesuai kebijakan; (b) query DNS lokal vs publik; (c) akses *share* Samba; (d) deteksi *anomaly* sederhana dari log/capture.  
6. **Laporan Akhir**: ringkas arsitektur, kebijakan, bukti pcap/screenshot, dan rekomendasi penguatan.

---

## Bagian A — Desain Mini-Lab & Threat Model

### A1. Topologi & Aset

* **Server**: Linux (Kali/Ubuntu), menjalankan BIND + HTTP + Samba.
* **Klien**: Linux/Windows (pengujian konektivitas & browsing).
* **Jaringan**: gunakan NAT/Host-Only pada hypervisor untuk isolasi.

### A2. Threat Model Sederhana

* Aset: *records* DNS internal, *share* file, layanan HTTP lokal.
* Ancaman: konfigurasi firewall salah, *open share*, DNS salah resolusi, kebocoran data via HTTP plaintext.
* Kontrol: **iptables whitelist**, patch layanan, audit share, gunakan HTTPS untuk layanan sensitif (catatan perbedaan HTTP/HTTPS). 

---

## Bagian B — Membangun Layanan Dasar

### B1. DNS Lokal (BIND *mini*)

1. **Instal & konfigurasi**: edit `named.conf.options` dan `named.conf.local`; buat *forward* & *reverse* zone; restart layanan.   
2. **Uji**: `dig host.lab mx` / `dig @<dns-lokal> host.lab` dan dokumentasikan waktu & jawaban. 

### B2. HTTP Lokal

* Jalankan server HTTP sederhana (mis. Apache/Nginx/Python http.server) untuk bahan uji GET/POST & *status codes*. 

### B3. Samba (*read-only share*)

* Buat satu *share* baca-saja untuk uji akses dari klien; catat negosiasi awal paket SMB saat koneksi.  

---

## Bagian C — Kebijakan Firewall (iptables)

### C1. Prinsip & Pemeriksaan Awal

* Cek kebijakan & rules yang ada: `sudo iptables -L` (lihat **default policy**). 

### C2. Terapkan Strategi *Default Deny + Whitelist*

Contoh (sesuaikan IP & interface lab):

```bash
# start clean
sudo iptables -F
# default deny
sudo iptables -P INPUT DROP
sudo iptables -P OUTPUT DROP
# izinkan loopback & established
sudo iptables -A INPUT  -i lo -j ACCEPT
sudo iptables -A OUTPUT -o lo -j ACCEPT
sudo iptables -A INPUT  -m state --state ESTABLISHED,RELATED -j ACCEPT
sudo iptables -A OUTPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
# DNS lokal (UDP/TCP 53) ke IP DNS lab
sudo iptables -A OUTPUT -p udp -d <DNS_LAB_IP> --dport 53 -j ACCEPT
sudo iptables -A OUTPUT -p tcp -d <DNS_LAB_IP> --dport 53 -j ACCEPT
# HTTP lokal ke server lab
sudo iptables -A OUTPUT -p tcp -d <WEB_LAB_IP> --dport 80 -j ACCEPT
# CIFS/SMB internal bila diperlukan
sudo iptables -A OUTPUT -p tcp -d <SMB_LAB_IP> --dport 445 -j ACCEPT
```

> **Ordering matters**: pastikan rule ACCEPT lebih atas dari rule DROP spesifik port, jika ada. 

---

## Bagian D — Observasi & Analisis Paket

### D1. tcpdump (koleksi bukti)

* Contoh *capture* fokus:

  * DNS: `sudo tcpdump -i any port 53 -w dns.pcap`
  * HTTP: `sudo tcpdump -i any tcp port 80 -w http.pcap`
  * ARP: `sudo tcpdump -i any arp -c 50 -w arp.pcap`
    Gunakan filter host/port/flags sesuai kebutuhan investigasi. 

### D2. Wireshark (analisis)

* **Display filter** yang wajib dikuasai di capstone:

  * `dns` / `tcp.port==80` / `arp` / `ip.addr==<HOST>` / `tcp contains <string>`
  * Untuk HTTP, ambil 2–3 contoh **status codes**.   

---

## Bagian E — Skenario Uji (Step-by-Step & Bukti)

1. **Resolusi DNS Lokal vs Publik**

   * Set resolver ke **DNS lab** → akses `host.lab`, tangkap `dns.pcap`, tampilkan *answer section*.
   * Bandingkan bila resolver diubah ke publik (catat perbedaan). 

2. **Kebijakan Firewall Berhasil**

   * Dengan iptables aktif:
     a. Akses **HTTP lokal** → *sukses* (paket terlihat, rule ACCEPT cocok).
     b. Akses **HTTP eksternal** → *gagal* (terblokir oleh default DROP). Tunjukkan *ordering* rule pada output `iptables -L`.  

3. **Akses Samba Read-Only**

   * Mount *share* dan baca 1 file uji; tangkap negosiasi awal SMB. Jelaskan mengapa *read-only* lebih aman. 

4. **Inventaris & ARP Snapshot**

   * Ambil tabel ARP & capture `arp.pcap`; jelaskan siapa gateway, siapa server/klien.  

---

## Laporan Akhir (Struktur yang Disarankan)

1. **Ringkasan eksekutif** (½ halaman)
2. **Arsitektur & kebijakan** (diagram topologi, tabel rules inti iptables)
3. **Hasil uji per skenario** (DNS, HTTP, SMB, ARP) + bukti (cuplikan `tcpdump`/Wireshark)
4. **Analisis & temuan** (mis. efek *ordering* rule, manfaat DNS lokal untuk kontrol, risiko HTTP plaintext)
5. **Rekomendasi hardening** (contoh: pindahkan layanan sensitif ke HTTPS; audit *share*; dokumentasi & *change control* iptables) 
6. **Lampiran** (konfigurasi, versi tools, pcap ringkasan)

---

## Kuis Akhir (contoh)

1. Mengapa strategi *default deny + whitelist* lebih aman, dan apa trade-off-nya di operasional? 
2. Tunjukkan satu contoh **display filter** untuk masing-masing: DNS, HTTP, ARP dan jelaskan kegunaannya.   
3. Bagaimana **ordering** rule iptables dapat menyebabkan whitelist “kalah” oleh DROP? Jelaskan dengan 2 kalimat. 

---

## Rubrik Penilaian Capstone

| Aspek                 | Kriteria                                        | Bobot |
| --------------------- | ----------------------------------------------- | ----- |
| Desain & Threat Model | Topologi jelas, aset & risiko terpetakan        | 15%   |
| Implementasi Layanan  | BIND/HTTP/Samba berjalan & teruji               | 20%   |
| Firewall (iptables)   | Default deny + whitelist; memahami ordering     | 25%   |
| Capture & Analisis    | tcpdump/Wireshark tepat; filter & temuan akurat | 25%   |
| Laporan               | Bukti kuat, rekomendasi bisa ditindaklanjuti    | 15%   |

---

## Checklist Kelulusan (Pass/Fail)

* [ ] BIND lokal merespons query *forward* dan *reverse* (bukti `dig`). 
* [ ] HTTP lokal dapat diakses; HTTP eksternal terblokir sesuai kebijakan. 
* [ ] Samba *read-only share* berhasil diakses & dianalisis. 
* [ ] Ada **pcap** untuk DNS/HTTP/ARP dan analisisnya (display filter + insight).  
* [ ] Laporan akhir memenuhi struktur dan memuat rekomendasi hardening.
