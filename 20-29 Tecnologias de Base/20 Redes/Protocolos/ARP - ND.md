# ARP
A tabela ARP mapeia endereços IPv4 para endereços MAC. Ela é armazenada na Ram, então é perdida no boot da máquina.

Mensagens ARP são encapsuladas diretamente em um quadro Ethernet usando os seguintes campos de cabeçalho:
###### ARP request
**Destination MAC address** - É o endereço de broadcast FF-FF-FF-FF-FF-FF, exigindo que todas as NIC Ethernet aceitem e processem o ARP.
**Source MAC address** - Este é o endereço MAC do remetente da mensagem de ARP request.
**Type** - Mensagens ARP tem um campo e tipo com 0x806. Isto informa a NIC que a porção de dados do frame precisa passar pelo processo de ARP.

###### ARP reply
**Destination MAC address** -Este é o endereço MAC do remetente da mensagem de ARP request.
**Source MAC address** - Este é o endereço MAC do remetente da mensagem de ARP reply.
**Type** - Mensagens ARP tem um campo e tipo com 0x806. Isto informa a NIC que a porção de dados do frame precisa passar pelo processo de ARP.

As entradas na tabela ARP tem carimbos de data e hora. Se o host não receber nenhum frame de algum dispositivo antes do carimbo de data e hora expirar, a entrada para este dispositivo é removida.
***
# ND
O IPv6 Neighbor Discovery protocol é referenciado tanto como ND  como NDP. O ND provê resolução de endereço, descobrimento de roteador, e serviços de redirecionamento para IPv6 usando ICMPv6.

O ICMPv6 ND usa 5 mensagens para disponibilizar esses serviços:
- Neighbor Solicitation messages
- Neighbor Advertisement messages
- Router Solicitation messages
- Router Advertisement messages
- Redirect Message

Neighbor Solicitation e Neighbor Advertisement são utilizados de dispositivo para dispositivo como a resolução de endereço. Esses dispositivos pode ser computadores e roteadores.

Router Solicitation e Router Advertisement são utilizados para mensagens entre dispositivos e roteadores. Normalmente router discovery é usado para alocação dinâmica de endereços e Stateless Address Autoconfiguration (SLAAC).

Redirect Message é usado para uma melhor escolha do next-hop.






































