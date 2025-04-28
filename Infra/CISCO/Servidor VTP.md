# Configuração de um servidor VTP

![[redes-infra-images-01.png]]

## Servidor VTP - SW0

`SW0(config)#vtp mode server`
Define como um servidor VTP

`SW0(config)#vtp version 2`
Define a versão do VTP para a versão 2

`SW0#show vtp status`
Mostra as configurações do vtp

---

`SW0(config)#vlan 10`
Cria uma vlan com o id=10

`SW0(config-vlan)#name VLAN10`
Nomeia a vlan para VLAN10

`SW0(config)#vlan 20`
Cria uma vlan com o id=20

`SW0(config-vlan)#name VLAN20`
Nomeia a vlan para VLAN20

---

`SW0(config)#vtp domain vtpserver.com`
Define um domínio para o servidor

---

`SW0(config)#interface range fastEthernet 0/2-4`
Assim podemos configurar um range de interfaces

`SW0(config-if-range)#switchport mode trunk`
Põe as portas do range em modo de trunk

`SW0#sh vlan brief`
Mostra as VLANs

`SW0#sh vlan`
Mostra as VLANs

## Cliente VTP - SW1

`SW1(config)#vtp mode client`
Define como cliente VTP

`SW0#show vtp status`
Mostra as configurações do vtp

`Switch#confvtp domain vtpserver.com`
Define o domínio a ser usado, normalmente já vem automaticamente

## Portas cliente VTP - SW1

`SW1(config)#interface range fastEthernet 0/2-12`
Configura um range de interfaces

`SW1(config-if-range)#switchport mode access`
Coloca a(s) porta(s) escolhida(s) em modo de acesso

`SW1(config-if-range)#switchport access vlan 10`
Define que elas vão ter acesso a vlan 10

















