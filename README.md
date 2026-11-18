# Ubuntu Server Administration

Kumpulan dokumentasi praktikum administrasi jaringan dengan **Ubuntu Server** pada Oracle VirtualBox. Setiap folder berisi README berisi langkah instalasi, konfigurasi, pengujian dari klien Windows, kendala yang ditemui, dan screenshot buktinya.

Disusun oleh **Danish Arfa Ramadhan**, D4 Teknologi Rekayasa Internet, Sekolah Vokasi, Universitas Gadjah Mada.

## Daftar Praktikum

| Unit | Layanan | Software | Port utama | Dokumentasi |
|------|---------|----------|------------|-------------|
| 2 | File sharing | Samba | 139, 445 (TCP) | [samba server](./samba%20server) |
| 3 | File transfer | vsftpd | 21, 40000-50000 (TCP, mode pasif) | [FTP server](./FTP%20server) |
| 5 | Web server | Apache2 | 80 (TCP) | [Web server](./Web%20server) |
| 6 | DNS | BIND9 | 53 (TCP/UDP) | [DNS server](./DNS%20server) |
| 7 | Proxy | Squid | 3128 (TCP) | [Proxy server](./Proxy%20server) |
| 9 | Email | Postfix dan Dovecot | 25 (SMTP), 143 (IMAP) | [Mail server](./Mail%20server) |

## Ringkasan Tiap Unit

* **Samba (Unit 2):** dua folder share (`share1`, `share2`) dengan hak akses baca/tulis yang saling berlawanan untuk `user1` dan `user2`, diuji dari File Explorer Windows.
* **FTP (Unit 3):** vsftpd dengan mode pasif, pembuatan user FTP, pemblokiran user lewat `/etc/ftpusers`, dan izin tulis dengan `write_enable=YES`.
* **Web (Unit 5):** instalasi Apache2, virtual hosting dengan `DocumentRoot` baru, dan sub folder `/about`.
* **DNS (Unit 6):** zona forward dan reverse untuk domain lokal `arfakerensekalii.com`, diuji dengan `nslookup`, `dig`, `ping`, dan browser.
* **Proxy (Unit 7):** Squid sebagai proxy untuk klien Windows, dilengkapi pemblokiran situs lewat ACL domain.
* **Mail (Unit 9):** pengiriman email antar user lokal dengan Postfix, dan akses email lewat IMAP (Dovecot) serta perintah `mail`.

## Lab Environment

* **Hypervisor:** Oracle VirtualBox
* **Server:** Ubuntu Server 24.04 LTS (Noble), hostname `ubuntuarfa`
* **Jaringan server:** dua adapter, yaitu NAT (`enp0s3`, akses internet untuk instalasi paket) dan Host-Only (`enp0s8`, `192.168.56.102/24`, jalur komunikasi dengan klien)
* **Klien:** Windows 11 (File Explorer, Command Prompt, browser)

```
+-------------------+        Host-Only        +----------------------+
|  Windows (klien)  | <---------------------> |  Ubuntu Server (VM)  |
|  192.168.56.x     |   192.168.56.0/24       |  ubuntuarfa          |
+-------------------+                         |  192.168.56.102      |
                                              +----------+-----------+
                                                         | NAT
                                                         v
                                                     Internet
```

## Urutan dan Keterkaitan

Setiap unit bisa dikerjakan terpisah, kecuali:

* Pengujian browser di **DNS (Unit 6)** memakai situs dari **Web server (Unit 5)**, jadi Apache harus aktif.
* Aturan firewall UFW bertambah dari unit ke unit (Samba, FTP, Apache, BIND9, Squid), sehingga `ufw status` di unit berikutnya menampilkan aturan unit sebelumnya.

## Struktur Repositori

```
ubuntu-server-administration/
├── samba server/    # Unit 2
├── FTP server/      # Unit 3
├── Web server/      # Unit 5
├── DNS server/      # Unit 6
├── Proxy server/    # Unit 7
└── Mail server/     # Unit 9
```

Tiap folder berisi `README.md` dan folder `images/` untuk screenshot.

## Catatan Keamanan

Konfigurasi di repositori ini dibuat untuk **lingkungan lab tertutup** dan tidak layak dipakai di produksi. Beberapa contohnya:

* Samba: `chmod 777` pada folder share.
* FTP: koneksi tanpa enkripsi (password terkirim plaintext).
* Proxy: `http_access allow all`.
* Mail: `disable_plaintext_auth = no` tanpa SSL/TLS.

Untuk penggunaan nyata, aktifkan enkripsi (FTPS/SFTP, TLS untuk mail), batasi akses lewat ACL, dan gunakan izin file seminimal mungkin.

## Referensi

Daftar bacaan tiap unit ada di laporan praktikum masing-masing. Dokumentasi resmi yang relevan:

* [Samba](https://www.samba.org/samba/what_is_samba.html)
* [Ubuntu: Squid proxy server](https://ubuntu.com/server/docs/proxy-servers-squid)
* [Ubuntu: Install and configure Apache](https://ubuntu.com/tutorials/install-and-configure-apache)
