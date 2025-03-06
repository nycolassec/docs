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
# IPv6
![[Pasted image 20250225083806.png]]

###### Melhorias que o IPv6 fornece em relação ao IPv4:
**Increase address space** - Um endereço IPv6 é baseado em um endereçamento de 128 bits ao contrário do IPv4 com 32 bits.
**Improved packet handling** - O cabeçalho do pacote IPv6 foi simplificado com menos campos
**Eliminates the need for NAT** - Com um largo número de endereços IPv6, NAT não é necessário.

No IPv6 para fazer a distinção de um host interno e externo a rede local, o roteador local anuncia o endereço de rede local (prefix) para todos os dispositivos na rede.





























































































































































































































































