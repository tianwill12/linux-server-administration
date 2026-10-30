# 06 - Web Server Administration (Nginx)

## 1. Tujuan

Memasang dan mengonfigurasi Nginx, membuat halaman web sederhana, dan mengujinya dari Windows.

## 2. Instalasi

```text
$ sudo apt install -y nginx

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

Reading package lists...
Building dependency tree...
Reading state information...
The following additional packages will be installed:
  nginx-common
Suggested packages:
  fcgiwrap nginx-doc
The following packages will be upgraded:
  nginx nginx-common
2 upgraded, 0 newly installed, 0 to remove and 218 not upgraded.
Prakonfigurasi paket ... 
Need to get 0 B/570 kB of archives.
After this operation, 5.120 B of additional disk space will be used.
(Sedang membaca basis data ... (Sedang membaca basis data ... 5%(Sedang membaca basis data ... 10%(Sedang membaca basis data ... 15%(Sedang membaca basis data ... 20%(Sedang membaca basis data ... 25%(Sedang membaca basis data ... 30%(Sedang membaca basis data ... 35%(Sedang membaca basis data ... 40%(Sedang membaca basis data ... 45%(Sedang membaca basis data ... 50%(Sedang membaca basis data ... 55%(Sedang membaca basis data ... 60%(Sedang membaca basis data ... 65%(Sedang membaca basis data ... 70%(Sedang membaca basis data ... 75%(Sedang membaca basis data ... 80%(Sedang membaca basis data ... 85%(Sedang membaca basis data ... 90%(Sedang membaca basis data ... 95%(Sedang membaca basis data ... 100%(Sedang membaca basis data ...  132234 berkas atau direktori telah terpasang.)
Preparing to unpack .../nginx_1.24.0-2ubuntu7.19_amd64.deb ...
Unpacking nginx (1.24.0-2ubuntu7.19) over (1.24.0-2ubuntu7.6) ...
Preparing to unpack .../nginx-common_1.24.0-2ubuntu7.19_all.deb ...
Unpacking nginx-common (1.24.0-2ubuntu7.19) over (1.24.0-2ubuntu7.6) ...
Sedang menata nginx-common (1.24.0-2ubuntu7.19) ... 
nginx.service is a disabled or a static unit not running, not starting it.
Sedang menata nginx (1.24.0-2ubuntu7.19) ... 
Not attempting to start NGINX, port 80 is already in use.
Processing triggers for man-db (2.12.0-4build2) ...
Processing triggers for ufw (0.36.2-6) ...
Rules updated for profile 'Apache Full'
Rules updated for profile 'Nginx Full'
Rules updated for profile 'OpenSSH'
Skipped reloading firewall

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
```

```text
$ nginx -v
nginx version: nginx/1.24.0 (Ubuntu)
```

```text
$ sudo systemctl enable --now nginx
Synchronizing state of nginx.service with SysV service script with /usr/lib/systemd/systemd-sysv-install.
Executing: /usr/lib/systemd/systemd-sysv-install enable nginx
Created symlink /etc/systemd/system/multi-user.target.wants/nginx.service → /usr/lib/systemd/system/nginx.service.
Job for nginx.service failed because the control process exited with error code.
See "systemctl status nginx.service" and "journalctl -xeu nginx.service" for details.
```

```text
$ systemctl is-active nginx
failed
```

```text
$ systemctl is-enabled nginx
enabled
```

## 3. Konfigurasi Site dan Halaman Web

| Perintah | Fungsi |
|---|---|
| `nginx -t` | Memeriksa sintaks konfigurasi |
| `systemctl reload nginx` | Memuat konfigurasi baru tanpa memutus koneksi |
| `curl -I` | Memeriksa header respons HTTP |

```text
$ cat /etc/nginx/sites-available/latihan
server {
    listen 80 default_server;
    server_name _;
    root /var/www/latihan;
    index index.html;
    location / { try_files $uri $uri/ =404; }
}
```

```text
$ cat /var/www/latihan/index.html
<!DOCTYPE html>
<html lang="id">
<head><meta charset="UTF-8"><title>Linux Server Administration</title></head>
<body style="font-family:sans-serif;text-align:center;margin-top:10%">
<h1>Halo dari Ubuntu Server 24.04</h1>
<p>Web server Nginx berjalan di tiansitorus.</p>
<p>Proyek: Linux Server Administration - tianwill12</p>
</body>
</html>
```

```text
$ sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

```text
$ sudo systemctl reload nginx
nginx.service is not active, cannot reload.
```

```text
$ curl -I http://localhost
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
Date: Fri, 30 Oct 2026 18:12:35 GMT
Server: Apache/2.4.58 (Ubuntu)
Content-Type: text/html; charset=UTF-8

```

```text
$ curl -s http://localhost | head -5
<!DOCTYPE html>
<html>
<head>
    <title>Coffee Espresso</title>

```

```text
$ sudo ss -tlnp | grep nginx
```

```text
$ sudo ufw status | grep 80
80/tcp                     ALLOW       Anywhere                  
80/tcp (v6)                ALLOW       Anywhere (v6)             
```

## 4. Pengujian dari Windows

Browser Windows membuka `http://172.20.10.2`. Halaman "Halo dari Ubuntu Server 24.04" muncul.

![Halaman Nginx](../screenshots/nginx.png)

## 5. Log Nginx

```text
$ sudo tail -n 5 /var/log/nginx/access.log
```

```text
$ sudo tail -n 5 /var/log/nginx/error.log
2026/10/30 18:12:32 [emerg] 5463#5463: bind() to 0.0.0.0:80 failed (98: Address already in use)
2026/10/30 18:12:32 [emerg] 5463#5463: bind() to [::]:80 failed (98: Address already in use)
2026/10/30 18:12:32 [emerg] 5463#5463: bind() to 0.0.0.0:80 failed (98: Address already in use)
2026/10/30 18:12:32 [emerg] 5463#5463: bind() to [::]:80 failed (98: Address already in use)
2026/10/30 18:12:32 [emerg] 5463#5463: still could not bind()
```

```text
$ ls -lh /var/log/nginx
total 12K
-rw-r----- 1 www-data adm    0 Apr  8  2026 access.log
-rw-r----- 1 www-data adm  636 Apr  2  2026 access.log.1
-rw-r----- 1 www-data adm 1007 Okt 30 18:12 error.log
-rw-r----- 1 www-data adm 2,1K Apr  2  2026 error.log.1
```

## 6. Analisis dan Kesimpulan

ANALISIS_DI_SINI

## 7. Troubleshooting

| Masalah | Penyebab | Solusi |
|---|---|---|
| Halaman tidak terbuka dari Windows | Port 80 diblokir UFW | `sudo ufw allow 80/tcp` |
| `nginx -t` gagal | Salah sintaks | Perbaiki file lalu ulangi `nginx -t` |
| `bind() to 0.0.0.0:80 failed` | Port 80 dipakai proses lain | Cek `ss -tlnp \| grep :80` |
