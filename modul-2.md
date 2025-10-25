# Modul 2 — Dasar Jaringan (TCP/IP, OSI, Tools Analisis, Subnetting & CIDR)

## Tujuan Pembelajaran

Setelah modul ini kamu mampu:

* Menjelaskan IP address (kelas A/B/C), public vs private, DHCP, NAT, port, TCP/UDP, **three-way handshake**, topologi jaringan, dan model OSI. 
* Memakai **CLI tools** (netstat/ss) serta memahami peran **sniffer** (tcpdump/Wireshark).  
* Meng-capture & mem-filter lalu menganalisis paket dengan **tcpdump** dan **Wireshark** (filters, follow stream, statistik). 
* Melakukan perhitungan dasar **subnetting** dan membaca **notasi CIDR**. 

## Peta Keterkaitan

* Skill shell dari **Modul 1** (pipeline/grep, permission, proses) langsung dipakai untuk filter koneksi `netstat/ss` dan otomasi capture. 
* Materi TCP/IP & OSI di modul ini jadi pondasi **Modul 3 (Firewall)** dan protokol lanjutan di **Modul 5**. 

---

## Materi Inti

### 1) IP Address, Kelas, dan Private vs Public

* IPv4 = 32-bit (4 oktet), contoh 192.168.1.101; kelas A/B/C dan rentangnya.  
* Alamat **private** (10.0.0.0/8, 172.16.0.0/16*, 192.168.0.0/16) dipakai di LAN dan diterjemahkan ke **public IP** oleh NAT.  

### 2) DHCP & NAT (sekilas praktik di Linux)

* **DHCP** memberi IP secara dinamis; contoh alur `dhclient eth0` dan verifikasi via `ifconfig`.   

### 3) Port, TCP vs UDP, dan 3-Way Handshake

* **TCP** melakukan 3 langkah: SYN → SYN/ACK → ACK sebelum transfer data; **UDP** connectionless dan efisien (contoh: DNS/NTP/SNMP).  

### 4) Topologi & Model OSI (untuk “mental map” serangan)

* Ringkas topologi bus/star, implikasi latency & ketersediaan. 
* Mnemonik OSI dan contoh vektor serangan per layer (phishing, hijacking session, MiTM, spoofing MAC, sniffing).  

### 5) Alat Analisis CLI: netstat & ss

* Menampilkan koneksi TCP (`-t`), UDP (`-u`), listening (`-l`), dan pemakaian `grep` untuk filter (mis. `| grep http`). **`ss`** memberi info lebih detail. 

### 6) Sniffer: tcpdump & Wireshark (dasar yang sering dipakai)

* **tcpdump**: capture cepat, `-w` simpan ke .pcap, filter BPF (host/port/flags; kombinasikan AND/OR/NOT).    
* **Wireshark** UI: Packet List/Details/Bytes dan **display filter** umum (`tcp`, `udp`, `http`, `ip.addr==`, `tcp.dstport==80`, `tcp contains ...`).     

### 7) Subnetting & CIDR (quickstart)

* Motivasi, subnet mask, dan **notasi CIDR** untuk efisiensi alokasi host.  

---

## Praktikum (Langkah-demi-Langkah)

### Lab 1 — Melihat 3-Way Handshake dengan tcpdump

1. Jalankan layanan web di Kali (contoh Apache), lalu capture host lawan:

```bash
sudo systemctl start apache2
sudo tcpdump host <IP_client>
```

Amati urutan **S**, **S.** (SYN/ACK), **.** (ACK) di output tcpdump saat klien membuka halaman.  

### Lab 2 — Filter Lanjutan di tcpdump

* Port only (verbose decode): `sudo tcpdump -vv dst port 80`
* TCP flags: `sudo tcpdump 'tcp[tcpflags]==tcp-syn'`
* Kombinasi & negasi: `sudo tcpdump host 192.168.0.114 and port 80`; `sudo tcpdump not host 192.168.0.114`    

### Lab 3 — Wireshark Display Filters (dasar → fokus)

1. Terapkan filter `tcp` / `udp` / `http`.
2. Saring satu alamat: `ip.addr==192.168.1.107`.
3. Lihat hanya HTTP dest port 80: `tcp.dstport==80`.
4. Cari string di payload: `tcp contains facebook`.    

### Lab 4 — netstat/ss + grep

* List koneksi & filter layanan:
  `netstat -a | grep http` → bandingkan output `ss`. Jelaskan bedanya di logbook. 

### Lab 5 — Subnetting Cepat (latihan manual)

* Diberi IP/mask, tentukan: network address, broadcast, range host, jumlah host. (Dasar konsep akan diperdalam lagi di modul lanjutan). 

---

## Kuis Cepat (contoh)

1. Jelaskan beda **TCP** vs **UDP** dan kapan memilih satu di atas yang lain. 
2. Sebutkan tiga **display filter** Wireshark yang sering dipakai dan fungsinya. 
3. Pada OSI, serangan apa yang umum di **Network layer**? 

## Tugas Mini (menghubungkan Modul 1 ↔ Modul 2)

* Tulis skrip bash yang:
  a) Menjalankan `ss` untuk daftar koneksi,
  b) Mem-filter hanya koneksi ke `:80`/`:443` (gunakan `grep`),
  c) Menyimpan ke file log harian di `/var/tmp`,
  d) (Opsional) Capture singkat `tcpdump -c 100 -w web.pcap` dan analisis `tcp` di Wireshark.
  Skrip ini memanfaatkan pipeline shell dari modul sebelumnya & praktik analisis jaringan di modul ini.  

## Rubrik Penilaian

| Aspek               | Kriteria                                                             | Bobot |
| ------------------- | -------------------------------------------------------------------- | ----- |
| Konsep TCP/IP & OSI | Menjawab benar 3-5 pertanyaan konsep (kelas IP, NAT/DHCP, handshake) | 25%   |
| CLI Networking      | `netstat/ss` + filter grep berfungsi & terdokumentasi                | 20%   |
| tcpdump             | Capture + filter (host/port/flags) + penjelasan singkat hasil        | 25%   |
| Wireshark           | Memakai 2–3 display filter tepat sasaran                             | 15%   |
| Subnetting          | Hitung network/broadcast/jumlah host dari 1 skenario                 | 15%   |

## Bacaan Sumber (kutipan relevan)

* **Network Basics** (IP, kelas, DHCP/NAT, port, TCP/UDP, topologi, OSI).   
* **Network Analysis** (CLI tools, sniffers, tcpdump, Wireshark, filters).    
* **Subnetting & CIDR** (motivasi, mask, notasi).  
* **Linux Basics for Hackers** (operasional DHCP & DNS/dig untuk rekons).  
