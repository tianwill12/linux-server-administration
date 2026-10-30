# 01 - Basic Server Administration

## 1. Tujuan

Mempelajari dan mempraktikkan administrasi dasar pada Ubuntu Server 24.04.4 LTS:

- Pemeriksaan informasi sistem
- Pemeriksaan penggunaan CPU, RAM, disk, dan jaringan
- Manajemen paket dengan APT
- Manajemen direktori dan file
- Monitoring sumber daya dasar

## 2. Lingkungan

| Komponen | Keterangan |
|---|---|
| OS | Ubuntu Server 24.04.4 LTS |
| Virtualisasi | VMware Workstation |
| RAM | 2 GB |
| Disk | ± 9,8 GB |
| User | tiansitorus |
| Akses | SSH dari PowerShell Windows |

## 3. Pemeriksaan Informasi Sistem

| Perintah | Fungsi |
|---|---|
| `hostnamectl` | Hostname, OS, kernel, dan virtualisasi |
| `lsb_release -a` | Versi distribusi Ubuntu |
| `uname -r` | Versi kernel |
| `uptime` | Lama server menyala dan load average |
| `whoami` | User yang sedang aktif |
| `timedatectl` | Zona waktu dan status sinkronisasi waktu |

### Hasil

```text
$ hostnamectl
 Static hostname: tiansitorus
       Icon name: computer-vm
         Chassis: vm 🖴
      Machine ID: bfcee9f7c5084f1f8907ccc70e304573
         Boot ID: ac48558dbffb40eab9d863bfcefb40db
  Virtualization: vmware
Operating System: Ubuntu 24.04.4 LTS
          Kernel: Linux 6.8.0-107-generic
    Architecture: x86-64
 Hardware Vendor: VMware, Inc.
  Hardware Model: VMware Virtual Platform
Firmware Version: 6.00
   Firmware Date: Thu 2020-11-12
    Firmware Age: 5y 11month 2w 3d
```

```text
$ lsb_release -a
Distributor ID:	Ubuntu
Description:	Ubuntu 24.04.4 LTS
Release:	24.04
Codename:	noble
```

```text
$ uname -r
6.8.0-107-generic
```

```text
$ uptime
 17:44:33 up 27 min,  2 users,  load average: 0,08, 0,02, 0,03
```

```text
$ whoami
tiansitorus
```

```text
$ timedatectl
               Local time: Jum 2026-10-30 17:44:34 UTC
           Universal time: Jum 2026-10-30 17:44:34 UTC
                 RTC time: Jum 2026-10-30 17:44:33
                Time zone: Etc/UTC (UTC, +0000)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

## 4. Pemeriksaan CPU, RAM, Disk, dan Jaringan

| Perintah | Fungsi |
|---|---|
| `lscpu`, `nproc` | Informasi dan jumlah core CPU |
| `free -h` | Penggunaan RAM dan swap |
| `df -h` | Penggunaan disk per filesystem |
| `lsblk` | Struktur disk dan partisi |
| `ip -br address` | Ringkasan alamat IP tiap interface |
| `docker system df` | Penggunaan disk oleh Docker |

### Hasil

```text
$ lscpu | head -15
Architecture:                            x86_64
CPU op-mode(s):                          32-bit, 64-bit
Address sizes:                           45 bits physical, 48 bits virtual
Byte Order:                              Little Endian
CPU(s):                                  2
On-line CPU(s) list:                     0,1
Vendor ID:                               GenuineIntel
Model name:                              12th Gen Intel(R) Core(TM) i5-1235U
CPU family:                              6
Model:                                   154
Thread(s) per core:                      1
Core(s) per socket:                      1
Socket(s):                               2
Stepping:                                4
BogoMIPS:                                4991,98
```

```text
$ nproc
2
```

```text
$ free -h
               total        used        free      shared  buff/cache   available
Mem:           1,9Gi       806Mi       303Mi       3,8Mi       978Mi       1,1Gi
Swap:          1,9Gi          0B       1,9Gi
```

```text
$ df -h
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                              193M  1,3M  191M   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv  9,8G  6,2G  3,2G  67% /
tmpfs                              961M     0  961M   0% /dev/shm
tmpfs                              5,0M     0  5,0M   0% /run/lock
/dev/sda2                          1,8G  200M  1,5G  13% /boot
tmpfs                              193M   12K  193M   1% /run/user/1000
```

```text
$ lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda                         8:0    0   20G  0 disk 
├─sda1                      8:1    0    1M  0 part 
├─sda2                      8:2    0  1,8G  0 part /boot
└─sda3                      8:3    0 18,2G  0 part 
  └─ubuntu--vg-ubuntu--lv 252:0    0   10G  0 lvm  /
sr0                        11:0    1  3,1G  0 rom  
```

```text
$ ip -br address
lo               UNKNOWN        127.0.0.1/8 ::1/128 
ens33            UP             172.20.10.2/28 metric 100 fe80::20c:29ff:fea2:d79d/64 
dummy0           UNKNOWN        fe80::7cb4:1ff:fef2:bfba/64 
```

```text
$ sudo docker system df
TYPE            TOTAL     ACTIVE    SIZE      RECLAIMABLE
Images          0         0         0B        0B
Containers      0         0         0B        0B
Local Volumes   0         0         0B        0B
Build Cache     0         0         0B        0B
```

## 5. Manajemen Paket (APT)

| Perintah | Fungsi |
|---|---|
| `apt update` | Memperbarui daftar paket |
| `apt list --upgradable` | Melihat paket yang dapat di-upgrade |
| `apt policy tree` | Versi terpasang dan tersedia |
| `apt install -y tree` | Memasang paket `tree` |
| `dpkg -l` | Memeriksa paket terpasang |
| `apt autoremove`, `apt clean` | Membersihkan paket dan cache untuk menghemat disk |

### Hasil

```text
$ sudo apt update

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

Hit:1 http://id.archive.ubuntu.com/ubuntu noble InRelease
Hit:2 http://id.archive.ubuntu.com/ubuntu noble-updates InRelease
Hit:3 http://id.archive.ubuntu.com/ubuntu noble-backports InRelease
Hit:4 https://download.docker.com/linux/ubuntu noble InRelease
Hit:5 http://security.ubuntu.com/ubuntu noble-security InRelease
Reading package lists...
Building dependency tree...
Reading state information...
222 packages can be upgraded. Run 'apt list --upgradable' to see them.
```

```text
$ apt list --upgradable 2>/dev/null | head -15
Listing...
amd64-microcode/noble-updates,noble-security 3.20251202.1ubuntu0.24.04.1 amd64 [upgradable from: 3.20250311.1ubuntu0.24.04.1]
apache2-bin/noble-updates,noble-security 2.4.58-1ubuntu8.17 amd64 [upgradable from: 2.4.58-1ubuntu8.11]
apache2-data/noble-updates,noble-security 2.4.58-1ubuntu8.17 all [upgradable from: 2.4.58-1ubuntu8.11]
apache2-utils/noble-updates,noble-security 2.4.58-1ubuntu8.17 amd64 [upgradable from: 2.4.58-1ubuntu8.11]
apache2/noble-updates,noble-security 2.4.58-1ubuntu8.17 amd64 [upgradable from: 2.4.58-1ubuntu8.11]
apparmor/noble-updates 4.0.1really4.0.1-0ubuntu0.24.04.9 amd64 [upgradable from: 4.0.1really4.0.1-0ubuntu0.24.04.5]
apport-core-dump-handler/noble-updates 2.28.3-0ubuntu0.1 all [upgradable from: 2.28.1-0ubuntu3.8]
apport/noble-updates 2.28.3-0ubuntu0.1 all [upgradable from: 2.28.1-0ubuntu3.8]
base-files/noble-updates 13ubuntu10.5 amd64 [upgradable from: 13ubuntu10.4]
bind9-dnsutils/noble-updates,noble-security 1:9.18.39-0ubuntu0.24.04.8 amd64 [upgradable from: 1:9.18.39-0ubuntu0.24.04.3]
bind9-host/noble-updates,noble-security 1:9.18.39-0ubuntu0.24.04.8 amd64 [upgradable from: 1:9.18.39-0ubuntu0.24.04.3]
bind9-libs/noble-updates,noble-security 1:9.18.39-0ubuntu0.24.04.8 amd64 [upgradable from: 1:9.18.39-0ubuntu0.24.04.3]
byobu/noble-updates 6.11-0ubuntu1.1 all [upgradable from: 6.11-0ubuntu1]
ca-certificates/noble-updates,noble-security 20260816ubuntu0.24.04.1 all [upgradable from: 20240203]
```

```text
$ apt policy tree

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

tree:
  Installed: (none)
  Candidate: 2.1.1-2ubuntu3.24.04.2
  Version table:
     2.1.1-2ubuntu3.24.04.2 500
        500 http://id.archive.ubuntu.com/ubuntu noble-updates/universe amd64 Packages
     2.1.1-2ubuntu3 500
        500 http://id.archive.ubuntu.com/ubuntu noble/universe amd64 Packages
```

```text
$ sudo apt install -y tree

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

Reading package lists...
Building dependency tree...
Reading state information...
The following packages were automatically installed and are no longer required:
  libisns0t64 libopeniscsiusr
Use 'sudo apt autoremove' to remove them.
The following NEW packages will be installed:
  tree
0 upgraded, 1 newly installed, 0 to remove and 222 not upgraded.
Need to get 47,4 kB of archives.
After this operation, 111 kB of additional disk space will be used.
Get:1 http://id.archive.ubuntu.com/ubuntu noble-updates/universe amd64 tree amd64 2.1.1-2ubuntu3.24.04.2 [47,4 kB]
Fetched 47,4 kB in 0s (99,2 kB/s)
Selecting previously unselected package tree.
(Sedang membaca basis data ... (Sedang membaca basis data ... 5%(Sedang membaca basis data ... 10%(Sedang membaca basis data ... 15%(Sedang membaca basis data ... 20%(Sedang membaca basis data ... 25%(Sedang membaca basis data ... 30%(Sedang membaca basis data ... 35%(Sedang membaca basis data ... 40%(Sedang membaca basis data ... 45%(Sedang membaca basis data ... 50%(Sedang membaca basis data ... 55%(Sedang membaca basis data ... 60%(Sedang membaca basis data ... 65%(Sedang membaca basis data ... 70%(Sedang membaca basis data ... 75%(Sedang membaca basis data ... 80%(Sedang membaca basis data ... 85%(Sedang membaca basis data ... 90%(Sedang membaca basis data ... 95%(Sedang membaca basis data ... 100%(Sedang membaca basis data ...  132238 berkas atau direktori telah terpasang.)
Preparing to unpack .../tree_2.1.1-2ubuntu3.24.04.2_amd64.deb ...
Unpacking tree (2.1.1-2ubuntu3.24.04.2) ...
Sedang menata tree (2.1.1-2ubuntu3.24.04.2) ... 
Processing triggers for man-db (2.12.0-4build2) ...

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
```

```text
$ dpkg -l | grep -w tree
ii  tree                                  2.1.1-2ubuntu3.24.04.2                  amd64        displays an indented directory tree, in color
```

```text
$ tree --version
tree v2.1.1 © 1996 - 2023 by Steve Baker, Thomas Moore, Francesc Rocher, Florian Sesser, Kyosuke Tokoro
```

```text
$ sudo apt autoremove -y

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

Reading package lists...
Building dependency tree...
Reading state information...
The following packages will be REMOVED:
  libisns0t64 libopeniscsiusr
0 upgraded, 0 newly installed, 2 to remove and 220 not upgraded.
After this operation, 668 kB disk space will be freed.
(Sedang membaca basis data ... (Sedang membaca basis data ... 5%(Sedang membaca basis data ... 10%(Sedang membaca basis data ... 15%(Sedang membaca basis data ... 20%(Sedang membaca basis data ... 25%(Sedang membaca basis data ... 30%(Sedang membaca basis data ... 35%(Sedang membaca basis data ... 40%(Sedang membaca basis data ... 45%(Sedang membaca basis data ... 50%(Sedang membaca basis data ... 55%(Sedang membaca basis data ... 60%(Sedang membaca basis data ... 65%(Sedang membaca basis data ... 70%(Sedang membaca basis data ... 75%(Sedang membaca basis data ... 80%(Sedang membaca basis data ... 85%(Sedang membaca basis data ... 90%(Sedang membaca basis data ... 95%(Sedang membaca basis data ... 100%(Sedang membaca basis data ...  132245 berkas atau direktori telah terpasang.)
Removing libisns0t64:amd64 (0.101-0.3build3) ...
Removing libopeniscsiusr (2.1.9-3ubuntu5.4) ...
Processing triggers for libc-bin (2.39-0ubuntu8.7) ...
```

```text
$ sudo apt clean

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

```

```text
$ df -h /
Filesystem                         Size  Used Avail Use% Mounted on
/dev/mapper/ubuntu--vg-ubuntu--lv  9,8G  6,0G  3,3G  66% /
```

> Catatan: `apt upgrade` sengaja ditunda karena kapasitas disk terbatas.

## 6. Manajemen Direktori dan File

| Perintah | Fungsi |
|---|---|
| `mkdir -p` | Membuat direktori beserta sub-direktori |
| `echo >` / `echo >>` | Membuat/menimpa dan menambah isi file |
| `cat` | Menampilkan isi file |
| `cp` / `mv` / `rm` | Menyalin / memindah atau mengganti nama / menghapus |
| `find` / `grep` / `wc -l` | Mencari file / mencari teks / menghitung baris |

### Hasil

```text
$ mkdir -p ~/latihan-dasar/{data,backup,log}
```

```text
$ echo "Halo Linux Server" > ~/latihan-dasar/data/halo.txt
```

```text
$ echo "Baris kedua" >> ~/latihan-dasar/data/halo.txt
```

```text
$ cat ~/latihan-dasar/data/halo.txt
Halo Linux Server
Baris kedua
```

```text
$ cp ~/latihan-dasar/data/halo.txt ~/latihan-dasar/backup/halo-backup.txt
```

```text
$ mv ~/latihan-dasar/backup/halo-backup.txt ~/latihan-dasar/backup/halo-v1.txt
```

```text
$ tree ~/latihan-dasar
/home/tiansitorus/latihan-dasar
├── backup
│   └── halo-v1.txt
├── data
│   └── halo.txt
└── log

4 directories, 2 files
```

```text
$ find ~/latihan-dasar -name "*.txt"
/home/tiansitorus/latihan-dasar/backup/halo-v1.txt
/home/tiansitorus/latihan-dasar/data/halo.txt
```

```text
$ grep "Linux" ~/latihan-dasar/data/halo.txt
Halo Linux Server
```

```text
$ wc -l ~/latihan-dasar/data/halo.txt
2 /home/tiansitorus/latihan-dasar/data/halo.txt
```

```text
$ rm ~/latihan-dasar/backup/halo-v1.txt
```

```text
$ ls -la ~/latihan-dasar/backup
total 8
drwxrwxr-x 2 tiansitorus tiansitorus 4096 Okt 30 17:44 .
drwxrwxr-x 5 tiansitorus tiansitorus 4096 Okt 30 17:44 ..
```

## 7. Monitoring CPU, RAM, dan Disk

| Perintah | Fungsi |
|---|---|
| `top -bn1` | Snapshot proses, CPU, dan RAM |
| `vmstat 1 5` | Statistik memori, CPU, dan I/O |
| `ps aux --sort=-%mem` | Proses dengan pemakaian RAM terbesar |
| `du -h --max-depth=1 /` | Folder dengan pemakaian disk terbesar |

### Hasil

```text
$ top -bn1 | head -15
top - 17:44:46 up 27 min,  2 users,  load average: 0,22, 0,06, 0,04
Tasks: 230 total,   1 running, 229 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0,0 us,  4,5 sy,  0,0 ni, 95,5 id,  0,0 wa,  0,0 hi,  0,0 si,  0,0 st 
MiB Mem :   1920,4 total,    380,3 free,    839,9 used,    868,5 buff/cache     
MiB Swap:   1902,0 total,   1902,0 free,      0,0 used.   1080,4 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
      1 root      20   0   22708  13872   9568 S   0,0   0,7   0:04.66 systemd
      2 root      20   0       0      0      0 S   0,0   0,0   0:00.02 kthreadd
      3 root      20   0       0      0      0 S   0,0   0,0   0:00.00 pool_wo+
      4 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 kworker+
      5 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 kworker+
      6 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 kworker+
      7 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 kworker+
     10 root       0 -20       0      0      0 I   0,0   0,0   0:00.16 kworker+
```

```text
$ vmstat 1 5
procs -----------memory---------- ---swap-- -----io---- -system-- -------cpu-------
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st gu
 0  0      0 389168  32456 856956    0    0   432   298  315    2  1  1 98  0  0  0
 0  0      0 391644  32456 856996    0    0     0     0  240  454  0  0 100  0  0  0
 0  0      0 396104  32456 856996    0    0     0     0  208  435  0  1 99  0  0  0
 0  0      0 399416  32456 856996    0    0     0     0  228  456  1  0 99  0  0  0
 0  0      0 399416  32456 856996    0    0     0     0  212  439  0  0 100  0  0  0
```

```text
$ ps aux --sort=-%mem | head -6
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
mysql        941  1.1 20.0 1786740 394316 ?      Ssl  17:17   0:19 /usr/sbin/mysqld
root        1090  0.0  4.4 1954568 87632 ?       Ssl  17:17   0:01 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
root         886  0.1  2.2 1824312 43840 ?       Ssl  17:17   0:03 /usr/bin/containerd
root         472  0.0  1.4 354628 27560 ?        SLsl 17:17   0:00 /sbin/multipathd -d -s
root         914  0.0  1.3 223656 27496 ?        Ss   17:17   0:00 /usr/sbin/apache2 -k start
```

```text
$ sudo du -h --max-depth=1 / 2>/dev/null | sort -h | tail -8
100K	/tmp
1,3M	/run
7,5M	/etc
102M	/home
200M	/boot
686M	/var
3,4G	/usr
6,2G	/
```

## 8. Analisis dan Kesimpulan

Server berjalan di Ubuntu 24.04.4 LTS dengan N core CPU dan RAM 2 GB.
Disk root terpakai XX% sehingga apt upgrade ditunda.
Alamat IP server 172.20.10.2. Paket tree berhasil dipasang.
Latihan file (mkdir, cp, mv, find, grep, rm) berjalan lancar.

## 9. Troubleshooting

| Masalah | Penyebab | Solusi |
|---|---|---|
| `git clone` meminta login | Nama repository salah (`administrasi-server-linux`), nama asli `linux-server-administration` | Memakai URL yang disalin dari address bar GitHub |
| `REMOTE HOST IDENTIFICATION HAS CHANGED` saat SSH | Sidik jari lama di `known_hosts` Windows | `ssh-keygen -R 172.20.10.2` lalu sambung ulang |
