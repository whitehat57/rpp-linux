# Silabus (K-13) — Linux & Network Basics for Ethical Hackers

## Identitas

* **Mata Pelajaran / Paket Keahlian**: Pengantar Linux & Dasar Jaringan untuk Keamanan Siber
* **Alokasi Waktu Total (estimasi)**: **±76 JP**
* **Karakter ranah kompetensi**: sikap, pengetahuan, keterampilan (sesuai Standar Proses). 

## Kompetensi Inti (KI) – ringkas

* **KI-1 (Sikap Spiritual)**: menghayati ajaran agama dalam perilaku belajar & etika digital.
* **KI-2 (Sikap Sosial)**: jujur, disiplin, tanggung jawab, kerja sama, dan taat etika penggunaan TI.
* **KI-3 (Pengetahuan)**: memahami konsep Linux, TCP/IP, firewall, nirkabel, dan protokol inti.
* **KI-4 (Keterampilan)**: mengaplikasikan perintah Linux, analisis paket, konfigurasi layanan/aturan secara aman.

> **Struktur Silabus** wajib memuat: Identitas mapel/sekolah/kelas; KI; KD; Materi Pokok; Pembelajaran; Penilaian; Alokasi Waktu; Sumber Belajar. Gunakan tabel berikut sebagai dokumen kerja. 

---

## Tabel Silabus per Modul

| Modul                          | **Kompetensi Dasar (KD)** – dirumuskan dari KI-3 & KI-4                                                                                                   | **Materi Pokok**                                                                                             | **Pembelajaran (inti ringkas)**                                                     | **Penilaian**                                                  | **Alokasi Waktu (JP)** | **Sumber Belajar**                                                                 |
| ------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------- | -------------------------------------------------------------- | ---------------------: | ---------------------------------------------------------------------------------- |
| **0. Orientasi & Etika**       | **3.1** Menjelaskan etika white-hat & batas legal; **4.1** Menyiapkan lab (VM/WSL/Termux), uji konektivitas/capture awal.                                 | Etika & legal; struktur kursus; instalasi alat (tcpdump/Wireshark); verifikasi IP/route.                     | Diskusi etika; setup lab; uji `ping`, `tcpdump` singkat; logbook & git.             | Observasi sikap, unjuk kerja setup, jurnal refleksi.           |                      4 | PDF: *Linux Basics for Hackers*, *Network Basics for Hackers* (file yang diunggah) |
| **1. Fondasi Linux**           | **3.2** Memahami filesystem, permission, proses, env; **4.2** Mengoperasikan perintah & menulis skrip bash sederhana.                                     | Navigasi berkas; `chmod/umask`; proses (`ps/top/kill/bg/fg`); variabel lingkungan; shebang & eksekusi skrip. | Demonstrasi + latihan terpandu; praktik lab 1–6; tugas skrip + `at`.                | Tes praktik CLI, penilaian produk skrip, kuis konsep.          |                      8 | *Linux Basics for Hackers* (CLI, proses, bash).                                    |
| **2. Dasar Jaringan (TCP/IP)** | **3.3** Menjelaskan IP, port, TCP/UDP, OSI; **4.3** Menganalisis paket dengan tcpdump/Wireshark & menghitung subnet dasar.                                | IP privat/publik, NAT/DHCP; 3-way handshake; filter tcpdump/BPF & Wireshark; CIDR dasar.                     | Capture handshake; display filter; latihan `ss/netstat`; soal subnetting.           | Unjuk kerja capture & analisis; kuis TCP/UDP/OSI.              |                     10 | *Network Basics for Hackers* (TCP/IP, tools).                                      |
| **3. Firewall (iptables)**     | **3.4** Memahami tables/chains/targets; **4.4** Menyusun rule dengan prinsip **default deny + whitelist** & mengevaluasi urutan rule.                     | FILTER/NAT/MANGLE/RAW; INPUT/OUTPUT/FORWARD; opsi `-A/-D/-L`, `-p/--dport`, `-s/-d`, `-j`.                   | Praktik menambah/menguji rule; studi urutan & flush; dokumentasi hasil.             | Unjuk kerja rule, bukti tes konektivitas, kuis konsep.         |                      8 | *Network Basics for Hackers* (iptables).                                           |
| **4. Wireless & Bluetooth**    | **3.5** Menjelaskan 802.11 & keamanan (WEP→WPA2-PSK) + dasar Bluetooth; **4.5** Mengamati frame 802.11 & inventaris perangkat Bluetooth secara **legal**. | Tipe frame (Beacon/Probe/Auth/Deauth); display filter; BlueZ tools; kebijakan WPS/PMKID (konseptual).        | Capture pasif; analisis deauth spike; inventaris BT sendiri; rekomendasi hardening. | Laporan analitik Wireshark; ceklis inventaris BT; kuis konsep. |                     10 | *Network Basics for Hackers* (Wi-Fi/BT).                                           |
| **5. Protokol Inti**           | **3.6** Menjelaskan ARP, DNS, SMB, SMTP, SNMP, HTTP/HTTPS; **4.6** Men-setup BIND/Samba mini & membaca paket khas.                                        | ARP table & netdiscover; `dig` & zona DNS; Samba dasar; alur SMTP; SNMP v1/2/3; status code HTTP.            | Lab per protokol; capture & interpretasi; hardening dasar.                          | Portofolio PCAP beranotasi; tes lisan; rubrik konfigurasi.     |                     12 | Kedua PDF (bab protokol & layanan).                                                |
| **6. Jaringan Khusus**         | **3.7** Mendeskripsikan CAN/SCADA/SDR (receive-only legal); **4.7** Menjalan­kan simulasi `vcan`+ICSim, membaca PCAP Modbus/DNP3, dan menerima ADS-B.     | `vcan`/can-utils/ICSim; gambaran Modbus/DNP3; `dump1090` (ADS-B) — **tanpa transmisi**.                      | Simulasi CAN; walk-through PCAP ICS; ADS-B lokal (web map).                         | Laporan observasi & threat-modeling ringkas.                   |                     12 | Kedua PDF (bab CAN/SCADA/RF).                                                      |
| **7. Capstone**                | **3.8** Merancang arsitektur lab & kebijakan keamanan; **4.8** Mengintegrasikan layanan (DNS/HTTP/SMB), firewall, & analisis paket ke laporan akhir.      | Desain mini-lab; iptables whitelist; capture DNS/HTTP/ARP; akses Samba RO; laporan akhir.                    | Proyek terpandu end-to-end + presentasi.                                            | Produk proyek + presentasi + rubrik komprehensif.              |                     12 | Semua modul & catatan praktikum.                                                   |

> **Penjelasan hukum format**: Silabus K-13 minimal memuat **Identitas mapel/sekolah/kelas; KI; KD; Materi Pokok; Pembelajaran; Penilaian; Alokasi Waktu; Sumber Belajar** (Lampiran Permendikbud 22/2016 Bab III.A.1 huruf a–j). **Silabus menjadi acuan penyusunan RPP**. 

---

## Rujukan regulasi

* **Permendikbud No. 22 Tahun 2016** tentang **Standar Proses** – pengertian, prinsip, dan **komponen silabus** (Lampiran Bab III). 
* **Panduan Pembelajaran dan Asesmen (Kurikulum Merdeka)** – **komponen Modul Ajar** (Tabel 3.4—3.5) bila sekolah menerapkan format Merdeka. 
