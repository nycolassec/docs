#### Habilitando o IP Forwarding
```sh
# Habilitamos de forma permanente, mas sprecisamos reiniciar
$ vim /etc/sysctl.conf
net.ipv4.ip_forward = 1

# Aplicar as mudanças
$ sysctl -p

# Habilitamos de forma temporária (até a reinicialização)
$ sysctl -w net.ipv4.ip_forward=1
```
#### Regras NAT
Primeiro adicionamos a interface da rede interna a zona `trusted`.
```sh
$ firewall-cmd --zone=public  --change-interface=eth1 --permanent
```

Agora adicionamos a interface externa a zona `external`.
```sh
# Adicionamos a zona
$ firewall-cmd --zone=external --change-interface=eth0 --permanent

# Adicionamos o forward
$ firewall-cmd --zone=external --add-forward --permanent

# Adicionamos o masquerade para nat, costuma ja vir habilitado
$ firewall-cmd --zone=external --add-masquerade --permanent

# Recarregamos as políticas
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
$ firewall-cmd --zone=internal  --change-interface=eth1 --permanent

# Configuramos a política para permitir a passagem do tráfego
$ firewall-cmd --permanent --new-policy int-to-ext
$ firewall-cmd --permanent --policy int-to-ext --add-ingress-zone=internal
$ firewall-cmd --permanent --policy int-to-ext --add-egress-zone=external
$ firewall-cmd --permanent --policy int-to-ext --set-target=ACCEPT
$ firewall-cmd --reload
```
***
$$\#54 : Nycolas\ Ramos$$
