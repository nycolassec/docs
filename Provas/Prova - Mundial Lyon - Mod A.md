# Firewall
### Aliases
Teremos 4 aliases para configurarmos as regras.
####  DMZ_ports
==Properties==
**Name :** DMZ_ports
**Type :** Port(s)
==Port(s)==
![[Pasted image 20250605184611.png]]
#### DMZ_to_SERVERS
==Properties==
**Name :** DMZ_to_SERVERS
**Type :** Port(s)
==Port(s)==
![[Pasted image 20250605190543.png]]
#### Server_Ports
==Properties==
**Name :** Server_Ports
**Type :** Port(s)
==Port(s)==
![[Pasted image 20250605191816.png]]
#### WAN_ports
==Properties==
**Name :** WAN_ports
**Type :** Port(s)
==Port(s)==
![[Pasted image 20250605192055.png]]
### Rules
Esse será o padrão em que as regras estarão descritas.
![[Pasted image 20250605163245.png]]

$$1°\ regra$$
![[Pasted image 20250605163306.png]]
![[Pasted image 20250605164138.png]]

$$2°\ regra\ regra$$
![[Pasted image 20250605164336.png]]
![[Pasted image 20250605164532.png]]

$$3°\ regra\ regra$$
![[Pasted image 20250605164842.png]]
![[Pasted image 20250605174653.png]]

$$4°\ regra\ regra$$
![[Pasted image 20250605174827.png]]
![[Pasted image 20250605174811.png]]

$$5°\ regra\ regra$$
![[Pasted image 20250605174932.png]]
![[Pasted image 20250605181256.png]]

$$6°\ regra\ regra$$
![[Pasted image 20250605174948.png]]
![[Pasted image 20250605175240.png]]
***
# LINSRV1
- [ ] **Join LinSRV1 to Lumiere.com domain.**
```sh
# Verifique se há conexão entre os hosts
$ realm discover lumiere.com

# Ingresse no domínio com um usuário
$ realm join lumiere.com -U administrator

# Verifique se é possível obter informações de um usuário
$ id administrator@lumiere.com
```

- [ ] **All other domain machines should be able to resolve the name to IP address of LINSRV1**
Devemos adicionar uma entrada para `linsrv1` no `dns` do domínio.
### SSH access
- [ ] **access allowed for domain users C1 and C2 via ssh**
Permita o login dos usuários com o realm
```sh
$ realm permit c1@lumiere.com
$ realm permit c1@lumiere.com
```

Agora tiramos a necessidade de logar com o `FQDN` mas só com o `cn`.
```sh
$ vim /etc/sssd/sssd.conf
use_fully_qualified_names = False

$ systemctl restart sshd
```

E teste a conexão `ssh` do Windows para o Linux.
```sh
$ ssh c1@lumiere.com@linsrv1
```

- [ ] **Both C1 and C2 users should be able to use all root level administrative commands through the use of the sudo commands.**
Digite `visudo` para editar o arquivo de `sudoers`, e adicione as linhas :
```sh
$ visudo
c1      ALL=ALL(ALL)    ALL
c2      ALL=ALL(ALL)    ALL
```

- [ ] **Other users which connect will not have elevated sudo privileges.**
Não precisamos modificar, pois por padrão o acesso ao sudo é negado.
### Local firewall setup
- [ ] **Firewall should be running**
```sh
$ systemctl start firewalld
```

- [ ] **Services for required operation permanently added to the firewall to be operational after a reboot**
Após a configuração do firewall, definimos a configuração como permanente
```sh
$ firewall-cmd --runtime-to-permanent
$ firewall-cmd --reload
```
### SSH configurations
Defina as configurações no arquivo `/etc/ssh/sshd_config`.
- [ ] **Have ssh listen on port #2022**
```sh
$ vim /etc/ssh/sshd_config
Port 2022

$ systemctl restart sshd
```

- [ ] **Root not allowed to connect via ssh**
```sh
$ vim /etc/ssh/sshd_config
PermitRootLogin no
```

- [ ] **Only C1 and C2 users can connect via ssh**
```sh
$ vim /etc/ssh/sshd_config
AllowUsers  c1 c2

$ sustemctl restart sshd
```
### Set the passwords for any locally created Linux users to match that in the domain
As políticas de senha devem ser definidas em `/etc/security/pwquality.conf` e `/etc/login.defs`

- [ ] **10 characters in length**
```sh
$ vim /etc/security/pwquality.conf
minlen = 10
```

- [ ] **Password changed every month**
```sh
$ vim /etc/login.defs
PASS_MAX_DAYS   30
```

- [ ] **Minimum password length of 25 days**
```sh
$ vim /etc/login.defs
PASS_MIN_DAYS   25
```

- [ ] **Warn a user their password will expire after 25 days**
```sh
$ vim /etc/login.defs
PASS_WARN_AGE   25
```

- [ ] **Password must have one lower case, upper one case, and one numerical digit**
```sh
$ vim /etc/security/pwquality.conf
dcredit = -1
ucredit = -1
lcredit = -1
```

- [ ] **Set this site up to use https:// preferably using a certificate signed by WinSRV3, but if you cannot get this to work, use a self-signed certificate.**
Crie um certificado auto assinado.
```sh
$ openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/pki/tls/private/lum.key \
-out /etc/pki/tls/certs/lum.crt
```

Agora podemos criar uma configuração para o nosso site.
```sh
$ vim /etc/httpd/conf.d/lum_ssl.conf
<VirtualHost *:443>
    ServerName lum.lumiere.com

    DocumentRoot "/var/www/lum"

    SSLEngine on
    SSLCertificateFile /etc/pki/tls/certs/lum.crt
    SSLCertificateKeyFile /etc/pki/tls/private/lum.key

    ErrorLog logs/lum-error_log
    CustomLog logs/lum-access_log combined
</VirtualHost>
```

- [ ] **enable SELinux but make certain the htttpd service is still fully operational.**
```sh
$ setsebool -P httpd_can_network_connect on
$ restorecon -Rv /var/www/lum
$ systemctl enable httpd
$ systemctl start httpd
```