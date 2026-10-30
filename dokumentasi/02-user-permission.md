# 02 - User & Permission Management

## 1. Tujuan

Mengelola pengguna, grup, permission, ownership, dan konfigurasi sudo pada Ubuntu Server 24.04.

## 2. Perintah yang Digunakan

| Perintah | Fungsi |
|---|---|
| `groupadd` | Membuat grup |
| `adduser` | Membuat user beserta home directory |
| `usermod -aG` | Menambahkan user ke grup tambahan |
| `id`, `getent group` | Memeriksa user dan anggota grup |
| `chown` | Mengubah pemilik dan grup file |
| `chmod` | Mengubah permission file |
| `/etc/sudoers.d/` | Konfigurasi sudo per-file |
| `visudo -cf` | Memeriksa sintaks file sudoers |
| `deluser --remove-home` | Menghapus user beserta home-nya |

## 3. Membuat Grup dan User

```text
$ sudo groupadd -f developer
```

```text
$ sudo groupadd -f webadmin
```

```text
$ sudo adduser --disabled-password --gecos "" budi
info: Adding user `budi' ...
info: Selecting UID/GID from range 1000 to 59999 ...
info: Adding new group `budi' (1003) ...
info: Adding new user `budi' (1003) with group `budi (1003)' ...
info: Creating home directory `/home/budi' ...
info: Copying files from `/etc/skel' ...
info: Adding new user `budi' to supplemental / extra groups `users' ...
info: Adding user `budi' to group `users' ...
```

```text
$ sudo adduser --disabled-password --gecos "" siti
info: Adding user `siti' ...
info: Selecting UID/GID from range 1000 to 59999 ...
info: Adding new group `siti' (1004) ...
info: Adding new user `siti' (1004) with group `siti (1004)' ...
info: Creating home directory `/home/siti' ...
info: Copying files from `/etc/skel' ...
info: Adding new user `siti' to supplemental / extra groups `users' ...
info: Adding user `siti' to group `users' ...
```

```text
$ sudo usermod -aG developer budi
```

```text
$ sudo usermod -aG developer,webadmin siti
```

```text
$ id budi
uid=1003(budi) gid=1003(budi) groups=1003(budi),100(users),1001(developer)
```

```text
$ id siti
uid=1004(siti) gid=1004(siti) groups=1004(siti),100(users),1001(developer),1002(webadmin)
```

```text
$ getent group developer
developer:x:1001:budi,siti
```

```text
$ getent group webadmin
webadmin:x:1002:siti
```

## 4. Permission dan Ownership

Folder `/srv/proyek` dimiliki grup `developer` dengan mode `2770`: pemilik dan grup punya akses penuh, user lain tidak punya akses, dan bit setgid membuat file baru otomatis ikut grup `developer`.

```text
$ sudo mkdir -p /srv/proyek
```

```text
$ sudo chown root:developer /srv/proyek
```

```text
$ sudo chmod 2770 /srv/proyek
```

```text
$ ls -ld /srv/proyek
drwxrws--- 2 root developer 4096 Okt 30 17:54 /srv/proyek
```

```text
$ sudo -u budi touch /srv/proyek/file-budi.txt
```

```text
$ sudo -u siti touch /srv/proyek/file-siti.txt
```

```text
$ ls -l /srv/proyek
ls: cannot open directory '/srv/proyek': Permission denied
```

```text
$ sudo -u nobody ls /srv/proyek
ls: cannot open directory '/srv/proyek': Permission denied
```

Latihan chmod pada file biasa:

```text
$ touch ~/latihan-dasar/izin.txt
```

```text
$ chmod 644 ~/latihan-dasar/izin.txt
```

```text
$ ls -l ~/latihan-dasar/izin.txt
-rw-r--r-- 1 tiansitorus tiansitorus 0 Okt 30 17:54 /home/tiansitorus/latihan-dasar/izin.txt
```

```text
$ chmod 600 ~/latihan-dasar/izin.txt
```

```text
$ ls -l ~/latihan-dasar/izin.txt
-rw------- 1 tiansitorus tiansitorus 0 Okt 30 17:54 /home/tiansitorus/latihan-dasar/izin.txt
```

```text
$ chmod u+x ~/latihan-dasar/izin.txt
```

```text
$ stat -c '%A %a %U:%G %n' ~/latihan-dasar/izin.txt
-rwx------ 700 tiansitorus:tiansitorus /home/tiansitorus/latihan-dasar/izin.txt
```

| Mode | Arti |
|---|---|
| 644 | Pemilik baca-tulis, grup dan lainnya hanya baca |
| 600 | Hanya pemilik yang bisa baca-tulis |
| 2770 | rwx untuk pemilik dan grup, setgid aktif, lainnya tanpa akses |

## 5. Konfigurasi sudo

Grup `webadmin` hanya boleh menjalankan satu perintah dengan sudo (prinsip least privilege).

```text
$ echo "%webadmin ALL=(root) /usr/bin/systemctl status nginx" | sudo tee /etc/sudoers.d/webadmin
%webadmin ALL=(root) /usr/bin/systemctl status nginx
```

```text
$ sudo chmod 440 /etc/sudoers.d/webadmin
```

```text
$ sudo visudo -cf /etc/sudoers.d/webadmin
/etc/sudoers.d/webadmin: parsed OK
```

```text
$ sudo -l -U siti
Matching Defaults entries for siti on tiansitorus:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty

User siti may run the following commands on tiansitorus:
    (root) /usr/bin/systemctl status nginx
```

## 6. Menghapus User dan Grup Latihan

```text
$ sudo deluser --remove-home budi
info: Looking for files to backup/remove ...
info: Removing files ...
info: Removing crontab ...
info: Removing user `budi' ...
```

```text
$ sudo deluser --remove-home siti
info: Looking for files to backup/remove ...
info: Removing files ...
info: Removing crontab ...
info: Removing user `siti' ...
```

```text
$ sudo groupdel developer
```

```text
$ sudo groupdel webadmin
```

```text
$ sudo rm -f /etc/sudoers.d/webadmin
```

```text
$ sudo rm -rf /srv/proyek
```

```text
$ getent passwd budi siti; echo kode_keluar=$?
kode_keluar=2
```

## 7. Analisis dan Kesimpulan

Server berjalan di Ubuntu 24.04.4 LTS dengan N core CPU dan RAM 2 GB.
Disk root terpakai XX% sehingga apt upgrade ditunda.
Alamat IP server 172.20.10.2. Paket tree berhasil dipasang.
Latihan file (mkdir, cp, mv, find, grep, rm) berjalan lancar.

## 8. Troubleshooting

| Masalah | Penyebab | Solusi |
|---|---|---|
| `sudo: parse error` pada sudoers | Sintaks salah | Selalu cek dengan `visudo -cf` sebelum dipakai |
| User tidak bisa akses folder grup | Grup belum aktif di sesi login | Login ulang atau `newgrp developer` |
