
```
(config) #ip dhcp pool Rede_1-1
```
Define um "pool" de IP's como o nome de Rede_1-1, e entra no modo de configuração desse "pool"

```
(dhcp-config) #network 192.168.0.0 255.255.255.0
```
Aqui atribuímos o ip privado e a máscara, para que o serviço DHCP saiba os ip's que ele pode atribuir.

```
(dhcp-config) #default-router 192.168.0.1
```
Aqui especificamos qual o roteador padrão.

```
(dhcp-config) # dns-server 192.168.0.2
```
Agora o DHCP também vai enviar o servidor DNS

```
(config) #ip dhcp excluded-address 192.168.0.1
```
Assim dizemos para ele não distribuir esse ip na rede








