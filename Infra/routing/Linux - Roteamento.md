#### Habilitando o IP Forwarding
```sh
# Habilitamos de forma permanente, mas sprecisamos reiniciar
$ vim /etc/sysctl.conf
net.ipv4.ip_forward = 1

# Habilitamos de forma temporária (até a reinicialização)
$ sysctl -w net.ipv4.ip_forward=1
```
#### Regras NAT
```sh
# Adicionamos a interface a zona
$ firewall-cmd --zone=trusted  --add-interface=eth1 --permanent

# Configuramos a interface externa
$ firewall-cmd --zone=external --add-interface=eth0 --permanent
$ firewall-cmd --zone=external --add-forward --permanent
$ firewall-cmd --zone=external --add-masquerade --permanent
$ firewall-cmd --reload
```
#### Verificando as configurações
```sh
# Ver interfaces por zona
$ firewall-cmd --get-active-zones

# Ver masquerade ativo
$ firewall-cmd --zone=external --query-masquerade
```
#### Outras zonas
Se quisermos que outras zonas também consigam ter acesso a esse roteamento, precisamos criar uma política.
```sh
# Adicionamos a interface a zona interna
$ firewall-cmd --zone=internal  --add-interface=eth1 --permanent

# Configuramos a política para permitir a passagem do tráfego
$ firewall-cmd --permanent --new-policy int-to-ext
$ firewall-cmd --permanent --policy int-to-ext --add-ingress-zone=internal
$ firewall-cmd --permanent --policy int-to-ext --add-egress-zone=external
$ firewall-cmd --permanent --policy int-to-ext --set-target=ACCEPT
$ firewall-cmd --reload
```
***
$$\#54 : Nycolas\ Ramos$$