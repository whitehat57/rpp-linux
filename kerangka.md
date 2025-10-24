# Kerangka Utama Course: Linux & Network Basics for Ethical Hackers

## Modul 0 — Orientasi & Etika

* Tujuan pembelajaran, mindset “white-hat”, dan alur belajar kursus. 

## Modul 1 — Fondasi Linux untuk Hacker

* Navigasi sistem berkas & manajemen file (touch, mkdir, cp, mv, rm).  
* Job control & proses (bg/fg, ps) + penjadwalan (at/cron).  
* Bash scripting dasar (pengantar otomasi). 
* Python scripting untuk tugas ofensif/otomasi (loop, banner grabbing sederhana).   

## Modul 2 — Dasar Jaringan (TCP/IP) untuk Hacker

* IP, kelas alamat, private vs public, DHCP, NAT, port, TCP/UDP, tiga langkah handshake, topologi, OSI. 
* Subnetting & CIDR (konsep, masker, notasi). 
* Network analysis: CLI tools, tcpdump, Wireshark (filter, follow stream, statistik). 

## Modul 3 — Firewall Linux & Packet Filtering

* Konsep firewall & arsitektur iptables (tables, chains, targets).    
* Praktik aturan dasar & prioritas rule; latihan flush/list.   

## Modul 4 — Wireless & Short-Range Networks

* Wi-Fi (protokol keamanan, adaptor, aircrack-ng, anatomi frame, filter Wireshark, teknik serangan).  
* Bluetooth (stack, tools, keamanan, BlueBourne). 

## Modul 5 — Deep Dive Protokol Inti

* ARP: fungsi, enumerasi host (netdiscover), potensi MiTM (arpspoof/Ettercap), latihan.   
* DNS: cara kerja, analisis paket, keamanan/DNSSEC, bangun BIND.  
* SMB: konsep, celah umum, bangun server Samba. 
* SMTP: model email, analisis paket, rekons & eksploit umum. 
* SNMP: versi, analisis, cracking community string. 
* HTTP/HTTPS: dasar, status code, auth testing dengan Burp. 

## Modul 6 — Jaringan Khusus & Lanjutan

* Automobile/CAN: protokol CAN, can-utils, vCAN, simulasi ICSim.   
* SCADA/ICS: vendor & protokol, isu keamanan. 
* Radio/SDR: terminologi, setup SDR pertama, intercept komunikasi, spoofing GPS (pengantar). 

## Modul 7 — Praktikum Terpandu (Capstone)

* Rancang firewall selektif (mis. hanya izinkan domain/port tertentu) & evaluasi dampak urutan rule. 
* Deploy BIND (DNS) mini & capture query-response untuk analisis. 
* Deploy Samba di Linux & uji enumerasi share. 
* Lab Wi-Fi: tangkap handshake & analisis di Wireshark (fokus edukasi). 

---
