## Zonas
###### Atribuir interfaces as zonas
```sh
$ firewall-cmd --zone=public --change-interface=eth0 --permanent
$ firewall-cmd --zone=internal --change-interface=eth1 --permanent

```
#### Adicionar serviços e portas
```sh
# Adicionar serviços e portas
$ firewall-cmd --zone=public --permanent --add-service=http
$ firewall-cmd --zone=public --permanent --add-service=https
$ firewall-cmd --zone=public --permanent --add-port=9009/tcp

# Remover serviços e portas
$ firewall-cmd --zone=public --permanent --remove-service=ftp
$ firewall-cmd --zone=public --permanent --remove-port=23/tcp
```