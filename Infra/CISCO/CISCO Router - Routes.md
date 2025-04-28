# Static Route
![[Pasted image 20250424191647.png]]
**Estrutura** : `ip route <destination_network> <subnet_mask> <next hope>`

Aqui queremos alcançar a rede `192.168.2.0/24` e a interface do do próximo salto tem o `IP` `192.168.10.2`.
```ios
RT-8> en
RT-8# conf t

RT-8(config)# ip route 192.168.2.0 255.255.255.0 192.168.10.2
RT-8(config)# do wr
```

Aqui queremos alcançar a rede `192.168.1.0/24` e a interface do do próximo salto tem o `IP` `192.168.10.1`.
```ios
RT-9> en
RT-9# conf t

RT-8(config)# ip route 192.168.1.0 255.255.255.0 192.168.10.1
RT-8(config)# do wr
```
