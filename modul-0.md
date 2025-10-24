# Modul 0 — Orientasi, Etika, & Setup Lab

## Ringkasan

Modul pengantar untuk menyamakan persepsi: tujuan belajar, etika “white-hat”, ruang lingkup kursus, dan menyiapkan lab supaya semua praktik di modul selanjutnya berjalan mulus (Kali/WSL/Termux, Wireshark/tcpdump, SSH).

## Hasil Belajar (Learning Outcomes)

Setelah modul ini, peserta mampu:

1. Menjelaskan batas etika dan legalitas aktivitas security testing (white-hat mindset).
2. Memetakan alur belajar kursus dan dependensi antarmodul.
3. Menyiapkan lingkungan lab minimal:

   * **Opsi A**: Kali Linux (bare metal/VM/WSL).
   * **Opsi B**: Linux lain + toolset (Wireshark, tcpdump, nmap).
   * **Opsi C**: Termux (Android) untuk latihan dasar CLI & jaringan.
4. Menguji konektivitas, membuat catatan eksperimen (logbook), dan versioning materi (Git).

## Prasyarat

* Familiar dengan komputer dasar.
* Akses ke salah satu: PC/Laptop (Windows/macOS/Linux) **atau** Android dengan Termux.
* Koneksi internet untuk mengunduh paket.

## Peta Jalur Kursus

* **Modul 1 (Linux)** → dasar CLI & scripting yang dipakai di **Modul 2 (TCP/IP)** dan **Modul 3 (Firewall)**.
* **Modul 2 (TCP/IP)** → prasyarat **Modul 4 (Wireless)** & **Modul 5 (Protokol Inti)**.
* **Modul 5** memperdalam protokol yang akan sering dianalisis di capstone **Modul 7**.
* Semua praktik selalu kembali ke skill Linux (permission, proses, log) dari **Modul 1**.

## Aturan Etika & Legal (Wajib!)

* Uji hanya sistem milik sendiri atau yang **punya izin tertulis**.
* Hindari data pribadi; jangan mengganggu layanan produksi.
* Dokumentasikan semua langkah; transparan dan bisa diaudit.
* Tujuan: **pembelajaran & perbaikan keamanan**, bukan penyalahgunaan.

## Setup Lingkungan (Pilih Salah Satu atau Kombinasi)

### A. Kali Linux (VM/WSL/Native)

1. **VM**: Install VirtualBox/VMware → import ISO Kali.
2. **WSL (Windows 10/11)**: aktifkan WSL → install Kali di Microsoft Store → buka terminal.
3. Tambah tool dasar:

   ```bash
   sudo apt update && sudo apt -y install wireshark tshark tcpdump nmap git curl
   ```
4. (Opsional) Non-root user & SSH:

   ```bash
   sudo adduser student && sudo usermod -aG sudo student
   sudo apt -y install openssh-server && sudo systemctl enable --now ssh
   ```

### B. Distro Linux Lain (Ubuntu/Debian/Arch dsb.)

```bash
sudo apt update && sudo apt -y install wireshark tshark tcpdump nmap git curl
# Arch: sudo pacman -S wireshark-qt tshark tcpdump nmap git curl
```

### C. Termux (Android)

```bash
pkg update && pkg upgrade -y
pkg install -y tsu git curl python openssh
# untuk jaringan dasar:
pkg install -y nmap tcpdump
termux-setup-storage
```

> Catatan: Beberapa fitur sniffing low-level di Android dibatasi; gunakan untuk latihan dasar CLI & pemahaman protokol, bukan capture interface yang dibatasi OS.

## Verifikasi Awal (Hello, Packets!)

1. **Cek IP & route**

   ```bash
   ip a
   ip r
   ```
2. **Uji koneksi**
   `ping -c 4 1.1.1.1` dan `ping -c 4 example.com`
3. **Tangkap paket singkat (butuh sudo/cap)**

   ```bash
   sudo tcpdump -i any -c 30
   ```
4. **Buka Wireshark** → filter `icmp` → amati request/reply.

## Workflow Belajar & Dokumentasi

* Buat **folder kursus** dan **logbook.md** untuk tiap latihan (tujuan, langkah, output, “what I learned”).
* Gunakan Git:

  ```bash
  git init
  echo "# Logbook Kursus" > README.md
  git add .
  git commit -m "init: modul-0 setup"
  ```
* Struktur saran:

  ```
  course/
  ├─ modul-0/
  │  ├─ logbook.md
  │  └─ screenshot/…
  ├─ modul-1/
  └─ …
  ```

## Penilaian & Rubrik Modul 0

| Aspek             | Kriteria                                                           | Bobot |
| ----------------- | ------------------------------------------------------------------ | ----- |
| Etika & Kepatuhan | Memahami batas izin, menyebutkan 3 risiko legal, komitmen tertulis | 25%   |
| Kesiapan Lab      | Tool terpasang & teruji (tcpdump/Wireshark/nmap/SSH)               | 35%   |
| Dokumentasi       | Logbook runut (tujuan, langkah, hasil, refleksi) + tangkapan layar | 25%   |
| Versioning        | Repo Git lokal/remote rapi, commit message jelas                   | 15%   |

**Lulus Modul 0 jika:** semua tool kunci terpasang & verifikasi konektivitas + satu capture sederhana tersimpan dan terdokumentasi.

## Tugas Bacaan (Dari Dua Sumber)

* **Linux Basics for Hackers**: bab pengantar Linux/CLI, filesystem, permission, proses (untuk bekal Modul 1).
* **Network Basics for Hackers**: pengantar TCP/IP, OSI, alat analisis jaringan (untuk bekal Modul 2).

## Aktivitas Praktik (Wajib Kumpul)

1. **Checklist Setup**: kirim output `ip a`, `ip r`, dan versi tool (`wireshark --version`, `tcpdump --version`, `nmap --version`) di logbook.
2. **Mini-Capture**: simpan `tcpdump -i any -c 50 -w first.pcap` lalu buka di Wireshark, tulis 3 hal yang kamu pelajari (mis. perbedaan ARP vs ICMP).
3. **Pernyataan Etika**: 3–5 poin komitmen pribadi (izin, non-disruptive, no PII, full logs).

## Kriteria Keamanan Operasional (OpsSec) Sejak Awal

* Pisahkan **lab network** dari jaringan kerja/kampus/rumah bila memungkinkan (VM NAT/Host-Only).
* Jangan jalankan tool agresif di jaringan publik tanpa izin.
* Simpan PCAP berisi data pribadi **hanya** di folder lokal terenkripsi (opsional: `gocryptfs/cryptsetup`).
