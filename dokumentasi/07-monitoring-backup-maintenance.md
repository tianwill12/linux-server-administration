# 07 - Monitoring, Backup & Maintenance

## 1. Tujuan

Memantau CPU, RAM, disk, dan jaringan, mengelola log, serta membuat backup dan pemulihan sederhana.

## 2. Monitoring Sumber Daya

| Perintah | Fungsi |
|---|---|
| `htop` | Monitor proses interaktif |
| `free -h` | Penggunaan RAM dan swap |
| `df -h` | Penggunaan disk |
| `ss -s` | Ringkasan koneksi jaringan |
| `ip -s link` | Statistik paket per interface |

```text
$ free -h
               total        used        free      shared  buff/cache   available
Mem:           1,9Gi       872Mi       200Mi       3,8Mi       1,0Gi       1,0Gi
Swap:          1,9Gi       268Ki       1,9Gi
```

```text
$ df -h /
Filesystem                         Size  Used Avail Use% Mounted on
/dev/mapper/ubuntu--vg-ubuntu--lv  9,8G  7,1G  2,3G  76% /
```

```text
$ uptime
 18:14:26 up 57 min,  3 users,  load average: 0,20, 0,19, 0,13
```

```text
$ ps aux --sort=-%cpu | head -6
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
mysql        941  1.0 20.0 1786740 394316 ?      Ssl  17:17   0:37 /usr/sbin/mysqld
tiansit+    4493  0.3  0.4  14616  8612 pts/0    S+   18:02   0:02 ssh tiansitorus@172.20.10.2
tiansit+    2904  0.3  0.3  14972  7072 ?        S    17:35   0:07 sshd: tiansitorus@pts/0
root         740  0.2  0.4 242164  9620 ?        Ssl  17:17   0:09 /usr/bin/vmtoolsd
tiansit+    4543  0.2  0.3  14964  7116 ?        S    18:02   0:01 sshd: tiansitorus@pts/1
```

```text
$ ss -s
Total: 207
TCP:   13 (estab 3, closed 3, orphaned 0, timewait 2)

Transport Total     IP        IPv6
RAW	  1         0         1        
UDP	  4         3         1        
TCP	  10        8         2        
INET	  15        11        4        
FRAG	  0         0         0        

```

```text
$ ip -s link show | head -10
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    RX:  bytes packets errors dropped  missed   mcast           
       1711381   18334      0       0       0       0 
    TX:  bytes packets errors dropped carrier collsns           
       1711381   18334      0       0       0       0 
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 00:0c:29:a2:d7:9d brd ff:ff:ff:ff:ff:ff
    RX:  bytes packets errors dropped  missed   mcast           
    1033392181  727965      0       0       0       0 
```

```text
$ htop --version
htop 3.3.0
```

## 3. Manajemen Log

| Perintah | Fungsi |
|---|---|
| `journalctl -p err -b` | Log berlevel error sejak boot |
| `journalctl --disk-usage` | Ruang disk yang dipakai journal |
| `journalctl --vacuum-size` | Membatasi ukuran journal |
| `logrotate` | Rotasi log otomatis |

```text
$ sudo journalctl -p err -b -n 8 --no-pager
Okt 30 17:18:02 tiansitorus login[882]: PAM unable to dlopen(pam_lastlog.so): /usr/lib/security/pam_lastlog.so: cannot open shared object file: No such file or directory
Okt 30 17:18:02 tiansitorus login[882]: PAM adding faulty module: pam_lastlog.so
Okt 30 17:19:02 tiansitorus systemd[1]: Failed to start kolla-cron-container.service - docker kolla-cron-container.service.
Okt 30 17:19:02 tiansitorus systemd[1]: Failed to start kolla-fluentd-container.service - docker kolla-fluentd-container.service.
Okt 30 17:19:02 tiansitorus systemd[1]: Failed to start kolla-haproxy-container.service - docker kolla-haproxy-container.service.
Okt 30 17:19:02 tiansitorus systemd[1]: Failed to start kolla-kolla_toolbox-container.service - docker kolla-kolla_toolbox-container.service.
Okt 30 17:19:02 tiansitorus systemd[1]: Failed to start kolla-proxysql-container.service - docker kolla-proxysql-container.service.
Okt 30 18:12:35 tiansitorus systemd[1]: Failed to start nginx.service - A high performance web server and a reverse proxy server.
```

```text
$ sudo journalctl --disk-usage
Archived and active journals take up 99.6M in the file system.
```

```text
$ sudo journalctl --vacuum-size=100M
Vacuuming done, freed 0B of archived journals from /var/log/journal.
Vacuuming done, freed 0B of archived journals from /var/log/journal/bfcee9f7c5084f1f8907ccc70e304573.
Vacuuming done, freed 0B of archived journals from /run/log/journal.
```

```text
$ sudo du -sh /var/log
105M	/var/log
```

```text
$ ls /etc/logrotate.d | head -10
alternatives
apache2
apport
apt
bootlog
btmp
cloud-init
dpkg
mysql-server
nginx
```

```text
$ sudo logrotate -d /etc/logrotate.conf 2>&1 | head -8
warning: logrotate in debug mode does nothing except printing debug messages!  Consider using verbose mode (-v) instead if this is not what you want.

reading config file /etc/logrotate.conf
including /etc/logrotate.d
reading config file alternatives
reading config file apache2
reading config file apport
reading config file apt
```

## 4. Backup

Backup konfigurasi penting (Nginx, SSH, halaman web) ke satu file terkompresi:

```text
$ sudo tar czf ~/backup/backup-$(date +%F).tar.gz /etc/nginx /etc/ssh/sshd_config.d /var/www/latihan 2>&1 | head -3
tar: Removing leading `/' from member names
tar: Removing leading `/' from hard link targets
```

```text
$ ls -lh ~/backup
total 8,0K
-rw-r--r-- 1 root root 7,2K Okt 30 18:14 backup-2026-10-30.tar.gz
```

```text
$ tar tzf ~/backup/backup-$(date +%F).tar.gz | head -10
etc/nginx/
etc/nginx/koi-win
etc/nginx/fastcgi.conf
etc/nginx/modules-available/
etc/nginx/conf.d/
etc/nginx/snippets/
etc/nginx/snippets/fastcgi-php.conf
etc/nginx/snippets/snakeoil.conf
etc/nginx/sites-available/
etc/nginx/sites-available/latihan
```

## 5. Simulasi Recovery

Halaman web sengaja dihapus, lalu dipulihkan dari backup.

```text
$ sudo rm /var/www/latihan/index.html
```

```text
$ curl -sI http://localhost | head -1
HTTP/1.1 200 OK
```

```text
$ sudo tar xzf ~/backup/backup-$(date +%F).tar.gz -C / var/www/latihan
```

```text
$ curl -sI http://localhost | head -1
HTTP/1.1 200 OK
```

```text
$ ls -l /var/www/latihan
total 4
-rw-r--r-- 1 root root 342 Okt 30 18:12 index.html
```

## 6. Backup Otomatis dengan Cron

Perintah berikut menjadwalkan backup tiap hari pukul 02.00 dan menyimpan 7 backup terakhir:

```bash
(crontab -l 2>/dev/null; echo '0 2 * * * tar czf $HOME/backup/auto-$(date +\%F).tar.gz /var/www/latihan 2>/dev/null; ls -t $HOME/backup/auto-*.tar.gz | tail -n +8 | xargs -r rm') | crontab -
```

```text
$ crontab -l
no crontab for tiansitorus
```

## 7. Analisis dan Kesimpulan

ANALISIS_DI_SINI

## 8. Troubleshooting

| Masalah | Penyebab | Solusi |
|---|---|---|
| Disk cepat penuh | Log atau cache menumpuk | `apt clean`, `journalctl --vacuum-size`, `docker system prune` |
| `tar: Removing leading /` | Perilaku normal tar | Abaikan, bukan error |
