# FTP Server

## 1. Overview

FTP (File Transfer Protocol) adalah protokol standar untuk memindahkan file antara server dan klien. Praktikum ini memasang FTP server `vsftpd` di Ubuntu Server, membuat user FTP, mengatur port mode pasif, memblokir user, dan memberi izin tulis, lalu mengujinya dari klien Windows.

## 2. Lab Environment

* OS: Ubuntu Server
* Virtualization: Oracle VirtualBox
* Hostname server: `ubuntuarfa`
* IP server: `192.168.56.102`
* Layanan FTP: `vsftpd`
* Klien: Windows (File Explorer)

## 3. Installation

1. Masuk sebagai root.

   ```bash
   sudo -i
   ```

2. Update paket.

   ```bash
   apt update
   ```

3. Pasang `vsftpd` dan lanjutkan prosesnya sampai selesai.

   ```bash
   apt install vsftpd
   ```

4. Cek status layanan. Hasil yang diharapkan: `active (running)`.

   ```bash
   systemctl status vsftpd
   ```

5. Cek firewall, lalu izinkan layanan FTP.

   ```bash
   ufw status
   ufw allow ftp
   ```

## 4. Configuration

**a. Port data mode pasif**

Izinkan rentang port data di firewall, lalu muat ulang aturan UFW.

```bash
ufw allow 40000:50000/tcp
ufw reload
```

Buka `/etc/vsftpd.conf`:

```bash
nano /etc/vsftpd.conf
```

Tambahkan di bagian paling bawah file:

```ini
pasv_enable=YES
pasv_min_port=40000
pasv_max_port=50000
```

Simpan, lalu restart layanan:

```bash
systemctl restart vsftpd
```

**b. Buat user FTP**

Password diisi, sedangkan data lain (Full Name, Room Number, dst.) dilewati dengan `Enter`. Direktori `/home/arfaftp` dibuat otomatis dan menjadi folder FTP user tersebut.

```bash
adduser arfaftp
systemctl restart vsftpd
```

**c. Siapkan isi folder FTP**

```bash
cd /home
ls
mkdir /home/arfaftp/folder_baru
touch /home/arfaftp/file_baru
```

**d. Blokir user (opsional, untuk uji pembatasan akses)**

Tambahkan nama user di baris paling bawah `/etc/ftpusers`:

```bash
nano /etc/ftpusers
```

```
arfaftp
```

Untuk membuka blokir, hapus kembali baris `arfaftp` dari file tersebut.

**e. Beri izin tulis**

Cadangkan konfigurasi, lalu buka dengan nomor baris:

```bash
cp /etc/vsftpd.conf /etc/vsftpd.conf.backup
nano /etc/vsftpd.conf --linenumbers
```

Hapus tanda komentar pada baris 31 sehingga menjadi:

```ini
write_enable=YES
```

Simpan, lalu restart layanan:

```bash
systemctl restart vsftpd
```

## 5. Testing

1. **Login FTP.** Di Windows, buka File Explorer dan ketik `ftp://192.168.56.102` di address bar. Jendela *Log On As* harus muncul. Login dengan user `arfaftp`. Login berhasil, tetapi folder masih kosong sebelum file dibuat.
2. **Isi folder.** Setelah `folder_baru` dan `file_baru` dibuat di server, refresh File Explorer. Keduanya muncul di sisi klien.
3. **Blokir user.** Setelah `arfaftp` ditambahkan ke `/etc/ftpusers`, tutup File Explorer, buka lagi, lalu login dengan `arfaftp`. Login gagal (*Could not login to the FTP server with the user name and password specified*). Setelah pengujian, hapus `arfaftp` dari `/etc/ftpusers`.
4. **Tulis sebelum `write_enable=YES`.** Membuat atau menghapus file dari File Explorer gagal dengan `550 Permission denied`.
5. **Tulis sesudah `write_enable=YES`.** Salin file dari komputer lokal ke folder FTP dan sebaliknya. Keduanya berhasil.

| Pengujian | Hasil |
|-----------|-------|
| Login `arfaftp` | Berhasil |
| Folder dan file terlihat di klien | Berhasil |
| Login setelah user diblokir | Gagal (sesuai harapan) |
| Tulis file sebelum `write_enable=YES` | Gagal, `550 Permission denied` |
| Copy file dua arah setelah `write_enable=YES` | Berhasil |

## 6. Troubleshooting

Tidak ada kendala yang tercatat selama praktikum. Panduan di laporan jika login FTP gagal karena akses tidak diizinkan:

* **Gejala:** login dari Windows gagal padahal user dan password benar.
* **Penyelesaian:**
  1. Pastikan aturan firewall sudah ada: `ufw allow ftp` dan `ufw allow 40000:50000/tcp`, lalu `ufw reload`.
  2. Jika masih gagal, nonaktifkan firewall untuk sementara: `ufw disable`. Langkah ini hanya untuk pengujian di lab.

Catatan lain:

* `550 Permission denied` saat membuat atau menghapus file sebelum `write_enable=YES` diaktifkan adalah perilaku default, bukan error.
* Pastikan `pasv_min_port` dan `pasv_max_port` di `vsftpd.conf` sama dengan rentang port yang dibuka di UFW.

## 7. Screenshots

### Instalasi

| Langkah | Bukti |
|---------|-------|
| Login root | ![Login root](images/01-login-root.png) |
| `apt update` | ![apt update](images/02-apt-update.png) |
| `apt install vsftpd` | ![apt install vsftpd](images/03-apt-install-vsftpd.png) |
| Status `vsftpd` aktif | ![Status vsftpd](images/04-status-vsftpd.png) |
| `ufw status` | ![ufw status](images/05-ufw-status.png) |
| `ufw allow ftp` | ![ufw allow ftp](images/06-ufw-allow-ftp.png) |

### Konfigurasi

| Langkah | Bukti |
|---------|-------|
| `ufw allow 40000:50000/tcp` | ![ufw allow range](images/07-ufw-allow-port-range.png) |
| `ufw reload` | ![ufw reload](images/08-ufw-reload.png) |
| Buka `vsftpd.conf` | ![nano vsftpd.conf](images/09-nano-vsftpd-conf.png) |
| Konfigurasi pasif | ![pasv config](images/10-pasv-config.png) |
| Restart `vsftpd` | ![restart vsftpd](images/11-restart-vsftpd.png) |
| `adduser arfaftp` | ![adduser](images/13-adduser.png) |
| Restart setelah user dibuat | ![restart vsftpd](images/14-restart-vsftpd.png) |
| `ls` di `/home` | ![ls home](images/15-ls-home.png) |
| `mkdir` dan `touch` | ![mkdir touch](images/16-mkdir-touch.png) |
| Buka `/etc/ftpusers` | ![nano ftpusers](images/18-nano-ftpusers.png) |
| `arfaftp` ditambahkan ke `ftpusers` | ![ftpusers arfaftp](images/19-ftpusers-arfaftp.png) |
| Backup `vsftpd.conf` | ![backup conf](images/22-backup-conf.png) |
| Buka dengan `--linenumbers` | ![nano linenumbers](images/23-nano-linenumbers.png) |
| `write_enable=YES` | ![write_enable](images/24-write-enable.png) |
| Restart setelah `write_enable` | ![restart vsftpd](images/25-restart-vsftpd.png) |

### Pengujian

| Uji | Bukti |
|-----|-------|
| Jendela login FTP | ![Login window](images/12-login-window.png) |
| Folder dan file terlihat di klien | ![Explorer files](images/17-explorer-files.png) |
| Login gagal (user diblokir) | ![Login blocked](images/20-login-blocked.png) |
| Tulis ditolak sebelum `write_enable` | ![Permission denied](images/21-write-permission-denied.png) |
| Copy file ke folder FTP | ![Explorer copy](images/26-explorer-copy.png) |
| Copy file dari FTP ke lokal | ![Copy ke lokal](images/27-copy-ke-lokal.png) |
