### Host reachability
Uma mensagem ICMP Echo  pode ser usada para testar a acessibilidade com outro host em uma rede IP. O host local envia um ICMP Echo Request para outro host , se este host estiver disponível, ele irá responder com um ICMP Echo Reply
### Destination or Service Unreachable
Quando um host ou gateway recebe um pacote que ele não pode enviar, ele usa uma mensagem de ICMP Destination Unreachable para avisar a origem que o destino ou serviço está inacessível.

Alguns dos códigos inacessíveis de destino para ICMPV4 são os seguintes:
- **0** - Net unreachable
- **1** - Host unreachable
- **2** - Protocol unreachable
- **3** - Port unreachable

Alguns dos códigos inacessíveis de destino para ICMPV4 são os seguintes:
- **0** - No route to destination
- **1** - Communication with the destination is administratively prohibited (e.g., firewall)
- **2** - Beyond scope of the source address
- **3** - Address unreachable
- **4** - Port unreachable
### Time exceeded
Uma mensagem ICMPv4 Time Exceeded é usada pelo roteador para indicar que o pacote não pode ser enviado pois o Time To Live (TTL) foi decrementado á 0.

ICMPv6 também envia uma mensagem de Time Exceeded se o roteador não puder enviar o pacote por causa do pacote ter expirado, usando o campo Hop Limit do IPv6.

> Time Exceeded messages are used by the **traceroute** tool.




























































































































