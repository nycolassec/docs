#### Pacotes a instalar
```sh
$ dnf install sssd realmd oddjob oddjob-mkhomedir
```
#### Ingressar
Primeiro verificamos se o host tem comunicação com o domínio.
```sh
$ realm discover wsc.local
```

Caso identificado com sucesso poderemos ingressar no AD
```sh
# Podemos especificar o nome de usuário que irá ser utilizado para ingressar a máquina no domínio
$ realm join wsc.local -U AdUser

# Também podemos especificar a OU em que ele irá ingressar
$ realm join wsc.local -U AdUser --computer-ou="OU=LinuxComputers,DC=wsc,DC=local"
```
#### Usuários
Podemos permitir ou negar que usuários e grupos façam logon nessa máquina.
```sh
# Verificar se conseguimos consultar os usuários
$ id administrator@wsc.local

# Negar e permitir os usuários
$ realm deny --all
$ realm permit AdUser@wsc.local
$ realm permit --groups Users@wsc.local
```

Agora tiramos a necessidade de logar com o `FQDN` mas só com o `cn`.
```sh
$ vim /etc/sssd/sssd.conf
use_fully_qualified_names = False

$ systemctl restart sshd
```
#### Somente usuários do AD no SSH
```sh
$ vim /etc/ssh/sshd_config
AllowUsers AdUser Administrator
```
#### Permissão de SUDO
Digite `visudo` para editar o arquivo de `sudoers`, e adicione as linhas :
```sh
$ visudo
AdUser      ALL=ALL(ALL)    ALL
```