# 04 - Firewall & Network Security

## 1. Tujuan

Mengonfigurasi UFW, mengatur port yang diizinkan, memeriksa koneksi jaringan, dan menguji keamanan dasar.

## 2. Konfigurasi UFW

| Perintah | Fungsi |
|---|---|
| `ufw allow OpenSSH` | Mengizinkan SSH (port 22) |
| `ufw default deny incoming` | Menolak semua koneksi masuk secara default |
| `ufw default allow outgoing` | Mengizinkan koneksi keluar |
| `ufw allow 80/tcp` | Mengizinkan HTTP untuk Nginx |
| `ufw --force enable` | Mengaktifkan firewall |

```text
$ sudo ufw allow OpenSSH
Skipping adding existing rule
Skipping adding existing rule (v6)
```

```text
$ sudo ufw default deny incoming
Default incoming policy changed to 'deny'
(be sure to update your rules accordingly)
```

```text
$ sudo ufw default allow outgoing
Default outgoing policy changed to 'allow'
(be sure to update your rules accordingly)
```

```text
$ sudo ufw allow 80/tcp
Rules updated
Rules updated (v6)
```

```text
$ sudo ufw --force enable
Firewall is active and enabled on system startup
```

```text
$ sudo ufw status verbose
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
80,443/tcp (Apache Full)   ALLOW IN    Anywhere                  
80,443/tcp (Nginx Full)    ALLOW IN    Anywhere                  
22/tcp (OpenSSH)           ALLOW IN    Anywhere                  
80/tcp                     ALLOW IN    Anywhere                  
80,443/tcp (Apache Full (v6)) ALLOW IN    Anywhere (v6)             
80,443/tcp (Nginx Full (v6)) ALLOW IN    Anywhere (v6)             
22/tcp (OpenSSH (v6))      ALLOW IN    Anywhere (v6)             
80/tcp (v6)                ALLOW IN    Anywhere (v6)             

```

```text
$ sudo ufw status numbered
Status: active

     To                         Action      From
     --                         ------      ----
[ 1] Apache Full                ALLOW IN    Anywhere                  
[ 2] Nginx Full                 ALLOW IN    Anywhere                  
[ 3] OpenSSH                    ALLOW IN    Anywhere                  
[ 4] 80/tcp                     ALLOW IN    Anywhere                  
[ 5] Apache Full (v6)           ALLOW IN    Anywhere (v6)             
[ 6] Nginx Full (v6)            ALLOW IN    Anywhere (v6)             
[ 7] OpenSSH (v6)               ALLOW IN    Anywhere (v6)             
[ 8] 80/tcp (v6)                ALLOW IN    Anywhere (v6)             

```

## 3. Pemeriksaan Jaringan

| Perintah | Fungsi |
|---|---|
| `ip -br address` | Alamat IP interface |
| `ip route` | Tabel routing dan gateway |
| `ss -tulpn` | Port yang sedang listening |
| `ping -c 4` | Menguji konektivitas |

```text
$ ip -br address
lo               UNKNOWN        127.0.0.1/8 ::1/128 
ens33            UP             172.20.10.2/28 metric 100 fe80::20c:29ff:fea2:d79d/64 
dummy0           UNKNOWN        fe80::7cb4:1ff:fef2:bfba/64 
```

```text
$ ip route
default via 172.20.10.1 dev ens33 proto dhcp src 172.20.10.2 metric 100 
172.20.10.0/28 dev ens33 proto kernel scope link src 172.20.10.2 metric 100 
172.20.10.1 dev ens33 proto dhcp scope link src 172.20.10.2 metric 100 
```

```text
$ sudo ss -tulpn
Netid State  Recv-Q Send-Q                    Local Address:Port  Peer Address:PortProcess                                                                                                                                                       
udp   UNCONN 0      0                            127.0.0.54:53         0.0.0.0:*    users:(("systemd-resolve",pid=631,fd=16))                                                                                                                    
udp   UNCONN 0      0                         127.0.0.53%lo:53         0.0.0.0:*    users:(("systemd-resolve",pid=631,fd=14))                                                                                                                    
udp   UNCONN 0      0                     172.20.10.2%ens33:68         0.0.0.0:*    users:(("systemd-network",pid=607,fd=24))                                                                                                                    
udp   UNCONN 0      0      [fe80::20c:29ff:fea2:d79d]%ens33:546           [::]:*    users:(("systemd-network",pid=607,fd=23))                                                                                                                    
tcp   LISTEN 0      151                           127.0.0.1:3306       0.0.0.0:*    users:(("mysqld",pid=941,fd=23))                                                                                                                             
tcp   LISTEN 0      4096                            0.0.0.0:22         0.0.0.0:*    users:(("sshd",pid=4565,fd=3),("systemd",pid=1,fd=120))                                                                                                      
tcp   LISTEN 0      4096                      127.0.0.53%lo:53         0.0.0.0:*    users:(("systemd-resolve",pid=631,fd=15))                                                                                                                    
tcp   LISTEN 0      70                            127.0.0.1:33060      0.0.0.0:*    users:(("mysqld",pid=941,fd=21))                                                                                                                             
tcp   LISTEN 0      4096                         127.0.0.54:53         0.0.0.0:*    users:(("systemd-resolve",pid=631,fd=17))                                                                                                                    
tcp   LISTEN 0      511                                   *:80               *:*    users:(("apache2",pid=944,fd=3),("apache2",pid=939,fd=3),("apache2",pid=938,fd=3),("apache2",pid=937,fd=3),("apache2",pid=936,fd=3),("apache2",pid=914,fd=3))
tcp   LISTEN 0      4096                               [::]:22            [::]:*    users:(("sshd",pid=4565,fd=4),("systemd",pid=1,fd=121))                                                                                                      
```

```text
$ ping -c 4 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=114 time=46.7 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=114 time=107 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=114 time=95.7 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=114 time=442 ms

--- 8.8.8.8 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3003ms
rtt min/avg/max/mdev = 46.735/172.758/441.749/156.939 ms
```

```text
$ resolvectl status | head -12
Global
         Protocols: -LLMNR -mDNS -DNSOverTLS DNSSEC=no/unsupported
  resolv.conf mode: stub

Link 2 (ens33)
    Current Scopes: DNS
         Protocols: +DefaultRoute -LLMNR -mDNS -DNSOverTLS DNSSEC=no/unsupported
Current DNS Server: 172.20.10.1
       DNS Servers: 172.20.10.1 fe80::646d:2fff:fec7:6e64

Link 3 (dummy0)
    Current Scopes: none
```

## 4. Pengujian dari Windows (PowerShell)

```powershell
Test-NetConnection 172.20.10.2 -Port 22
Test-NetConnection 172.20.10.2 -Port 8080
```

HASIL_POWERSHELL

## 5. Analisis dan Kesimpulan

ANALISIS_DI_SINI

## 6. Troubleshooting

| Masalah | Penyebab | Solusi |
|---|---|---|
| Terkunci setelah `ufw enable` | SSH belum diizinkan | Dari konsol VMware: `sudo ufw allow OpenSSH` |
| Port container tetap terbuka | Docker melewati UFW | Batasi publikasi port di Docker |
