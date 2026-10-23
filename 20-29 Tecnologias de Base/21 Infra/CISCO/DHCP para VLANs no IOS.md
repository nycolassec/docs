## Roteador 
- Temos que criar sub interfaces para direcionar para cada VLAN
- Também temos de encapsula-las e indicar para qual VLAN cada uma será usada
- A porta do switch deve estar como trunk para que mostre ao roteador as VLANS

## Criando sub interfaces
```
Router(config)#interface gigabitEthernet 0/0/0
Router(config-if)#no shutdown
```
Primeiro selecionamos a interface e ligamos ela

`Router(config)#interface gigabitEthernet 0/0/0.1`
Assim criamos e selecionamos uma sub-interface

`Router(config-subif)#encapsulation dot1Q 10`
Assim encapsulamos e indicamos a vlan de id 10

`Router(config-subif)#ip address 172.10.0.1 255.255.255.0`
Agora atribuímos um ip para a nossa sub interface