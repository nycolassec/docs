## Topologia
![[redes-infra-images-02.png]]

## Configuração inicial
Primeiro temos de configurar um IP no equipamento a ser acessado.

### Router
```IOS
Router# configure terminal
Router(config)# hostname R0
R0(config)# interface gigabitEthernet 0/0

R0(config-if)# no shutdown
R0(config-if)# ip address 192.168.0.1 255.255.255.0
R0(config-if)# end

R0# write
//ou
R0# copy running-config startup-config
```

### SWITCH
```IOS
Switch# configure terminal
Switch(config)# hostname SW0
SW0(config)# interface vlan 1

SW0(config-if)# no ip address dhcp
SW0(config-if)# no ip address
SW0(config-if)# ip address 192.168.0.10 255.255.255.0
SW0(config-if)# no shutdown
SW0(config-if)# exit

SW0(config)# ip default-gateway 192.168.0.1
SW0(config)# end

SW0# write
//ou
SW0# copy running-config startup-config
```


## Configuração SSH
```IOS
IOS# configure terminal

IOS(config)# ip domain-name ios.com
IOS(config)# crypto key generate rsa
IOS(config)# username cisco priv 15 secret cisco
IOS(config)# line vty 0 4

IOS(config-line)# login local
IOS(config-line)# transport input ssh
IOS(config-line)# end

IOS# write
//ou
IOS# copy running-config startup-config
```


## Acesso SSH

### PC
```PC
C:/> ssh cisco@192.168.0.1
Password: cisco

R0# 
```

---
## Referências

**`CISCO`** : https://www.cisco.com/c/pt_br/support/docs/ios-nx-os-software/ios-software-releases-122-mainline/46741-backup-config.html