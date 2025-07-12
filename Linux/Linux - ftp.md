#### Instalação
```sh
$ dnf install vsftpd -y
```
#### Configurações
Ele utilizará a porta `21`, diferente do `sftp` que utiliza a `22`.
```sh
$ vim /etc/vsftpd/vsftpd.conf
# TLS
ssl_enable=YES
rsa_cert_file=/etc/vsftpd/ssl/ftp.wsc.local.cert.pem
rsa_private_key_file=/etc/vsftpd/ssl/ftp.wsc.local.key.pem

force_local_data_ssl=YES
force_local_logins_ssl=YES
ssl_tlsv1_2=YES
ssl_tlsv1_3=YES
ssl_sslv2=NO
ssl_sslv3=NO
require_ssl_reuse=NO
ssl_ciphers=HIGH

# Segurança adicional
allow_anon_ssl=NO
anonymous_enable=NO

# Portas passivas
pasv_enable=YES
pasv_min_port=10000
pasv_max_port=10100

# Chroot
chroot_local_user=YES
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd/chroot_list

# Evita problemas com uploads
seccomp_sandbox=NO

# Banner opcional
ftpd_banner=Servidor FTP Seguro com TLS

# ===== =====

# Porta padrão
listen=YES
listen_ipv6=NO

# Permite login local
local_enable=YES
write_enable=YES

local_umask=022
ls_recurse_enable=YES

# Diretório base por usuário
user_sub_token=$USER
local_root=/ftp/$USER
allow_writeable_chroot=YES

dirmessage_enable=YES
xferlog_enable=YES
connect_from_port_20=YES
xferlog_std_format=YES

# User list
userlist_enable=YES
userlist_file=/etc/vsftpd/user_list
userlist_deny=YES

# Log
debug_ssl=YES
log_ftp_protocol=YES

pam_service_name=vsftpd
```
#### Firewall
```sh
sudo firewall-cmd --permanent --add-port=21/tcp
sudo firewall-cmd --permanent --add-port=10000-10100/tcp
sudo firewall-cmd --reloadd
```
#### SELinux
```sh
$ setsebool -P ftpd_full_access on
```

>[!warning] Diretórios
>Os diretórios definidos em  `local_root` devem ser criados manualmente, pois p vsFTP não consegue cria-los.
#### Client
```sh
$ dnf install -y lftp
$ lftp -u admin ftp://10.0.10.1
```
***
$$\#54 : Nycolas Ramos$$