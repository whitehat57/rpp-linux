# Modul 3 — Firewall Linux & Packet Filtering (iptables)

## Tujuan Pembelajaran

Setelah modul ini, kamu mampu:

* Menjelaskan konsep dasar firewall & peran iptables di Linux. 
* Memetakan arsitektur iptables: **tables**, **chains**, **match**, **targets** (ACCEPT/DROP/REJECT/LOG/RETURN).    
* Mengecek dan memahami **default policy** serta kapan perlu mengubahnya. 
* Membuat, meninjau, dan menghapus rule (opsi umum `-A/-D/-L`, `-s/-d/-j`, `-p/--dport`).   
* Memahami dampak **urutan rule** serta flushing untuk mulai dari “kertas kosong”.  

## Peta Keterkaitan

* Skill CLI & permission dari **Modul 1** dipakai langsung untuk mengelola rule dan menjalankan iptables dengan hak yang benar. (opsi `sudo` dan manajemen file/log dari modul sebelumnya relevan).
* Konsep TCP/UDP/port dari **Modul 2** jadi fondasi saat menulis rule berbasis port/protokol serta memverifikasi trafik dengan tcpdump/Wireshark.

---

## Materi Inti

### 1) Apa itu Firewall & iptables

* Firewall menyaring trafik masuk/keluar; iptables adalah firewall fleksibel berbasis CLI di Linux. 
* Dikembangkan proyek Netfilter; matang dan selevel fitur komersial. 

### 2) Arsitektur iptables

* **Tables**: FILTER (default), NAT (rewrite sumber/tujuan), MANGLE (ubah header), RAW (pengecualian connection tracking). 
* **Chains**: INPUT (ke host lokal), OUTPUT (keluar dari host), FORWARD (dilewatkan/routed). 
* **MATCH**: kondisi yang harus dipenuhi paket; **TARGETS**: aksi (ACCEPT/DROP/REJECT/LOG/RETURN). 

### 3) Default Policy

* Periksa kebijakan default tiap chain dengan `iptables -L`. Banyak sistem defaultnya ACCEPT; opsi “default BLOCK lalu whitelist” sangat aman namun butuh perawatan intensif. 

### 4) Opsi Perintah yang Paling Sering Dipakai

* `-A` (append), `-D` (delete), `-L` (list). 
* `-s` (source), `-d` (destination), `-j` (target). 
* `-p` (protokol) dan `--dport` (port tujuan) untuk rule berbasis layanan. 

### 5) Contoh Aturan Dasar & Pentingnya Urutan

* Blokir sumber tertentu (contoh 192.168.1.102) pada INPUT: gunakan `-A INPUT -s 192.168.1.102 -j DROP`. 
* Izinkan akses ke domain tertentu (OUTPUT ke amazon.com via TCP), dan **drop** 80/443 untuk selainnya — perhatikan **urutan**: rule yang cocok pertama kali “menang”.  

### 6) Meninjau & Mengosongkan Rules

* Lihat rules: `iptables -L` (list). Kosongkan (flush) semua rules: `iptables -F`. 

> Catatan: iptables juga punya tabel **NAT/MANGLE/RAW**; eksplorasi NAT (PREROUTING/POSTROUTING) dapat dilakukan setelah konsep FILTER mahir. 

---

## Praktikum (Langkah-demi-Langkah)

### Lab 1 — Cek Kebijakan Default & Kondisi Awal

1. Tinjau kebijakan & rules:

   ```bash
   sudo iptables -L
   ```

   Identifikasi default policy (ACCEPT/BLOCK) di tiap chain. 

### Lab 2 — Blokir Sumber Tertentu (INPUT)

1. Tambahkan rule blokir IP “nakal”:

   ```bash
   sudo iptables -A INPUT -s 192.168.1.102 -j DROP
   ```

   (Konsep: `-A` append, `-s` sumber, `-j` target DROP). 
2. Verifikasi daftar rules: `sudo iptables -L`. 

### Lab 3 — Whitelist 1 Domain, Drop Lainnya (OUTPUT)

1. Izinkan koneksi TCP ke domain tertentu:

   ```bash
   sudo iptables -A OUTPUT -p tcp -d amazon.com -j ACCEPT
   ```

   (Ingat, resolusi DNS dilakukan saat rule dibuat; lebih stabil pakai IP). 
2. Drop HTTP/HTTPS lainnya:

   ```bash
   sudo iptables -A OUTPUT -p tcp --dport 80 -j DROP
   sudo iptables -A OUTPUT -p tcp --dport 443 -j DROP
   ```

   3. Jelaskan di logbook kenapa **urutan** rule krusial agar whitelist tidak “tertutup” oleh DROP port 80/443. 

### Lab 4 — Bersih-Bersih (Flush)

1. Reset rules ke kosong:

   ```bash
   sudo iptables -F
   sudo iptables -L
   ```

   Pastikan kembali “clean slate” sebelum eksperimen berikutnya. 

> Tips ops-sec: saat uji **remote** (SSH), siapkan akses konsol/VM snapshot untuk menghindari lockout saat salah menulis rule.

---

## Kuis Cepat (contoh)

1. Sebutkan **empat table** di iptables dan fungsinya singkat. 
2. Bedakan chain **INPUT**, **OUTPUT**, dan **FORWARD**. 
3. Apa perbedaan target **DROP** vs **REJECT**? (Keduanya memblokir; REJECT kirim error balik). 
4. Kapan cocok memakai strategi “default BLOCK + whitelist”? Jelaskan trade-off-nya. 

## Tugas Mini (menghubungkan Modul 2 ↔ 3)

* Tulislah dua rule:
  a) **DROP** semua `OUTPUT` ke port 80/443,
  b) **ACCEPT** hanya satu IP tujuan (pilih IP yang kamu analisis di Modul 2).
  Dokumentasikan: urutan rule, hasil konektivitas, dan capture singkat `tcpdump` untuk menunjukkan paket yang di-drop vs yang lolos.

## Rubrik Penilaian

| Aspek                | Kriteria                                                           | Bobot |
| -------------------- | ------------------------------------------------------------------ | ----- |
| Pemahaman Arsitektur | Menjelas-kan tables/chains/match/targets dengan benar              | 20%   |
| Penyusunan Rule      | Menggunakan opsi `-A/-D/-L`, `-s/-d/-j`, `-p/--dport` dengan tepat | 25%   |
| Urutan & Logika      | Menjelaskan dan membuktikan pengaruh **ordering** rule             | 20%   |
| Verifikasi           | Menunjukkan bukti sebelum/sesudah (`iptables -L`, uji koneksi)     | 20%   |
| Dokumentasi          | Logbook rapi (tujuan, langkah, hasil, refleksi)                    | 15%   |

## Bacaan Sumber (kutipan relevan)

* Konsep firewall & iptables; arsitektur tables/chains/match/targets.    
* Default policy & pemeriksaan awal (`iptables -L`). 
* Opsi umum `-A/-D/-L`, `-s/-d/-j`; rule berbasis port.  
* Contoh whitelist domain + DROP 80/443, urutan rule krusial; flush `-F`.  
