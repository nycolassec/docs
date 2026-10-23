O IP foi definido como um protocolo com pouca sobrecarga provendo somente funções necessárias para fazer a entrega do pacote de uma origem até um destino. Ele não foi projetado para fazer o rastreamento do fluxo de pacotes.

As características básicas do IP são:
- **Connectionless** - Não há conexão estabelecida antes do envio dos pacotes.
- **Best Effort** - O IP é inerentemente não confiável, pois não garante a entrega de pacotes.
- **Media Independent** - A operação não depende da mídia que carrega os dados.
#### Resolução de endereços
Enquanto o IPv4 usa o ARP para resolver o endereço MAC do dispositivo de destino, o IPv6 usa o ICMPv6 Neighbor Discovery
#### Route table
Um roteador pode aprender as redes remotas de duas formas:
**Manually** - Rotas remotas são inseridas manualmente na tabela de rotas usando rotas estáticas.
**Dynamically** - Rotas remotas são automaticamente aprendidas usando protocolos de roteamento dinâmico.

A tabela de rotas guarda 3 tipos de rotas:
**directly-connected networks** - Essas redes estão ativas nas interfaces do roteador.
**Remote networks** - Essas entradas de rotas de rede estão conectados em outros roteadores.
**Default route** - A rota padrão é usada quando não há melhor correspondência na tabela de roteamento.
# IPv4
![[Pasted image 20250306083825.png]]

Em alguns casos UM dispositivo intermediário deve dividir um pacote IPv4 quando está enviando de um meio  pata outro meio com um MTU menor. Isso é chamado de fragmentação. Pacotes IPv6 não podem ser fragmentados por um roteador.
###### Limitações do IPv4
**IPv4 address depletion** - IPv4 tem um número limitado de endereços disponíveis. Embora haja aproximadamente 4 bilhões de endereços IPv4 o aumento exponencial de dispositivos aumenta a necessidade de termos mais endereços.
**Lack of end-to-end connectivity** - O NAT provê um caminho para que múltiplos dispositivos usem um mesmo endereço IPv4. Por causa disso o endereço interno é desconhecido. Isso pode ser problemático para as tecnologias que requerem uma conexão end-to-end.
**Increased network complexity** - O NAT também adiciona uma camada a mais de complexidade, o que aumenta a latência e dificulte o processo de troubleshooting.

Para fazer a distinção de um host interno e externo a rede local, o IPv4 usa a máscara de sub-rede.
****
# Coexistência entre IPv6 e IPv4
A IETF criou várias ferramentas e protocolos para ajudar os administradores de rede a migrarem suas redes para IPv6.
###### Dual Stack
Permite que IPv4 e IPv6 coexistam no mesmo segmento de rede. Dispositivos Dual Stack executam IPv4 e IPv6 simultaneamente.
###### Tunelamento
é um método de transporta um pacote IPv6 sobre uma rede IPv4. O pacote IPv6 é encapsulado em um pacote IPv4.
###### Tradução
Network Address Translation 64 (NAT64) permite dispositivos habilitados com IPv6 se comunicarem com dispositivos habilitados com IPv4 usando um a tradução similar ao NAT para IPv4. Um pacote IPv6 é traduzido para um pacote IPv4 e vice versa.
****
# IPv6
![[Pasted image 20250225083806.png]]

###### Melhorias que o IPv6 fornece em relação ao IPv4:
**Increase address space** - Um endereço IPv6 é baseado em um endereçamento de 128 bits ao contrário do IPv4 com 32 bits.
**Improved packet handling** - O cabeçalho do pacote IPv6 foi simplificado com menos campos
**Eliminates the need for NAT** - Com um largo número de endereços IPv6, NAT não é necessário.

No IPv6 para fazer a distinção de um host interno e externo a rede local, o roteador local anuncia o endereço de rede local (prefix) para todos os dispositivos na rede.

O ICMPv6 inclui resolução de endereço e autoconfiguração de endereço.
#### Unicast, Multicast, Anycast
Como no IPv4 temos 3 tipos de endereço:
**Unicast** - Um endereço IPv6 unicast identifica exclusivamente uma interface em um dispositivo com IPv6 habilitado.
**Multicast** - Um endereço IPv6 multicast é usado para enviar um único pacote IPv6 á vários hosts.
**Anycast** - Um endereço de Anycast IPv6 é qualquer endereço unicast IPv6 que possa ser atribuído a vários dispositivos. Um pacote enviado para um endereço Anycast é roteado para o dispositivo mais próximo, com esse endereço.

Diferente do IPv4, o IPv6 não tem um endereço de broadcast, No entanto, há um endereço IPv6 all-nodes multicast que tem o mesmo resultado.

É fortemente recomendado que usem um interface ID de 64 bits pois o stateless address autoconfiguration (SLAAC) usa 64 bits para o interface ID.
#### Tipos de Endereços IPv6 Unicast

Diferente dos dispositivos IPv4 que tem apenas um endereço, endereços IPv6 tem normalmente dois endereços unicast:
**Global Unicast Address (GUA)** - Ele é unicamente global e roteável na internet.
**Link-local Address (LLA)** - É requerido por todos dispositivo habilitados para IPv6. LLAs são usadas para comunicar com outros dispositivos no mesmo link local. Com IPV6, o termo link significa subnet. LLAs são confinadas em um mesmo link local. Roteadores não irão enviar pacotes com um endereço LLA na origem ou no destino.
**Unique local addresses (range fc00::/7 to fdff::/7)** ainda não foram implementados. No entanto, esses endereços podem eventualmente ser usados para endereçar dispositivos que não precisam ser acessados por redes externas, como impressoras e servidores externos.

## IPv6 GUA
Atualmente apenas os endereços com os três primeiros bits com 001 ou 2000::/3 estão sendo atribuídos.

| Bin                 | Hex  |
| ------------------- | ---- |
| 0010 0000 0000 0000 | 2000 |
| 0011 1111 1111 1111 | 3fff |
Esse endereço tem 3 partes:
- Global Routing Prefix
- Subnet ID
- Interface ID

![[Pasted image 20250310112316.png]]
### Estrutura
**Global Routing Prefix** é a porção de prefixo ou network que é atribuída pelo provedor. é comum para ISPs atribuir um prefixo de roteamento global de /48 para seus clientes. Por exemplo o endereço IPv6 2001:db8:acad::/48 tem um prefixo de roteamento global que indica que os primeiros 48 bits (3 hextets) é como o ISP conhece esse prefixo. O tamanho do prefixo de roteamento global determina o tamanho do ID da sub-rede.

**Subnet ID** é a área entre o Global Routing Prefix e o Interface ID. Diferente do IPv4 onde precisamos emprestar bits da porção de hosts para criar sub-redes o IPv6 foi projetado com dub-redes em mente. A Sub Net ID é usada pelas organizações para identificar subnets em seus sites.

>Muitas organizações estão recebendo prefixo de roteamento global /32. Usando o prefixo recomendado /64 para criar um ID de interface de 64 bits, deixa um ID de sub-rede de 32 bits.

**Interface ID** é equivalente a porção dos hosts no IPv4. Esse termo é usado pois um host pode ter múltiplas interfaces, cada uma com um ou mais Interfaces ID.














































































































































































































































































