#### Instalação
```sh
$ dnf install vsftpd -y
$ vim /etc/vsftpd/vsftpd.conf
```

Ele utilizará a porta `21`, diferente do `sftp` que utiliza a `22`. Quando utilizamos certificados a nomenclatura passa de `ftp` para `ftps`. Após uma diretiva não deve haver outro caractere, os comentários colocados após as diretivas na mesma linha são somente para explicação e não devem ser replicados.
#### Configurações
###### Mínimo funcional
```sh
# Bloquear login anônimo
anonymous_enable=NO
allow_anon_ssl=NO

# Login com usuários locais
local_enable=YES
write_enable=YES

# Qual versão do IP será escutado pelo serviço
listen=YES
listen_ipv6=NO

pam_service_name=vsftpd
```
###### TLS
```sh
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
```
###### Ports
As portas definidas nessa configuração devem ser liberadas no firewall.
`connect_from_port_20=YES` Ativa o modo ativo, assim o servidor se conecta de volta pela porta 20, pode ocasionar problemas com o firewall do client.
```shell
pasv_enable=YES
pasv_min_port=10000
pasv_max_port=10100
# connect_from_port_20=YES
```
###### CHROOT no usuários
O chroot prende os usuários em uma pasta e define como a pasta root daquele usuário, sendo a pasta padrão a home do usuário em questão. Se fizermos isso e o `write_enable=YES` estiver definido, devemos permitir a escrita no chroot, pois ele bloqueia por padrão. Caso o `chroot_list_enable=YES` for definido, os usuários na lista serão uma exceção do **chroot**, se estiver como `NO` todos os usuários serão **chroot**.
```shell
chroot_local_user=YES
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd/chroot_list
allow_writeable_chroot=YES   # Assim permitimos que o usuário escreva no root.
```
###### Lista de usuários
Aqui definimos se será usado para uma lista de usuários para validar o login, caso o `userlist_deny` seja `YES` qualquer usuário na lista será bloqueado, caso seja `NO` qualquer usuário que não estiver na lista será bloqueado.
```shell
userlist_enable=YES
userlist_file=/etc/vsftpd/user_list
userlist_deny=YES
```
###### Diretório base por usuários
Aqui definimos um token que será utilizado par representar o nome do usuário, e alteramos o local `root` dos usuários.
```sh
user_sub_token=$USER     # Definir a nomeclatura do token
local_root=/ftp/$USER    # O caminho da pasta
```
###### Messages
`dirmessage_enable=YES ` procura por arquivos `.message` para mostrar uma mensagem quando o usuário entrar em uma pasta.
```shell
ftpd_banner=Servidor FTP Seguro com TLS # Banner opcional
dirmessage_enable=YES
```
###### Permissions
- **Cálculo:** `666 - umask` para arquivos, `777 - umask` para diretórios
- **Exemplo:** Com ``umask`` 022 → Arquivos: 644 (`rw-r--r--`), Diretórios: 755 (`rwxr-xr-x`)
- **Valor comum:** 022 (leitura pública, escrita apenas do dono)
```shell
local_umask=022
ls_recurse_enable=YES # Habilita o ls -R
```
###### Logs
```shell
debug_ssl=YES
log_ftp_protocol=YES

xferlog_enable=YES
xferlog_std_format=YES
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
$ lftp -u admin ftps://10.0.10.1
```
***
$$\#54 : Nycolas Ramos$$