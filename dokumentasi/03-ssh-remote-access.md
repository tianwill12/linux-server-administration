# 03 - SSH Remote Access

## 1. Tujuan

Mengonfigurasi OpenSSH Server, akses remote dari PowerShell Windows, autentikasi SSH key, dan pengamanan akses SSH.

## 2. Status dan Konfigurasi SSH

```text
$ sudo systemctl status ssh --no-pager | head -12
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/usr/lib/systemd/system/ssh.service; enabled; preset: enabled)
     Active: active (running) since Fri 2026-10-30 18:03:23 UTC; 45s ago
TriggeredBy: ● ssh.socket
       Docs: man:sshd(8)
             man:sshd_config(5)
    Process: 4564 ExecStartPre=/usr/sbin/sshd -t (code=exited, status=0/SUCCESS)
   Main PID: 4565 (sshd)
      Tasks: 1 (limit: 2206)
     Memory: 1.2M (peak: 3.5M)
        CPU: 102ms
     CGroup: /system.slice/ssh.service
```

```text
$ ss -tlnp | grep :22
LISTEN 0      4096         0.0.0.0:22         0.0.0.0:*          
LISTEN 0      4096            [::]:22            [::]:*          
```

```text
$ ls -la ~/.ssh
total 24
drwx------ 2 tiansitorus tiansitorus 4096 Okt 30 18:02 .
drwxr-x--- 9 tiansitorus tiansitorus 4096 Okt 30 18:03 ..
-rw------- 1 tiansitorus tiansitorus    0 Apr  2  2026 authorized_keys
-rw------- 1 tiansitorus tiansitorus  399 Okt 30 18:01 id_ed25519
-rw-r--r-- 1 tiansitorus tiansitorus   94 Okt 30 18:01 id_ed25519.pub
-rw------- 1 tiansitorus tiansitorus  978 Okt 30 18:02 known_hosts
-rw-r--r-- 1 tiansitorus tiansitorus  142 Okt 30 18:01 known_hosts.old
```

```text
$ cat /etc/ssh/sshd_config.d/01-hardening.conf
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
MaxAuthTries 3
X11Forwarding no
```

```text
$ sudo sshd -T | grep -E 'permitrootlogin|passwordauthentication|pubkeyauthentication|maxauthtries|x11forwarding'
maxauthtries 3
permitrootlogin no
pubkeyauthentication yes
passwordauthentication no
x11forwarding no
```

## 3. Langkah dari Windows (PowerShell)

```powershell
ssh-keygen -t ed25519 -C "tian-windows"
type $env:USERPROFILE\.ssh\id_ed25519.pub | ssh tiansitorus@172.20.10.2 "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
ssh tiansitorus@172.20.10.2
ssh -o PubkeyAuthentication=no tiansitorus@172.20.10.2
```

Hasil: login dengan key berhasil, login tanpa key ditolak (`Permission denied (publickey)`).

## 4. Pengamanan yang Diterapkan

| Pengaturan | Nilai | Alasan |
|---|---|---|
| PermitRootLogin | no | Mencegah login langsung sebagai root |
| PasswordAuthentication | no | Mencegah serangan tebak password |
| MaxAuthTries | 3 | Membatasi percobaan login |
| X11Forwarding | no | Mengurangi permukaan serangan |

## 5. Analisis dan Kesimpulan

Pengasihan dan konfigurasi pengamanan SSH berjalan sesuai harapan. Pengujian membuktikan bahwa otentikasi berbasis SSH Key berhasil dilakukan, sedangkan akses tanpa SSH Key langsung ditolak dengan pesan `Permission denied (publickey)`. 

Konfigurasi pengamanan seperti `PermitRootLogin no`, `PasswordAuthentication no`, pembatasan `MaxAuthTries 3`, serta penutupan `X11Forwarding no` berhasil memperkecil permukaan serangan (attack surface) serta meminimalkan risiko serangan brute-force maupun akses tak berizin sebagai superuser (root). Dapat disimpulkan bahwa server telah berhasil di-hardening dan siap digunakan untuk akses remote yang lebih aman.
## 6. Troubleshooting

| Masalah | Penyebab | Solusi |
|---|---|---|
| `REMOTE HOST IDENTIFICATION HAS CHANGED` | Sidik jari lama di `known_hosts` | `ssh-keygen -R 172.20.10.2` |
| Password masih diterima setelah hardening | File bawaan terbaca lebih dulu | Beri nama file `01-hardening.conf` |
| Terkunci dari SSH | Key belum terpasang | Hapus file hardening lewat konsol VMware |
