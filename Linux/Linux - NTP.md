### Server
O Server está com o IP `10.0.10.1`.
#### Instalação
```sh
$ dnf install -y chrony
$ systemctl enable --now chronyd
```
#### Configuração
Crie ou modifique as linhas a seguir
```sh
$ vim /etc/chrony.conf
pool 2.centos.pool.ntp.org iburst maxsources 1
# ou
server 127.0.0.1 iburst

allow 10.0.10.0/24
local stratum 3
```
`pool` e `server` não podem ser usados juntos.

>[!warning] DNS
>o hostname `2.centos.pool.ntp.org` tem que ser resolvido caso não haja acesso a internet
#### Firewall
```sh
$ firewall-cmd --add-service=ntp --permanent
$ firewall-cmd --reload
```
#### Sincronização
```sh
$ chronyc -a makestep
$ chronyc tracking
$ chronyc sources

$ timedatectl set-timezone UTC
```
***
### Client
O Client está com o IP `10.0.10.100`.
Configurar a sincronização de horário contra o servidor NTP: pool 2.centos.pool.ntp.org com sincronização rápida.
#### Instalação
```sh
$ dnf install -y chrony
$ systemctl enable --now chronyd
```
#### Configuração
```sh
$ vim /etc/chrony.conf
pool 2.centos.pool.ntp.org iburst maxsources 1
# ou
server 10.0.10.1 iburst
```
`pool` e `server` não podem ser usados juntos.

>[!warning] DNS
>o hostname `2.centos.pool.ntp.org` tem que ser resolvido caso não haja acesso a internet
#### Firewall
```sh
$ firewall-cmd --add-service=ntp --permanent
$ firewall-cmd --reload
```
#### Sincronizando
```sh
$ chronyc -a makestep
$ chronyc tracking
$ chronyc sources
```
***
$$\#54 : Nycolas Ramos$$
