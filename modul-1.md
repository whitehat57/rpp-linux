# Modul 1 — Fondasi Linux untuk Hacking (Navigasi, Permission, Proses, & Bash Dasar)

## Tujuan Pembelajaran

Setelah modul ini kamu mampu:

* Menavigasi filesystem (buat, salin, pindah, hapus file/folder) dengan aman.
* Mengatur permission (chmod simbolik & numerik), memahami umask, serta SUID/SGID pada level praktis.
* Mengelola environment variables (PATH, export, unset) tanpa “merusak” sistem.
* Memantau & mengendalikan proses (ps/top/kill/renice, bg/fg), serta menjadwalkan tugas dengan `at`.
* Menulis dan mengeksekusi skrip bash pertama (shebang → izin eksekusi → jalankan).

## Peta Keterkaitan (dengan Modul 0 & modul berikutnya)

* Skill navigasi & permission dari modul ini menjadi dasar untuk menginstal & menjalankan tools di modul-modul selanjutnya. Contoh: memberi execute bit pada tool yang baru diunduh agar dapat dipakai.
* Manajemen proses & penjadwalan `at` dipakai untuk otomatisasi rekons/scan rutin di modul jaringan.

---

## Materi Inti

### 1) Navigasi & Manajemen File/Folder

* `touch`, `ls -l`, `mkdir`, `cd`, `cp`, `mv`, `rm`, `rmdir`, dan kehati-hatian `rm -r`.

### 2) Permission & Umask (termasuk SUID/SGID)

* Menambah izin eksekusi dengan gaya simbolik (`chmod u+x, o+x ...`).
* Default permission dikendalikan oleh `umask` (umum: 022 → file 644, dir 755).
* Gambaran SUID/SGID untuk kebutuhan eksekusi dengan hak sementara (konsep & cara set).

### 3) Environment Variables (PATH & kawan-kawan)

* Hindari menimpa `PATH` (gunakan append: `PATH=$PATH:/path/baru`), gunakan `export`/`unset` saat perlu.
* Format KEY=value, dibaca shell (bash); ini pondasi untuk kustomisasi lingkungan kerja.

### 4) Manajemen Proses (ps/top/kill/bg/fg/renice) + Penjadwalan

* Kill signal penting (SIGTERM, SIGKILL), `killall`, dan interaksi lewat `top`.
* Pindah proses ke background/foreground dengan `bg`/`fg` & temukan PID via `ps`.
* Jadwalkan tugas sekali jalan dengan `at` (format waktu fleksibel).

### 5) Bash Scripting Dasar

* Shebang `#!/bin/bash`, komentar `#`, echo, beri izin eksekusi (`chmod 755`), dan jalankan `./script`.

---

## Praktikum (Langkah demi langkah)

### Lab 1 — Navigasi & File Dasar

1. Buat file & folder, lalu jelajahi:
   `touch newfile && mkdir newdirectory && cd newdirectory && ls -l`
2. Salin & ubah nama:
   `cp ../newfile ./newfile_copy && mv newfile_copy renamed.txt`
3. Hapus aman: `rm renamed.txt` dan coba `rmdir newdirectory` (lihat pesan), bandingkan dengan `rm -r newdirectory`.

### Lab 2 — Permission & Umask

1. Tambah execute ke file alat: `chmod u+x, o+x tool.bin`.
2. Cek & set `umask`: `umask` → edit `.profile` untuk set `umask 022` (atau uji nilai lain di shell sementara).

### Lab 3 — Environment Variables (PATH)

1. Lihat PATH: `echo $PATH`.
2. Salah kaprah (jangan ditiru pada mesin utama): set `PATH=/tmp` lalu coba `ls` (akan error); pulihkan dengan `PATH=$PATH:/bin:/usr/bin` atau logout/login.
3. Tambahkan path alat: `PATH="$PATH:/opt/tools" && export PATH`.
4. Buat variabel sendiri, echo, export, dan unset.

### Lab 4 — Proses: ps/top/kill/bg/fg

1. Jalankan proses uji: `sleep 600 &` → catat PID (`echo $!`).
2. Pindah bg/fg & cari PID via `ps aux | grep sleep`; gunakan `fg %1` atau `bg %1`.
3. Buka `top`, identifikasi proses berat; coba `renice` via `top` (tekan `R`) atau `renice +10 -p <PID>`.
4. Matikan proses: `kill -15 <PID>` → jika bandel, `kill -9 <PID>` atau `killall -9 sleep`.

### Lab 5 — Bash Pertamamu

1. Buat file `HelloHackersArise` berisi:

   ```bash
   #!/bin/bash
   # Skrip pertama
   echo "Hello, Hackers-Arise!"
   ```
2. Jadikan executable & jalankan:
   `chmod 755 HelloHackersArise && ./HelloHackersArise`.

### Lab 6 — Menjadwalkan Tugas dengan `at`

1. Tulis skrip dummy `myscanningscript` (isi bebas).
2. Jadwalkan: `at now + 2 minutes` → ketik `/root/myscanningscript`, akhiri `Ctrl+D`.
   (Contoh format waktu lain tersedia, mis. `at 7:20pm June 25`.)

---

## Kuis Cepat (contoh)

1. Apa efek `umask 022` pada file & direktori baru?
2. Kapan memakai `SIGTERM` vs `SIGKILL`? Jelaskan perbedaannya.
3. Mengapa **menimpa** PATH berbahaya, dan bagaimana cara yang benar menambahkan path baru?

## Tugas Mini

* Tulis skrip bash yang menampilkan 5 proses teratas (berdasar CPU), simpan ke file log di `/var/tmp`, beri execute bit, lalu jadwalkan dengan `at` untuk berjalan sekali (pilih waktunya). Gunakan `ps`, `sort`, dan redirection. (Hint: konsep `at` & eksekusi skrip ada di materi.)

## Bacaan Sumber (kutipan relevan)

* Navigasi & file dasar: touch/mkdir/cp/mv/rm/rmdir.
* Permission, umask, & special bits (SUID/SGID).
* Environment variables & PATH.
* Proses (kill/killall/top/renice) & penjadwalan `at`.
* Bash scripting dasar (shebang → echo → chmod → run).
